# IGDTUW Student Hub - Requirements Document

## Overview

IGDTUW Student Hub is a centralized platform designed for students to manage their academic progress, discover opportunities, stay updated with campus activities, and participate in competitive programming contests. The platform aggregates information from multiple sources and provides personalized recommendations through AI assistance.

### Problem Statement

Students currently face fragmented information across multiple platforms for contests, opportunities, campus announcements, and academic tracking. This leads to missed opportunities, inefficient time management, and difficulty in maintaining academic goals. IGDTUW Student Hub consolidates these resources into a single, personalized platform.

---

## Goals and Non-Goals

### Goals
- Provide a unified dashboard for academic tracking, opportunities, and campus life
- Aggregate competitive programming contests from multiple platforms
- Automate opportunity discovery and management with intelligent filtering
- Enable society PR Heads to manage announcements independently
- Offer AI-powered assistance for navigation and personalized recommendations
- Ensure mobile-responsive experience for on-the-go access

### Non-Goals
- Direct contest participation or submission (external platform links only)
- Payment processing for opportunities or events
- Real-time chat or messaging between users
- Course management or assignment submission
- Attendance tracking or grade management by faculty

---

## Personas / Roles

### Student (Primary User)
- Views personalized dashboard with CGPA, saved opportunities, and activity feed
- Tracks academic performance using CGPA/SGPA calculators
- Discovers and saves opportunities (hackathons, internships, scholarships)
- Views campus announcements and society updates
- Receives AI-powered recommendations based on interests
- Accesses unified contest calendar

### PR Head (Society Representative)
- Creates, edits, and deletes announcements for their society
- Manages society profile and information
- Cannot modify other societies' content
- Has verified badge on society posts

### System Automation
- Fetches opportunities via SerpAPI every 24 hours
- Cleans and structures data using Gemini API
- Auto-deletes expired opportunities
- Syncs contest data from multiple APIs
- Enforces data retention policies

---

## Functional Requirements

### FR-1: Authentication & User Management
- FR-1.1: Users must authenticate via Supabase Auth (email/password, OAuth)
- FR-1.2: System must support role assignment (Student, PR Head)
- FR-1.3: Users must complete profile with interests, year, branch, skills
- FR-1.4: Profile completion percentage must be displayed on dashboard
- FR-1.5: Users can update profile information at any time

### FR-2: Personalized Dashboard
- FR-2.1: Display current CGPA prominently
- FR-2.2: Show saved opportunities count with quick access
- FR-2.3: Display upcoming events from calendar (next 7 days)
- FR-2.4: Show recent campus announcements (last 5)
- FR-2.5: Display profile completion status with actionable prompts
- FR-2.6: Provide quick action buttons for common tasks

### FR-3: Calgo (Contest Calendar)
- FR-3.1: Fetch contests from LeetCode, Codeforces, CodeChef, AtCoder APIs
- FR-3.2: Display unified calendar view with all contests
- FR-3.3: Show contest details (name, platform, start time, duration, link)
- FR-3.4: Open contest details in drawer on click
- FR-3.5: Filter contests by platform
- FR-3.6: Sort contests by date (upcoming first)
- FR-3.7: Highlight contests starting within 24 hours
- FR-3.8: Provide direct links to contest platforms

### FR-4: Academics Module
- FR-4.1: Calculate SGPA for individual semesters (subject-wise grades and credits)
- FR-4.2: Calculate cumulative CGPA from all semesters
- FR-4.3: Store semester history with grades and credits
- FR-4.4: Allow editing of past semester data
- FR-4.5: Allow deletion of semester records
- FR-4.6: Predict future SGPA/CGPA based on target grades
- FR-4.7: Optimize grade distribution to achieve target CGPA
- FR-4.8: Display visual representation of academic progress

### FR-5: Opportunities Module
- FR-5.1: Fetch opportunities (hackathons, internships, scholarships, workshops, conferences) via SerpAPI
- FR-5.2: Clean and structure raw data using Gemini API
- FR-5.3: Store opportunities with fields: title, description, deadline, location, link, category, tags
- FR-5.4: Auto-refresh opportunities every 24 hours
- FR-5.5: Auto-delete opportunities past deadline
- FR-5.6: Filter opportunities by category, location, tags
- FR-5.7: Search opportunities by keywords
- FR-5.8: Allow users to save/bookmark opportunities
- FR-5.9: Display saved opportunities on dashboard
- FR-5.10: Show opportunity expiry countdown

### FR-6: Campus Life Module
- FR-6.1: Display announcements from societies and campus administration
- FR-6.2: PR Heads can create new announcements for their society
- FR-6.3: PR Heads can edit their own society's announcements
- FR-6.4: PR Heads can delete their own society's announcements
- FR-6.5: Students can view all announcements (read-only)
- FR-6.6: Display verified badge for official societies
- FR-6.7: Show announcement metadata (society name, date, author)
- FR-6.8: Filter announcements by society
- FR-6.9: Sort announcements by date (recent first)
- FR-6.10: Support rich text formatting in announcements

