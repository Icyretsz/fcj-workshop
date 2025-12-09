---
title: "Worklog tuần 12"
date: "2025-09-15"
weight: 1
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu tuần 12:

* Tổ chức và tối ưu hóa API routes với React Query
* Xây dựng các trang bổ sung cho Type Rush (Dự án cuối kỳ FCJ)
* Nâng cao trải nghiệm người dùng với leaderboard và user profile features

### Các nhiệm vụ cần thực hiện trong tuần này:
| Ngày | Nhiệm vụ                                                                             | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo                                                                                                                            |
| --- |----------------------------------------------------------------------------------|------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Định nghĩa và tổ chức cấu trúc API routes<br/>- Thiết lập React Query (TanStack Query) | 17/11/2025 | 17/11/2025 | <https://tanstack.com/query/latest/docs/react/overview> |
| 3   | - Xây dựng trang Leaderboard<br/>- Triển khai ranking filters và sorting | 18/11/2025 | 18/11/2025 | |
| 4   | - Xây dựng trang Users<br/>- Triển khai user search và profile cards | 19/11/2025 | 19/11/2025 | |
| 5   | - Thiết kế và xây dựng Landing page<br/>- Tạo hero section và features showcase | 20/11/2025 | 20/11/2025 | |
| 6   | - Hoàn thiện và tối ưu hóa tất cả các trang<br/>- Kiểm tra và sửa bugs | 21/11/2025 | 21/11/2025 | |


### Thành tựu tuần 12:

**Tổ chức API Routes và thiết lập React Query (Ngày 1)**
* Định nghĩa cấu trúc API route toàn diện:
    * Authentication routes: `/api/auth/login`, `/api/auth/register`, `/api/auth/logout`, `/api/auth/refresh`.
    * User routes: `/api/users/:id`, `/api/users/:id/stats`, `/api/users/search`.
    * Game routes: `/api/games/history`, `/api/games/:id`, `/api/games/stats`.
    * Leaderboard routes: `/api/leaderboard/global`, `/api/leaderboard/daily`, `/api/leaderboard/weekly`.
    * Profile routes: `/api/profile`, `/api/profile/achievements`, `/api/profile/settings`.
* Tổ chức cấu trúc API client:
    * Tạo thư mục `api/` với tổ chức modular:
        * `api/client.ts`: Axios instance với interceptors.
        * `api/auth.ts`: API calls liên quan đến Authentication.
        * `api/users.ts`: API calls liên quan đến User.
        * `api/games.ts`: Game data API calls.
        * `api/leaderboard.ts`: Leaderboard API calls.
    * Cấu hình Axios interceptors cho:
        * Automatic token attachment vào requests.
        * Token refresh trên lỗi 401.
        * Error handling và logging.
        * Request/response transformation.

* Thiết lập React Query (TanStack Query):
    * Cài đặt và cấu hình package `@tanstack/react-query`.
    * Tạo `QueryClient` với default options được tối ưu hóa:
        * Stale time: 5 phút cho hầu hết các queries.
        * Cache time: 10 phút.
        * Retry logic: 3 lần retry với exponential backoff.
        * Refetch on window focus cho real-time data.
    * Thiết lập `QueryClientProvider` ở app root.
    * Cấu hình React Query DevTools cho debugging.
* Tạo custom hooks cho API queries:
    * `useAuth()`: Authentication state và mutations.
    * `useUser(userId)`: Fetch user data với caching.
    * `useUserStats(userId)`: Fetch user statistics.
    * `useLeaderboard(type)`: Fetch leaderboard data (global, daily, weekly).
    * `useGameHistory(userId)`: Fetch game history với pagination.
    * `useSearchUsers(query)`: Search users với debouncing.
* Triển khai mutation hooks cho data updates:
    * `useUpdateProfile()`: Update user profile với optimistic updates.
    * `useUpdateSettings()`: Update user settings.
    * Cấu hình cache invalidation khi mutations thành công.
    * Thêm error handling và success notifications.

