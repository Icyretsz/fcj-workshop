---
title: "Week 12 Worklog"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.12. </b> "
---

### Week 12 Objectives:

* Organize and optimize API routes with React Query
* Build additional pages for Type Rush (FCJ Final Project)
* Enhance user experience with leaderboard and user profile features

### Tasks to be carried out this week:
| Day | Task                                                                             | Start Date | Completion Date | Reference Material                                                                                                                            |
| --- |----------------------------------------------------------------------------------|------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Define and organize API routes structure<br/>- Set up React Query (TanStack Query) | 17/11/2025 | 17/11/2025 | <https://tanstack.com/query/latest/docs/react/overview> |
| 3   | - Build Leaderboard page<br/>- Implement ranking filters and sorting | 18/11/2025 | 18/11/2025 | |
| 4   | - Build Users page<br/>- Implement user search and profile cards | 19/11/2025 | 19/11/2025 | |
| 5   | - Design and build Landing page<br/>- Create hero section and features showcase | 20/11/2025 | 20/11/2025 | |
| 6   | - Polish and optimize all pages<br/>- Test and fix bugs | 21/11/2025 | 21/11/2025 | |


### Week 12 Achievements:

**API Routes Organization and React Query Setup (Day 1)**
* Defined comprehensive API route structure:
    * Authentication routes: `/api/auth/login`, `/api/auth/register`, `/api/auth/logout`, `/api/auth/refresh`.
    * User routes: `/api/users/:id`, `/api/users/:id/stats`, `/api/users/search`.
    * Game routes: `/api/games/history`, `/api/games/:id`, `/api/games/stats`.
    * Leaderboard routes: `/api/leaderboard/global`, `/api/leaderboard/daily`, `/api/leaderboard/weekly`.
    * Profile routes: `/api/profile`, `/api/profile/achievements`, `/api/profile/settings`.
* Organized API client structure:
    * Created `api/` folder with modular organization:
        * `api/client.ts`: Axios instance with interceptors.
        * `api/auth.ts`: Authentication-related API calls.
        * `api/users.ts`: User-related API calls.
        * `api/games.ts`: Game data API calls.
        * `api/leaderboard.ts`: Leaderboard API calls.
    * Configured Axios interceptors for:
        * Automatic token attachment to requests.
        * Token refresh on 401 errors.
        * Error handling and logging.
        * Request/response transformation.

* Set up React Query (TanStack Query):
    * Installed and configured `@tanstack/react-query` package.
    * Created `QueryClient` with optimized default options:
        * Stale time: 5 minutes for most queries.
        * Cache time: 10 minutes.
        * Retry logic: 3 retries with exponential backoff.
        * Refetch on window focus for real-time data.
    * Set up `QueryClientProvider` in app root.
    * Configured React Query DevTools for debugging.
* Created custom hooks for API queries:
    * `useAuth()`: Authentication state and mutations.
    * `useUser(userId)`: Fetch user data with caching.
    * `useUserStats(userId)`: Fetch user statistics.
    * `useLeaderboard(type)`: Fetch leaderboard data (global, daily, weekly).
    * `useGameHistory(userId)`: Fetch game history with pagination.
    * `useSearchUsers(query)`: Search users with debouncing.
* Implemented mutation hooks for data updates:
    * `useUpdateProfile()`: Update user profile with optimistic updates.
    * `useUpdateSettings()`: Update user settings.
    * Configured cache invalidation on successful mutations.
    * Added error handling and success notifications.

**Leaderboard Page Development (Day 2)**
* Designed Leaderboard page layout:
    * Responsive grid layout with ranking cards.
    * Filter tabs for different leaderboard types (Global, Daily, Weekly, Monthly).
    * Top 3 podium display with special styling.
    * Infinite scroll or pagination for full leaderboard.
    * Search/filter functionality for finding specific users.
* Implemented Leaderboard components:
    * `LeaderboardPage` (main container):
        * Tab navigation for leaderboard types.
        * Filter controls (time period, game mode).
        * Search bar for finding users.
    * `LeaderboardTabs` component:
        * Tab buttons for Global, Daily, Weekly, Monthly.
        * Active tab highlighting.
        * Tab change handling with query params.
    * `LeaderboardPodium` component:
        * Special cards for top 3 players (1st, 2nd, 3rd).
        * Gold, silver, bronze styling.
        * Player avatars, usernames, and scores.
        * Achievement badges and titles.
    * `LeaderboardTable` component:
        * Table/list view for ranks 4+.
        * Columns: Rank, Player, WPM, Accuracy, Games Played, Points.
        * Sortable columns (click to sort by different metrics).
        * Hover effects and row highlighting.
    * `LeaderboardCard` component:
        * Individual ranking card with player info.
        * Rank badge, avatar, username.
        * Key stats display (WPM, accuracy, games).
        * Link to user profile.

