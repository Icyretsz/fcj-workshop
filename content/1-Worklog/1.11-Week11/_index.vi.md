---
title: "Worklog Tuần 11"
date: "2025-09-15"
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11:

* Phát triển frontend cho Type Rush (Dự án cuối kỳ FCJ)
* Triển khai UI chế độ multiplayer Wave Rush và tích hợp WebSocket
* Xây dựng real-time communication với game-service microservice

### Các nhiệm vụ cần thực hiện trong tuần này:
| Ngày | Nhiệm vụ                                                                             | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo                                                                                                                            |
| --- |----------------------------------------------------------------------------------|------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| 2   | - Thiết kế kiến trúc frontend Wave Rush<br/>- Lập kế hoạch cấu trúc WebSocket event với game-service | 10/11/2025 | 10/11/2025 | <https://socket.io/docs/v4/client-api/> |
| 3   | - Triển khai các UI components Wave Rush<br/>- Xây dựng game state management | 11/11/2025 | 11/11/2025 | |
| 4   | - Phát triển tích hợp WebSocket cho Wave Rush<br/>- Kiểm tra đồng bộ hóa trò chơi real-time với game-service | 12/11/2025 | 12/11/2025 | |
| 5   | - Thiết kế kiến trúc frontend Type Race<br/>- Lập kế hoạch UI flow cho single-race | 13/11/2025 | 13/11/2025 | |
| 6   | - Triển khai các UI components Type Race<br/>- Xây dựng tích hợp WebSocket cho Type Race | 14/11/2025 | 14/11/2025 | |


### Thành tựu tuần 11:

**Dự án cuối kỳ FCJ: Type Rush - Tổng quan phát triển Frontend**
* Frontend trò chơi gõ phím multiplayer Type Rush:
    * Thiết kế giao diện người dùng trò chơi gõ phím multiplayer real-time toàn diện.
    * Lập kế hoạch hai chế độ chơi riêng biệt: Wave Rush và Type Race.
    * Thiết kế kiến trúc tích hợp WebSocket frontend với game-service microservice.
    * Tạo game state management có khả năng mở rộng với React hooks.
* Technology stack cho frontend:
    * Frontend framework: React với TypeScript cho type safety.
    * Real-time communication: Socket.IO client cho WebSocket connection.
    * State management: React Context API và custom hooks.
    * UI framework: Tailwind CSS cho responsive styling.
    * Game-service: Microservice riêng biệt xử lý tất cả WebSocket events và multiplayer game logic.

**Chế độ chơi Wave Rush - Kiến trúc Frontend (Ngày 1)**
* Thiết kế cấu trúc frontend Wave Rush:
    * UI cuộc đua gõ phím nhiều vòng với hiển thị độ khó tăng dần.
    * Giao diện gameplay dựa trên wave nơi người chơi cạnh tranh qua nhiều vòng.
    * Hiển thị tiến độ người chơi real-time với thanh progress bar có animation.
    * Layout responsive cho phòng chơi multiplayer (2-8 người chơi).
* Lập kế hoạch game states và screens frontend:
    * Lobby screen: Hiển thị room code, danh sách người chơi, nút ready-up.
    * Countdown screen: Animation đếm ngược trước trò chơi (3-2-1-GO).
    * Wave active screen: Giao diện gõ phím với text display và input field.
    * Wave results screen: Kết quả vòng, xếp hạng người chơi, xem trước wave tiếp theo.
    * Game complete screen: Bảng xếp hạng cuối cùng và thống kê.
* Thiết kế cấu trúc WebSocket event với game-service:
    * Client → Server events: `join_room`, `player_ready`, `typing_progress`, `wave_complete`.
    * Server → Client events: `room_updated`, `game_starting`, `wave_started`, `player_progress_update`, `wave_ended`, `game_finished`.
* Tạo component hierarchy:
    * `WaveRushPage` (parent container)
    * `WaveRushLobby` (lobby và room management)
    * `WaveRushGame` (gameplay đang hoạt động)
    * `PlayerList` (trạng thái người chơi real-time)
    * `TypingArea` (text display và input)
    * `ProgressBar` (hiển thị tiến độ người chơi)
    * `WaveResults` (kết quả giữa các wave)
    * `FinalResults` (màn hình hoàn thành trò chơi)