**Phát triển trang Leaderboard (Ngày 2)**
* Thiết kế layout trang Leaderboard:
    * Responsive grid layout với ranking cards.
    * Filter tabs cho các loại leaderboard khác nhau (Global, Daily, Weekly, Monthly).
    * Hiển thị podium top 3 với styling đặc biệt.
    * Infinite scroll hoặc pagination cho full leaderboard.
    * Chức năng search/filter để tìm người dùng cụ thể.
* Triển khai các Leaderboard components:
    * `LeaderboardPage` (main container):
        * Tab navigation cho leaderboard types.
        * Filter controls (time period, game mode).
        * Search bar để tìm users.
    * Component `LeaderboardTabs`:
        * Tab buttons cho Global, Daily, Weekly, Monthly.
        * Active tab highlighting.
        * Tab change handling với query params.
    * Component `LeaderboardPodium`:
        * Special cards cho top 3 players (1st, 2nd, 3rd).
        * Gold, silver, bronze styling.
        * Player avatars, usernames và scores.
        * Achievement badges và titles.
    * Component `LeaderboardTable`:
        * Table/list view cho ranks 4+.
        * Columns: Rank, Player, WPM, Accuracy, Games Played, Points.
        * Sortable columns (click để sort theo metrics khác nhau).
        * Hover effects và row highlighting.
    * Component `LeaderboardCard`:
        * Individual ranking card với player info.
        * Rank badge, avatar, username.
        * Key stats display (WPM, accuracy, games).
        * Link đến user profile.

* Tích hợp React Query cho leaderboard data:
    * Sử dụng hook `useLeaderboard(type)` để fetch data:
        ```typescript
        const { data: leaderboard, isLoading, error } = useLeaderboard('global');
        ```
    * Triển khai automatic refetching khi tab change.
    * Thêm loading skeletons cho UX tốt hơn.
    * Cached leaderboard data để giảm API calls.
    * Triển khai optimistic updates khi rank của user thay đổi.
* Thêm leaderboard features:
    * Real-time rank updates (refetch on interval).
    * User's current rank highlighting (nếu trong top 100).
    * "Your rank" indicator hiển thị vị trí của user.
    * Filter theo game mode (Wave Rush, Type Race).
    * Date range selector cho historical leaderboards.
    * Export leaderboard data (CSV/PDF).
* Triển khai responsive design:
    * Mobile-friendly card layout cho màn hình nhỏ hơn.
    * Collapsible filters trên mobile.
    * Touch-optimized scrolling và interactions.

**Phát triển trang Users (Ngày 3)**
* Thiết kế layout trang Users:
    * Search bar với real-time filtering.
    * Grid layout của user profile cards.
    * Pagination hoặc infinite scroll.
    * Filter options (online status, rank range, skill level).
    * Sort options (by rank, recent activity, WPM).
* Triển khai các Users page components:
    * `UsersPage` (main container):
        * Search bar ở trên cùng.
        * Filter và sort controls.
        * User grid với responsive layout.
    * Component `UserSearchBar`:
        * Search input với debounced queries.
        * Search icon và clear button.
        * Autocomplete dropdown với suggestions.
        * Recent searches history.
    * Component `UserCard`:
        * User avatar với online status indicator.
        * Username và user rank/title.
        * Key stats preview (WPM, accuracy, games).
        * Follow/Unfollow button (nếu có).
        * Link đến detailed profile page.
    * Component `UserFilters`:
        * Online/Offline filter toggle.
        * Rank range slider.
        * Game mode filter.
        * Sort dropdown (WPM, Rank, Recent Activity).
    * Component `UserGrid`:
        * Responsive grid layout (1-4 columns dựa trên kích thước màn hình).
        * Loading states với skeleton cards.
        * Empty state khi không tìm thấy users.