* Integrated React Query for leaderboard data:
    * Used `useLeaderboard(type)` hook to fetch data:
        ```typescript
        const { data: leaderboard, isLoading, error } = useLeaderboard('global');
        ```
    * Implemented automatic refetching on tab change.
    * Added loading skeletons for better UX.
    * Cached leaderboard data to reduce API calls.
    * Implemented optimistic updates when user's rank changes.
* Added leaderboard features:
    * Real-time rank updates (refetch on interval).
    * User's current rank highlighting (if in top 100).
    * "Your rank" indicator showing user's position.
    * Filter by game mode (Wave Rush, Type Race).
    * Date range selector for historical leaderboards.
    * Export leaderboard data (CSV/PDF).
* Implemented responsive design:
    * Mobile-friendly card layout for smaller screens.
    * Collapsible filters on mobile.
    * Touch-optimized scrolling and interactions.

**Users Page Development (Day 3)**
* Designed Users page layout:
    * Search bar with real-time filtering.
    * Grid layout of user profile cards.
    * Pagination or infinite scroll.
    * Filter options (online status, rank range, skill level).
    * Sort options (by rank, recent activity, WPM).
* Implemented Users page components:
    * `UsersPage` (main container):
        * Search bar at top.
        * Filter and sort controls.
        * User grid with responsive layout.
    * `UserSearchBar` component:
        * Search input with debounced queries.
        * Search icon and clear button.
        * Autocomplete dropdown with suggestions.
        * Recent searches history.
    * `UserCard` component:
        * User avatar with online status indicator.
        * Username and user rank/title.
        * Key stats preview (WPM, accuracy, games).
        * Follow/Unfollow button (if applicable).
        * Link to detailed profile page.
    * `UserFilters` component:
        * Online/Offline filter toggle.
        * Rank range slider.
        * Game mode filter.
        * Sort dropdown (WPM, Rank, Recent Activity).
    * `UserGrid` component:
        * Responsive grid layout (1-4 columns based on screen size).
        * Loading states with skeleton cards.
        * Empty state when no users found.

* Integrated React Query for users data:
    * Used `useSearchUsers(query)` hook with debouncing:
        ```typescript
        const debouncedQuery = useDebounce(searchQuery, 300);
        const { data: users, isLoading } = useSearchUsers(debouncedQuery);
        ```
    * Implemented pagination with `useInfiniteQuery`:
        * Load more button or infinite scroll.
        * Prefetch next page for smooth experience.
    * Cached user search results for instant back navigation.
    * Implemented filter and sort on client-side for cached data.
* Added user interaction features:
    * Click on user card to view full profile.
    * Challenge user to game (opens game invitation).
    * View user statistics and game history.
    * Follow/Unfollow functionality (if social features enabled).
    * Share user profile link.

**Landing Page Development (Day 4)**
* Designed Landing page structure:
    * Hero section with call-to-action.
    * Features showcase section.
    * How it works section.
    * Leaderboard preview.
    * Testimonials or user reviews (optional).
    * Footer with links and social media.
* Implemented Landing page components:
    * `LandingPage` (main container).
    * `HeroSection` component:
        * Large heading: "Improve Your Typing Speed".
        * Subheading: Description of Type Rush game.
        * Primary CTA button: "Start Playing" (links to registration/game).
        * Secondary CTA: "View Leaderboard".
        * Background animation or gradient.
        * Hero image or game preview screenshot.
    * `FeaturesSection` component:
        * Grid of feature cards (3-4 features).
        * Features highlighted:
            * "Multiplayer Racing": Compete with players worldwide.
            * "Real-time Progress": See your competitors' progress live.
            * "Multiple Game Modes": Wave Rush and Type Race.
            * "Track Your Progress": Detailed statistics and history.
        * Icon + title + description for each feature.
        * Animations on scroll (fade-in, slide-in).
    * `HowItWorksSection` component:
        * Step-by-step guide (3-4 steps).
        * Steps: 1) Create Account, 2) Join Game, 3) Type Fast, 4) Win!
        * Visual indicators (numbers or icons).
        * Simple illustrations or screenshots.
    * `LeaderboardPreview` component:
        * "Top Players" heading.
        * Top 5-10 players from global leaderboard.
        * Mini leaderboard cards.
        * "View Full Leaderboard" link.
    * `CTASection` component:
        * Final call-to-action before footer.
        * "Ready to compete?" heading.
        * "Sign Up Now" button.
        * Encouraging copy about joining the community.
    * `Footer` component:
        * Links: About, Privacy Policy, Terms of Service, Contact.
        * Social media icons (if applicable).
        * Copyright notice.

