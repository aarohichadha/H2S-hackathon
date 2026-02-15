# IGDTUW Student Hub - Design Document

## Architecture Overview

IGDTUW Student Hub follows a hybrid architecture that optimizes for performance and security by separating read and write paths:

### Read Path (Direct to Supabase)
```
Frontend (React) → Supabase Client → PostgreSQL (with RLS)
                 ↓
            Realtime subscriptions for live updates
```

- Direct database reads via Supabase client SDK
- Row Level Security (RLS) enforces access control at database level
- Realtime subscriptions for announcements and opportunities
- React Query caches responses client-side
- Reduces backend load and latency for read-heavy operations

### Write Path (Through Backend)
```
Frontend → Node.js/Express API → Supabase Admin Client → PostgreSQL
         ↓
    JWT validation, business logic, external API calls
```

- All writes go through Express backend for validation
- Backend handles privileged operations (role checks, ownership validation)
- External API integrations (SerpAPI, Gemini) isolated in backend
- Audit logging for sensitive operations
- Rate limiting and request validation

### External Integrations
```
Cron Jobs (Render) → SerpAPI → Gemini API → Supabase
                   ↓
            Contest APIs (LeetCode, Codeforces, etc.)
```

---

## Component Diagram / Module Breakdown

### Frontend Modules

#### 1. Dashboard Module
- **Components**: `WelcomeHeader`, `StatCard`, `QuickActions`, `ActivityFeed`
- **Responsibilities**: Aggregate view of CGPA, saved opportunities, upcoming contests, recent announcements
- **Data Sources**: Supabase (academics, saved_opportunities, announcements, contests)
- **Caching**: React Query with 5-minute stale time

#### 2. Calgo (Contest Calendar)
- **Components**: `CalendarGrid`, `EventCard`, `EventDrawer`, `PlatformFilter`
- **Responsibilities**: Unified contest calendar from multiple platforms
- **Data Sources**: Supabase (contests table)
- **Features**: Filter by platform, sort by date, highlight upcoming (24h), drawer with details
- **Caching**: React Query with 15-minute stale time

#### 3. Academics Module
- **Components**: `CGPACalculator`, `SGPACalculator`, `Predictor`, `SemesterHistory`, `TargetOptimizer`
- **Responsibilities**: CGPA/SGPA calculation, prediction, optimization, semester management
- **Data Sources**: Supabase (academics table)
- **Features**: Add/edit/delete semesters, calculate cumulative CGPA, predict future grades
- **Caching**: React Query with infinite stale time (user-specific data)

#### 4. Opportunities Module
- **Components**: `OpportunityGrid`, `OpportunityCard`, `FilterBar`, `SearchInput`, `SaveButton`
- **Responsibilities**: Display, filter, search, and save opportunities
- **Data Sources**: Supabase (opportunities, saved_opportunities tables)
- **Features**: Category filter, location filter, keyword search, save/unsave, expiry countdown
- **Caching**: React Query with 10-minute stale time, pagination (20 per page)

#### 5. Campus Life Module
- **Components**: `AnnouncementList`, `AnnouncementCard`, `AnnouncementEditor`, `SocietyBadge`
- **Responsibilities**: View announcements, create/edit/delete (PR Heads only)
- **Data Sources**: Supabase (announcements, societies tables)
- **Features**: Rich text editor, society filter, verified badges, realtime updates
- **Caching**: React Query with realtime subscription for live updates

#### 6. Profile Module
- **Components**: `ProfileForm`, `InterestsSelector`, `SkillsInput`, `CompletionProgress`
- **Responsibilities**: User profile management, interests, skills, completion tracking
- **Data Sources**: Supabase (users table)
- **Features**: Multi-select interests, tag-based skills, progress bar
- **Caching**: React Query with infinite stale time

#### 7. AI Assistant Module
- **Components**: `AIChat`, `QuickLinks`, `RecommendationCard`
- **Responsibilities**: Navigation help, opportunity/society recommendations
- **Data Sources**: Backend API (Gemini integration)
- **Features**: Context-aware suggestions, quick navigation, personalized recommendations
- **Caching**: Session-based (no persistent cache)

---

## Data Model

### Core Tables

#### users (Supabase Auth + Custom Profile)
```sql
users {
  id: uuid (PK, from auth.users)
  email: string (unique)
  full_name: string
  role: enum('student', 'pr_head')
  year: integer
  branch: string
  interests: text[] (array)
  skills: text[] (array)
  profile_completion: integer (0-100)
  avatar_url: string (nullable)
  created_at: timestamp
  updated_at: timestamp
}
```