* Tích hợp React Query cho users data:
    * Sử dụng hook `useSearchUsers(query)` với debouncing:
        ```typescript
        const debouncedQuery = useDebounce(searchQuery, 300);
        const { data: users, isLoading } = useSearchUsers(debouncedQuery);
        ```
    * Triển khai pagination với `useInfiniteQuery`:
        * Load more button hoặc infinite scroll.
        * Prefetch next page cho smooth experience.
    * Cached user search results cho instant back navigation.
    * Triển khai filter và sort trên client-side cho cached data.
* Thêm user interaction features:
    * Click vào user card để xem full profile.
    * Challenge user to game (mở game invitation).
    * Xem user statistics và game history.
    * Follow/Unfollow functionality (nếu có social features).
    * Share user profile link.

**Phát triển Landing Page (Ngày 4)**
* Thiết kế cấu trúc Landing page:
    * Hero section với call-to-action.
    * Features showcase section.
    * How it works section.
    * Leaderboard preview.
    * Testimonials hoặc user reviews (tùy chọn).
    * Footer với links và social media.
* Triển khai các Landing page components:
    * `LandingPage` (main container).
    * Component `HeroSection`:
        * Large heading: "Improve Your Typing Speed".
        * Subheading: Mô tả trò chơi Type Rush.
        * Primary CTA button: "Start Playing" (links đến registration/game).
        * Secondary CTA: "View Leaderboard".
        * Background animation hoặc gradient.
        * Hero image hoặc game preview screenshot.
    * Component `FeaturesSection`:
        * Grid của feature cards (3-4 features).
        * Features highlighted:
            * "Multiplayer Racing": Cạnh tranh với người chơi trên toàn thế giới.
            * "Real-time Progress": Xem tiến độ đối thủ trực tiếp.
            * "Multiple Game Modes": Wave Rush và Type Race.
            * "Track Your Progress": Thống kê và lịch sử chi tiết.
        * Icon + title + description cho mỗi feature.
        * Animations on scroll (fade-in, slide-in).
    * Component `HowItWorksSection`:
        * Step-by-step guide (3-4 steps).
        * Steps: 1) Create Account, 2) Join Game, 3) Type Fast, 4) Win!
        * Visual indicators (numbers hoặc icons).
        * Simple illustrations hoặc screenshots.
    * Component `LeaderboardPreview`:
        * "Top Players" heading.
        * Top 5-10 players từ global leaderboard.
        * Mini leaderboard cards.
        * "View Full Leaderboard" link.
    * Component `CTASection`:
        * Final call-to-action trước footer.
        * "Ready to compete?" heading.
        * "Sign Up Now" button.
        * Encouraging copy về việc tham gia community.
    * Component `Footer`:
        * Links: About, Privacy Policy, Terms of Service, Contact.
        * Social media icons (nếu có).
        * Copyright notice.

* Styled Landing page với modern design:
    * Sử dụng Tailwind CSS cho responsive design.
    * Triển khai gradient backgrounds và color schemes.
    * Thêm smooth scroll animations với Framer Motion hoặc CSS transitions.
    * Sử dụng high-quality images và icons.
    * Đảm bảo mobile responsiveness cho tất cả sections.
* Tích hợp dynamic data:
    * Fetched top players cho leaderboard preview sử dụng React Query.
    * Hiển thị live player count (tùy chọn).
    * Hiển thị recent game results hoặc activity feed (tùy chọn).

**Cải tiến trang bổ sung (Ngày 5-6)**
* User Profile Page (nếu chưa được xây dựng):
    * Hiển thị user statistics chi tiết.
    * Game history với filters.
    * Achievements và badges đã kiếm được.
    * Personal records và milestones.
    * Settings và preferences section.
* Game History Page:
    * Filterable game history table.
    * Detailed stats cho mỗi game đã chơi.
    * Replay functionality (nếu có).
    * Export game data.