**Triển khai Frontend Wave Rush (Ngày 2-3)**
* Xây dựng các React components Wave Rush:
    * Tạo component `WaveRushLobby`:
        * Hiển thị room code với chức năng copy-to-clipboard.
        * Danh sách người chơi động hiển thị trạng thái join và ready.
        * Nút Ready/Unready với state management.
        * Nút start game (chỉ hiển thị cho room host).
    * Phát triển component `WaveRushGame`:
        * Hiển thị số wave và timer.
        * Text display với highlighting từng ký tự.
        * Input field với character validation và error highlighting.
        * Hiển thị tính toán WPM và accuracy trực tiếp.
    * Triển khai component `PlayerList`:
        * Các player status cards real-time với avatars.
        * Progress bars hiển thị completion percentage.
        * Hiển thị WPM và accuracy cho mỗi người chơi.
        * Hiển thị ranking hiện tại trong wave đang hoạt động.
    * Xây dựng component `TypingArea`:
        * Text display monospace với styling cho ký tự đúng/sai.
        * Cursor position indicator.
        * Input field với auto-focus và ngăn chặn copy-paste.
        * Character validation từng ký tự với visual feedback.
    * Tạo component `WaveResults`:
        * Bảng ranking có animation với thay đổi vị trí.
        * Thống kê wave riêng lẻ (WPM, accuracy, time).
        * Countdown timer "Next wave in...".
        * Hiển thị tích lũy points/score.

**Tích hợp WebSocket cho Wave Rush (Ngày 3)**
* Thiết lập Socket.IO client connection:
    * Tạo custom React hook `useGameSocket` cho WebSocket management.
    * Triển khai connection state handling (connecting, connected, disconnected).
    * Thêm automatic reconnection logic với exponential backoff.
    * Quản lý socket lifecycle với cleanup đúng cách trong useEffect.
* Triển khai client-side event emitters:
    * `join_room(roomCode, playerData)`: Gửi room code và player info khi join.
    * `player_ready(isReady)`: Emit trạng thái ready/unready trong lobby.
    * `typing_progress(progress)`: Gửi typing progress real-time với throttling.
        * Progress data: ký tự đã gõ, vị trí hiện tại, WPM, accuracy, completion percentage.
        * Throttled đến mỗi 100ms để giảm network load.
    * `leave_room()`: Xử lý người chơi rời đi một cách graceful.
* Triển khai client-side event listeners:
    * `room_updated(roomData)`: Cập nhật UI với room state hiện tại.
        * Cập nhật player list với join/leave events.
        * Cập nhật ready statuses cho tất cả người chơi.
        * Enable/disable nút start dựa trên ready count.
    * `game_starting(countdown)`: Kích hoạt countdown animation.
        * Ẩn lobby UI, hiển thị countdown overlay.
        * Phát countdown sound effects.
    * `wave_started(waveData)`: Khởi tạo wave.
        * Hiển thị wave text và wave number.
        * Bắt đầu wave timer countdown.
        * Enable typing input field.
        * Reset player progress bars.
    * `player_progress_update(playerId, progress)`: Cập nhật tiến độ đối thủ.
        * Cập nhật progress bar cho người chơi cụ thể.
        * Cập nhật hiển thị WPM và accuracy.
        * Tính toán lại và cập nhật live rankings.
    * `wave_ended(results)`: Hiển thị kết quả wave.
        * Hiển thị wave results component với animations.
        * Cập nhật cumulative scores/rankings.
        * Bắt đầu countdown đến wave tiếp theo.
    * `game_finished(finalResults)`: Hiển thị bảng xếp hạng cuối cùng.
        * Hiển thị final rankings với podium animation.
        * Hiển thị game statistics và achievements.
        * Enable nút rematch hoặc leave room.
    * `error(errorData)`: Xử lý và hiển thị error messages.
        * Hiển thị error notifications (room full, invalid room code).
        * Xử lý disconnection errors một cách graceful.

**Chế độ chơi Type Race - Kiến trúc Frontend (Ngày 4)**
* Thiết kế cấu trúc frontend Type Race:
    * Giao diện cuộc đua gõ phím single-round nhanh.
    * Lobby matchmaking đơn giản hóa với auto-start.
    * Tiến độ đối thủ real-time với race track visualization.
    * Màn hình kết quả nhanh với animation celebration người thắng.