* Styled Landing page with modern design:
    * Used Tailwind CSS for responsive design.
    * Implemented gradient backgrounds and color schemes.
    * Added smooth scroll animations with Framer Motion or CSS transitions.
    * Used high-quality images and icons.
    * Ensured mobile responsiveness for all sections.
* Integrated dynamic data:
    * Fetched top players for leaderboard preview using React Query.
    * Displayed live player count (optional).
    * Showed recent game results or activity feed (optional).

**Additional Page Enhancements (Day 5-6)**
* User Profile Page (if not yet built):
    * Detailed user statistics display.
    * Game history with filters.
    * Achievements and badges earned.
    * Personal records and milestones.
    * Settings and preferences section.
* Game History Page:
    * Filterable game history table.
    * Detailed stats for each game played.
    * Replay functionality (if applicable).
    * Export game data.
* Settings Page:
    * Profile settings (avatar, username, email).
    * Game preferences (sound, notifications).
    * Privacy settings.
    * Account management (password change, delete account).

**React Query Optimizations**
* Implemented advanced React Query features:
    * Prefetching for improved navigation speed:
        ```typescript
        queryClient.prefetchQuery(['user', userId], () => fetchUser(userId));
        ```
    * Optimistic updates for instant UI feedback:
        * Update cache immediately on mutation.
        * Rollback on error with previous data.
    * Background refetching for stale data:
        * Refetch leaderboard every 30 seconds.
        * Refetch user stats on window focus.
    * Pagination with `useInfiniteQuery` for large datasets.
    * Query invalidation strategy:
        * Invalidate related queries on mutations.
        * Automatic cache cleanup for unused data.

**Performance Optimizations**
* Implemented frontend performance improvements:
    * Code splitting with React.lazy() for route-based components.
    * Image optimization with lazy loading.
    * Debounced search input to reduce API calls.
    * Memoized expensive computations with useMemo.
    * Virtualized lists for long leaderboards (react-window).
* Optimized React Query configuration:
    * Adjusted stale time based on data volatility.
    * Configured cache time to balance memory and performance.
    * Used query keys effectively for granular cache control.
    * Implemented query placeholders for instant loading states.

**Bug Fixes and Polish (Day 6)**
* Fixed issues identified during testing:
    * WebSocket reconnection edge cases.
    * Race conditions in game state updates.
    * Leaderboard ranking calculation bugs.
    * Mobile layout issues on small screens.
    * Form validation errors.
* UI/UX improvements:
    * Added loading states for all async operations.
    * Improved error messages and user feedback.
    * Enhanced animations for smoother transitions.
    * Refined color scheme and typography.
    * Improved accessibility (keyboard navigation, ARIA labels).
* Code refactoring:
    * Extracted reusable components.
    * Cleaned up unused code and imports.
    * Improved TypeScript types for better type safety.
    * Added JSDoc comments for complex functions.
    * Organized folder structure for better maintainability.

**Testing and Validation**
* Conducted comprehensive testing:
    * Tested all API integrations with React Query.
    * Verified cache behavior and invalidation.
    * Tested pagination and infinite scroll.
    * Validated search and filter functionality.
    * Tested responsive design across devices.
    * Checked accessibility with screen readers.
* Performance testing:
    * Measured page load times.
    * Checked for memory leaks in React components.
    * Validated WebSocket connection stability.
    * Tested with slow network conditions.

**Documentation and Code Quality**
* Improved code documentation:
    * Added README for API routes structure.
    * Documented React Query hooks usage.
    * Created component documentation with props and examples.
    * Added inline comments for complex logic.
* Established coding standards:
    * Consistent naming conventions.
    * TypeScript strict mode enabled.
    * ESLint and Prettier configuration.
    * Git commit message conventions.

**Key Takeaways**
* React Query benefits realized:
    * Simplified data fetching and state management.
    * Built-in caching significantly improved performance.
    * Automatic background refetching kept data fresh.
    * Optimistic updates enhanced user experience.
    * DevTools helped debug query issues easily.
* Frontend architecture improvements:
    * Modular API client structure improved maintainability.
    * Custom hooks promoted code reuse.
    * Separation of concerns made components cleaner.
    * TypeScript caught many potential bugs early.