* Settings Page:
    * Profile settings (avatar, username, email).
    * Game preferences (sound, notifications).
    * Privacy settings.
    * Account management (password change, delete account).

**Tối ưu hóa React Query**
* Triển khai các tính năng React Query nâng cao:
    * Prefetching cho improved navigation speed:
        ```typescript
        queryClient.prefetchQuery(['user', userId], () => fetchUser(userId));
        ```
    * Optimistic updates cho instant UI feedback:
        * Update cache ngay lập tức khi mutation.
        * Rollback on error với previous data.
    * Background refetching cho stale data:
        * Refetch leaderboard mỗi 30 giây.
        * Refetch user stats on window focus.
    * Pagination với `useInfiniteQuery` cho large datasets.
    * Query invalidation strategy:
        * Invalidate related queries khi mutations.
        * Automatic cache cleanup cho unused data.

**Tối ưu hóa hiệu suất**
* Triển khai frontend performance improvements:
    * Code splitting với React.lazy() cho route-based components.
    * Image optimization với lazy loading.
    * Debounced search input để giảm API calls.
    * Memoized expensive computations với useMemo.
    * Virtualized lists cho long leaderboards (react-window).
* Tối ưu hóa React Query configuration:
    * Điều chỉnh stale time dựa trên data volatility.
    * Cấu hình cache time để cân bằng memory và performance.
    * Sử dụng query keys hiệu quả cho granular cache control.
    * Triển khai query placeholders cho instant loading states.

**Sửa lỗi và hoàn thiện (Ngày 6)**
* Sửa các vấn đề được xác định trong testing:
    * WebSocket reconnection edge cases.
    * Race conditions trong game state updates.
    * Leaderboard ranking calculation bugs.
    * Mobile layout issues trên màn hình nhỏ.
    * Form validation errors.
* Cải tiến UI/UX:
    * Thêm loading states cho tất cả async operations.
    * Cải thiện error messages và user feedback.
    * Nâng cao animations cho smoother transitions.
    * Tinh chỉnh color scheme và typography.
    * Cải thiện accessibility (keyboard navigation, ARIA labels).
* Code refactoring:
    * Trích xuất reusable components.
    * Dọn dẹp unused code và imports.
    * Cải thiện TypeScript types cho better type safety.
    * Thêm JSDoc comments cho complex functions.
    * Tổ chức folder structure để maintainability tốt hơn.

**Kiểm tra và xác thực**
* Thực hiện comprehensive testing:
    * Kiểm tra tất cả API integrations với React Query.
    * Xác minh cache behavior và invalidation.
    * Kiểm tra pagination và infinite scroll.
    * Xác thực search và filter functionality.
    * Kiểm tra responsive design trên các devices.
    * Kiểm tra accessibility với screen readers.
* Performance testing:
    * Đo page load times.
    * Kiểm tra memory leaks trong React components.
    * Xác thực WebSocket connection stability.
    * Kiểm tra với slow network conditions.

**Documentation và chất lượng Code**
* Cải thiện code documentation:
    * Thêm README cho API routes structure.
    * Tài liệu hóa React Query hooks usage.
    * Tạo component documentation với props và examples.
    * Thêm inline comments cho complex logic.
* Thiết lập coding standards:
    * Consistent naming conventions.
    * TypeScript strict mode enabled.
    * ESLint và Prettier configuration.
    * Git commit message conventions.

**Những điểm rút ra chính**
* React Query benefits đã nhận ra:
    * Đơn giản hóa data fetching và state management.
    * Built-in caching cải thiện đáng kể performance.
    * Automatic background refetching giữ data fresh.
    * Optimistic updates nâng cao user experience.
    * DevTools giúp debug query issues dễ dàng.
* Cải tiến frontend architecture:
    * Modular API client structure cải thiện maintainability.
    * Custom hooks thúc đẩy code reuse.
    * Separation of concerns làm components sạch hơn.
    * TypeScript bắt được nhiều potential bugs sớm.