* Lập kế hoạch Type Race game flow:
    * Queue screen: Người chơi join queue và được auto-matched.
    * Found match screen: Hiển thị người chơi đã matched (2-4 người chơi).
    * Countdown: Countdown ngắn trước race bắt đầu (3 giây).
    * Race: Thử thách gõ phím single với live rankings và progress.
    * Results: Thông báo người thắng, performance stats, rematch option.
* Thiết kế Type Race WebSocket events:
    * Client → Server: `join_queue`, `leave_queue`, `typing_progress`, `race_complete`.
    * Server → Client: `queue_joined`, `match_found`, `race_starting`, `race_started`, `player_update`, `race_finished`.
* Tạo cấu trúc Type Race component:
    * `TypeRacePage` (parent container)
    * `TypeRaceQueue` (matchmaking queue)
    * `TypeRaceCountdown` (pre-race countdown)
    * `TypeRaceGame` (race đang hoạt động)
    * `RaceTrack` (race track trực quan với vị trí player cars)
    * `TypeRaceResults` (race results)

**Triển khai Frontend Type Race (Ngày 5-6)**
* Xây dựng các React components Type Race:
    * Tạo component `TypeRaceQueue`:
        * Queue animation với "searching for players" indicator.
        * Hiển thị estimated wait time.
        * Nút cancel queue.
        * Hiển thị player count trong queue (tùy chọn).
    * Phát triển component `TypeRaceGame`:
        * Race track visualization với player avatars/icons.
        * Horizontal progress bars hoặc race track positions.
        * Text display với character highlighting.
        * Input field với validation.
        * Live rankings sidebar.
    * Triển khai component `RaceTrack`:
        * Animated player positions dựa trên progress.
        * Smooth transitions khi người chơi tiến lên.
        * Position indicators (1st, 2nd, 3rd).
        * Finish line animation khi người chơi hoàn thành.
    * Xây dựng component `TypeRaceResults`:
        * Winner announcement với celebration animation.
        * Final rankings với player stats.
        * Performance comparison (WPM của bạn vs người khác).
        * Nút rematch và return to queue.

**Tích hợp WebSocket cho Type Race (Ngày 6)**
* Triển khai tích hợp Socket.IO client cho Type Race:
    * Tái sử dụng hook `useGameSocket` với Type Race events.
    * Triển khai client-side event emitters:
        * `join_queue(playerData)`: Join matchmaking queue.
        * `leave_queue()`: Thoát queue trước khi tìm thấy match.
        * `typing_progress(progress)`: Gửi race progress (giống Wave Rush).
        * `race_complete(finalTime)`: Thông báo race completion.
    * Triển khai client-side event listeners:
        * `queue_joined()`: Xác nhận queue entry, hiển thị queue screen.
        * `match_found(matchData)`: Hiển thị người chơi đã matched, chuẩn bị cho race.
        * `race_starting(countdown)`: Hiển thị countdown animation.
        * `race_started(raceData)`: Khởi tạo race với text và timer.
        * `player_update(playerId, progress)`: Cập nhật vị trí đối thủ real-time.
        * `player_finished(playerId, finalTime)`: Đánh dấu người chơi đã hoàn thành, hiển thị completion.
        * `race_finished(results)`: Hiển thị kết quả cuối cùng và rankings.
        * `queue_cancelled()`: Xử lý queue cancellation hoặc timeout.

**Frontend State Management và tối ưu hóa**
* Triển khai state management hiệu quả:
    * Sử dụng React Context cho global game state (phòng hiện tại, danh sách người chơi, trạng thái trò chơi).
    * Tạo custom hooks để tách biệt game logic:
        * `useWaveRush()`: Quản lý Wave Rush game state và logic.
        * `useTypeRace()`: Quản lý Type Race game state và logic.
        * `useTypingInput()`: Xử lý typing validation và progress calculation.
    * Triển khai local component state cho các vấn đề chỉ liên quan đến UI.
* Tối ưu hóa hiệu suất:
    * Memoized các calculations tốn kém (WPM, accuracy) với useMemo.
    * Tối ưu hóa re-renders với React.memo cho player cards.
    * Throttled typing progress updates đến game-service (100ms intervals).
    * Debounced input validation cho trải nghiệm người dùng mượt mà.
    * Sử dụng CSS transitions cho smooth animations thay vì JavaScript.