### FR-7: AI Assistant
- FR-7.1: Provide quick links to major platform sections
- FR-7.2: Offer navigation help and feature discovery
- FR-7.3: Suggest opportunities based on user interests and profile
- FR-7.4: Recommend societies based on user interests
- FR-7.5: Answer common questions about platform features
- FR-7.6: Integrate with Gemini API for intelligent responses

### FR-8: UI/UX Features
- FR-8.1: Implement light/dark theme toggle
- FR-8.2: Persist theme preference across sessions
- FR-8.3: Ensure fully responsive design (mobile, tablet, desktop)
- FR-8.4: Provide loading states for async operations
- FR-8.5: Display error messages with actionable guidance
- FR-8.6: Implement smooth transitions and animations

---

## Non-Functional Requirements

### NFR-1: Performance
- NFR-1.1: Dashboard must load within 2 seconds on 4G connection
- NFR-1.2: API responses must return within 500ms for cached data
- NFR-1.3: Contest calendar must render 100+ contests without lag
- NFR-1.4: Opportunity search must return results within 1 second
- NFR-1.5: Frontend bundle size must not exceed 500KB (gzipped)

### NFR-2: Security
- NFR-2.1: All API endpoints must require authentication
- NFR-2.2: Implement Row Level Security (RLS) in Supabase
- NFR-2.3: Validate and sanitize all user inputs
- NFR-2.4: Store API keys securely in environment variables
- NFR-2.5: Implement CORS policies for backend API
- NFR-2.6: Use HTTPS for all communications
- NFR-2.7: Implement rate limiting on API endpoints

### NFR-3: Accessibility
- NFR-3.1: Support keyboard navigation throughout the platform
- NFR-3.2: Provide ARIA labels for interactive elements
- NFR-3.3: Ensure color contrast ratios meet WCAG 2.1 AA standards
- NFR-3.4: Support screen readers for visually impaired users
- NFR-3.5: Provide text alternatives for visual content

### NFR-4: Reliability
- NFR-4.1: System uptime must be 99.5% or higher
- NFR-4.2: Implement graceful degradation when external APIs fail
- NFR-4.3: Provide fallback UI when data is unavailable
- NFR-4.4: Log errors for debugging and monitoring
- NFR-4.5: Implement retry logic for failed API calls

### NFR-5: Scalability
- NFR-5.1: Support up to 5,000 concurrent users
- NFR-5.2: Database queries must be optimized with proper indexing
- NFR-5.3: Implement pagination for large datasets (opportunities, announcements)
- NFR-5.4: Use CDN for static assets (Cloudflare Pages)
- NFR-5.5: Backend must handle 100 requests/second

### NFR-6: Maintainability
- NFR-6.1: Code must follow TypeScript best practices
- NFR-6.2: Maintain minimum 80% type coverage
- NFR-6.3: Use consistent naming conventions across codebase
- NFR-6.4: Document complex business logic with comments
- NFR-6.5: Separate concerns (services, components, utilities)

---

## Data Requirements

### User Table
- `id` (UUID, primary key)
- `email` (string, unique)
- `full_name` (string)
- `role` (enum: student, pr_head)
- `year` (integer)
- `branch` (string)
- `interests` (array of strings)
- `skills` (array of strings)
- `profile_completion` (integer, percentage)
- `created_at`, `updated_at` (timestamps)

### Academic Records Table
- `id` (UUID, primary key)
- `user_id` (UUID, foreign key)
- `semester` (integer)
- `subjects` (JSONB: array of {name, grade, credits})
- `sgpa` (decimal)
- `cgpa` (decimal)
- `created_at`, `updated_at` (timestamps)

### Opportunities Table
- `id` (UUID, primary key)
- `title` (string)
- `description` (text)
- `category` (enum: hackathon, internship, scholarship, workshop, conference)
- `deadline` (timestamp)
- `location` (string)
- `link` (string, URL)
- `tags` (array of strings)
- `source` (string)
- `fetched_at` (timestamp)
- `created_at`, `updated_at` (timestamps)

### Saved Opportunities Table
- `id` (UUID, primary key)
- `user_id` (UUID, foreign key)
- `opportunity_id` (UUID, foreign key)
- `saved_at` (timestamp)

### Contests Table
- `id` (UUID, primary key)
- `name` (string)
- `platform` (enum: leetcode, codeforces, codechef, atcoder)
- `start_time` (timestamp)
- `duration` (integer, seconds)
- `link` (string, URL)
- `fetched_at` (timestamp)

### Societies Table
- `id` (UUID, primary key)
- `name` (string, unique)
- `description` (text)
- `verified` (boolean)
- `logo_url` (string)
- `created_at`, `updated_at` (timestamps)