#### academics
```sql
academics {
  id: uuid (PK)
  user_id: uuid (FK → users.id)
  semester: integer
  subjects: jsonb[] (array of {name, grade, credits})
  sgpa: decimal(3,2)
  cgpa: decimal(3,2)
  created_at: timestamp
  updated_at: timestamp
  
  UNIQUE(user_id, semester)
}
```

#### contests
```sql
contests {
  id: uuid (PK)
  name: string
  platform: enum('leetcode', 'codeforces', 'codechef', 'atcoder')
  start_time: timestamp
  duration: integer (seconds)
  link: string (URL)
  fetched_at: timestamp
  created_at: timestamp
  
  INDEX(start_time)
  INDEX(platform)
}
```

#### opportunities
```sql
opportunities {
  id: uuid (PK)
  title: string
  description: text
  category: enum('hackathon', 'internship', 'scholarship', 'workshop', 'conference')
  deadline: timestamp
  location: string
  link: string (URL)
  tags: text[] (array)
  source: string
  fetched_at: timestamp
  created_at: timestamp
  updated_at: timestamp
  
  INDEX(deadline)
  INDEX(category)
  INDEX(tags) USING GIN
}
```

#### saved_opportunities
```sql
saved_opportunities {
  id: uuid (PK)
  user_id: uuid (FK → users.id)
  opportunity_id: uuid (FK → opportunities.id)
  saved_at: timestamp
  
  UNIQUE(user_id, opportunity_id)
  INDEX(user_id)
}
```

#### societies
```sql
societies {
  id: uuid (PK)
  name: string (unique)
  description: text
  verified: boolean (default false)
  logo_url: string (nullable)
  created_at: timestamp
  updated_at: timestamp
}
```

#### announcements
```sql
announcements {
  id: uuid (PK)
  society_id: uuid (FK → societies.id)
  author_id: uuid (FK → users.id)
  title: string
  content: text (rich text/markdown)
  published_at: timestamp
  created_at: timestamp
  updated_at: timestamp
  
  INDEX(society_id)
  INDEX(published_at DESC)
}
```

#### society_members
```sql
society_members {
  id: uuid (PK)
  society_id: uuid (FK → societies.id)
  user_id: uuid (FK → users.id)
  role: enum('member', 'pr_head')
  joined_at: timestamp
  
  UNIQUE(society_id, user_id)
  INDEX(user_id)
}
```

### Relationships
- `users` 1:N `academics` (one user, many semester records)
- `users` 1:N `saved_opportunities` (one user, many saved items)
- `users` N:M `societies` (through `society_members`)
- `societies` 1:N `announcements` (one society, many announcements)
- `users` 1:N `announcements` (one author, many announcements)
- `opportunities` N:M `users` (through `saved_opportunities`)

---

## Security Model

### Supabase Row Level Security (RLS) Policies

#### users table
```sql
-- Users can read their own profile
CREATE POLICY "Users can view own profile"
  ON users FOR SELECT
  USING (auth.uid() = id);

-- Users can update their own profile
CREATE POLICY "Users can update own profile"
  ON users FOR UPDATE
  USING (auth.uid() = id);
```

#### academics table
```sql
-- Users can read their own academic records
CREATE POLICY "Users can view own academics"
  ON academics FOR SELECT
  USING (auth.uid() = user_id);

-- Users can insert their own academic records
CREATE POLICY "Users can insert own academics"
  ON academics FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- Users can update their own academic records
CREATE POLICY "Users can update own academics"
  ON academics FOR UPDATE
  USING (auth.uid() = user_id);

-- Users can delete their own academic records
CREATE POLICY "Users can delete own academics"
  ON academics FOR DELETE
  USING (auth.uid() = user_id);
```

#### opportunities table
```sql
-- All authenticated users can read opportunities
CREATE POLICY "Authenticated users can view opportunities"
  ON opportunities FOR SELECT
  TO authenticated
  USING (true);

-- Only backend service role can write
-- (enforced via service_role key, not RLS)
```

#### saved_opportunities table
```sql
-- Users can view their own saved opportunities
CREATE POLICY "Users can view own saved opportunities"
  ON saved_opportunities FOR SELECT
  USING (auth.uid() = user_id);

-- Users can save opportunities
CREATE POLICY "Users can save opportunities"
  ON saved_opportunities FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- Users can unsave opportunities
CREATE POLICY "Users can unsave opportunities"
  ON saved_opportunities FOR DELETE
  USING (auth.uid() = user_id);
```