**Cải tiến UI/UX**
* Thêm visual feedback và animations:
    * Smooth transitions giữa các game states.
    * Typing animation với character highlighting.
    * Progress bar animations với color transitions.
    * Countdown animations với sound effects (tùy chọn).
    * Winner celebration animations trong results screen.
* Triển khai responsive design:
    * Mobile-friendly layout cho tất cả game screens.
    * Touch-optimized input cho mobile devices.
    * Responsive player list (grid layout thích ứng với kích thước màn hình).
    * Hỗ trợ chế độ portrait và landscape.
* Thêm accessibility features:
    * Hỗ trợ keyboard navigation.
    * Focus management cho input fields.
    * ARIA labels cho screen readers.
    * Hỗ trợ high contrast mode.

**Kiểm tra và Debugging**
* Frontend testing được thực hiện:
    * Kiểm tra WebSocket connection và reconnection scenarios.
    * Xác minh đồng bộ hóa real-time với nhiều browser tabs.
    * Kiểm tra typing input validation và edge cases.
    * Xác minh độ chính xác progress calculation (WPM, accuracy).
    * Kiểm tra UI responsiveness trên các kích thước màn hình khác nhau.
* Xử lý edge cases:
    * Network disconnection trong active game.
    * Xử lý người chơi rời giữa trò chơi.
    * Error handling cho invalid room code.
    * Scenarios khi game service không khả dụng.
    * Race condition handling trong state updates.

**Thách thức và Giải pháp**
* Các thách thức chính đã giải quyết:
    * WebSocket reconnection: Triển khai automatic reconnection với state recovery.
    * Real-time sync delays: Thêm optimistic UI updates cho trải nghiệm mượt mà hơn.
    * Input lag: Tối ưu hóa typing validation để chạy synchronously.
    * State management complexity: Tách riêng concerns với custom hooks.
    * Mobile keyboard issues: Sử dụng input types phù hợp và xử lý focus cẩn thận.

### Mục tiêu tuần 11:

* Kết nối, làm quen với các thành viên trong First Cloud Journey.
* Hiểu dịch vụ AWS cơ bản, cách dùng console & CLI.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Làm quen với các thành viên FCJ <br> - Đọc và lưu ý các nội quy, quy định tại đơn vị thực tập                                                                                             | 11/08/2025   | 11/08/2025      |
| 3   | - Tìm hiểu AWS và các loại dịch vụ <br>&emsp; + Compute <br>&emsp; + Storage <br>&emsp; + Networking <br>&emsp; + Database <br>&emsp; + ... <br>                                            | 12/08/2025   | 12/08/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tạo AWS Free Tier account <br> - Tìm hiểu AWS Console & AWS CLI <br> - **Thực hành:** <br>&emsp; + Tạo AWS account <br>&emsp; + Cài AWS CLI & cấu hình <br> &emsp; + Cách sử dụng AWS CLI | 13/08/2025   | 13/08/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu EC2 cơ bản: <br>&emsp; + Instance types <br>&emsp; + AMI <br>&emsp; + EBS <br>&emsp; + ... <br> - Các cách remote SSH vào EC2 <br> - Tìm hiểu Elastic IP   <br>                  | 14/08/2025   | 15/08/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành:** <br>&emsp; + Tạo EC2 instance <br>&emsp; + Kết nối SSH <br>&emsp; + Gắn EBS volume                                                                                         | 15/08/2025   | 15/08/2025      | <https://cloudjourney.awsstudygroup.com/> |


### Kết quả đạt được tuần 11:

* Hiểu AWS là gì và nắm được các nhóm dịch vụ cơ bản: 
  * Compute
  * Storage
  * Networking 
  * Database
  * ...

* Đã tạo và cấu hình AWS Free Tier account thành công.

* Làm quen với AWS Management Console và biết cách tìm, truy cập, sử dụng dịch vụ từ giao diện web.

* Cài đặt và cấu hình AWS CLI trên máy tính bao gồm:
  * Access Key
  * Secret Key
  * Region mặc định
  * ...

* Sử dụng AWS CLI để thực hiện các thao tác cơ bản như:

  * Kiểm tra thông tin tài khoản & cấu hình
  * Lấy danh sách region
  * Xem dịch vụ EC2
  * Tạo và quản lý key pair
  * Kiểm tra thông tin dịch vụ đang chạy
  * ...

* Có khả năng kết nối giữa giao diện web và CLI để quản lý tài nguyên AWS song song.
* ...