### Announcements Table
- `id` (UUID, primary key)
- `society_id` (UUID, foreign key)
- `author_id` (UUID, foreign key)
- `title` (string)
- `content` (text, rich text)
- `published_at` (timestamp)
- `created_at`, `updated_at` (timestamps)

### Society Members Table
- `id` (UUID, primary key)
- `society_id` (UUID, foreign key)
- `user_id` (UUID, foreign key)
- `role` (enum: member, pr_head)
- `joined_at` (timestamp)

---

## Security & Privacy Requirements

### Authentication & Authorization
- Implement Supabase Auth for user authentication
- Use JWT tokens for API authorization
- Implement role-based access control (RBAC) for PR Heads
- Enforce ownership-based access for announcements (PR Heads can only modify their society's content)

### Row Level Security (RLS) Policies
- Users can only read/write their own academic records
- Users can only read/write their own saved opportunities
- PR Heads can only create/update/delete announcements for their assigned society
- All users can read opportunities and contests (public data)
- Students can only read announcements (no write access)

### Data Privacy
- Do not expose user email addresses publicly
- Anonymize user data in logs and error reports
- Implement data retention policy (delete old opportunities automatically)
- Allow users to delete their account and associated data

### Audit & Compliance
- Log all announcement create/update/delete operations with user ID and timestamp
- Track opportunity fetch operations for debugging
- Monitor API usage to detect abuse or anomalies

---

## Integrations

### Contest APIs
- **LeetCode API**: Fetch upcoming contests; handle rate limits (max 60 requests/hour)
- **Codeforces API**: Fetch contest list; handle rate limits (max 1 request/2 seconds)
- **CodeChef API**: Fetch contest schedule; handle authentication if required
- **AtCoder API**: Fetch contest information; parse HTML if no official API available
- **Failure Handling**: Cache last successful fetch; display stale data with warning; retry with exponential backoff

### SerpAPI Integration
- Fetch opportunities using search queries (e.g., "hackathons in India 2026")
- Handle rate limits (free tier: 100 searches/month)
- Implement query rotation to maximize coverage
- Parse search results and extract relevant fields
- **Failure Handling**: Log failed requests; skip current cycle; alert admin if consecutive failures

### Gemini API Integration
- Clean and structure raw opportunity data from SerpAPI
- Extract key information (title, deadline, location, category, tags)
- Generate AI assistant responses for user queries
- Provide opportunity and society recommendations
- Handle rate limits (free tier: 60 requests/minute)
- **Failure Handling**: Return raw data if cleaning fails; use fallback responses for AI assistant

---

## Assumptions & Constraints

### Assumptions
- Target audience is IGDTUW students (initial scope)
- Users have reliable internet access
- External APIs (contests, SerpAPI) remain available and stable
- Gemini API provides accurate data cleaning and recommendations
- PR Heads are verified manually before role assignment

### Constraints
- **Budget**: Using free tiers for all services (Supabase, Render, Cloudflare, APIs)
- **API Limits**: SerpAPI limited to 100 searches/month; Gemini API limited to 60 requests/minute
- **Hackathon Timeline**: MVP must be completed within hackathon duration
- **Team Size**: Limited development resources; prioritize core features
- **Data Freshness**: Opportunities refresh every 24 hours (not real-time)
- **Storage**: Supabase free tier has 500MB database limit

---

## Acceptance Criteria

- User can sign up, log in, and complete profile with interests and skills
- Dashboard displays CGPA, saved opportunities count, upcoming events, and recent announcements
- Contest calendar shows unified view of contests from all 4 platforms with accurate timing
- CGPA calculator correctly computes SGPA and cumulative CGPA from semester data
- Users can add, edit, and delete semester records
- CGPA predictor suggests grade distribution to achieve target CGPA
- Opportunities are fetched automatically every 24 hours via SerpAPI
- Gemini API successfully cleans and structures opportunity data
- Expired opportunities are automatically deleted after deadline passes
- Users can filter and search opportunities by category, location, and keywords
- Users can save opportunities and view them on dashboard
- PR Heads can create, edit, and delete announcements for their assigned society only
- Students can view all announcements but cannot modify them
- Verified societies display badge on announcements
- AI Assistant provides navigation help and personalized recommendations
- Theme toggle switches between light and dark modes and persists preference
- Platform is fully responsive on mobile, tablet, and desktop devices
- All authenticated API endpoints reject unauthorized requests
- RLS policies prevent users from accessing others' private data
- Frontend loads within 2 seconds on 4G connection
- Backend handles API failures gracefully with fallback UI
- Platform maintains 99.5% uptime during testing period

---

**Document Version**: 1.0  
**Last Updated**: February 15, 2026  
**Status**: Draft - Implementation Ready
