import heapq
from collections import deque

def bos_greedy_schedule(V, E, p, r, delta, m):
    """
    BOS-Greedy 排程演算法實現
    輸入參數：
    - V: 任務節點列表
    - E: 優先順序邊列表 (i, j 表示 i -> j)
    - p: 任務處理時間的字典，如 p[i] 是任務 i 的處理時間
    - r: 任務發布時間的字典，如 r[i] 是任務 i 的最早可開始時間
    - delta: 邊重疊參數的字典，如 delta[(i,j)] = δ_{ij}
    - m: 機器數量 (同時最多可執行任務數)
    輸出：
    - schedule: 字典，包含每個任務的開始時間 'start' 和完成時間 'finish'
    """
    # 第1步：計算每個任務的 δ 修正關鍵餘長 D[i]
    # 建立任務的前序和後繼字典
    succ = {i: [] for i in V}
    pred = {i: [] for i in V}
    for (i, j) in E:
        succ[i].append(j)
        pred[j].append(i)
    # 拓撲排序（使用 Kahn 演算法）以便自後向前計算 D 值
    indeg = {i: 0 for i in V}
    for (i, j) in E:
        indeg[j] += 1

    Q = deque([i for i in V if indeg[i] == 0])
    topo_order = []
    while Q:
        u = Q.popleft()
        topo_order.append(u)
        for j in succ[u]:
            indeg[j] -= 1
            if indeg[j] == 0:
                Q.append(j)
    # 初始化 D 值為自身處理時間，然後反向拓撲順序動態規劃計算
    D = {i: p[i] for i in V}
    for i in reversed(topo_order):
        if succ[i]:
            # 對每個後繼任務 j，計算 (D[j] - δ_{ij})，取最大者
            max_tail = 0
            for j in succ[i]:
                d_ij = delta.get((i, j), 0)    # 若無定義則視為0
                tail_len = D[j] - d_ij         # 考慮從 i 到 j 的有效餘長
                if tail_len > max_tail:
                    max_tail = tail_len
            D[i] = p[i] + max_tail

    # 第2步：初始化資料結構
    current_time = 0
    # remain: 任務剩餘處理時間（初始化為總加工時間 p[i]）
    remain = {i: p[i] for i in V}
    # 記錄排程結果的字典
    schedule = {i: {"start": None, "finish": None} for i in V}
    # block_count: 每個任務尚有多少前置未解除阻塞（未滿足重疊啟動條件）的計數
    block_count = {j: 0 for j in V}
    for j in V:
        for i in pred.get(j, []):
            # 若任務 i 需要執行到剩餘時間 <= δ_{ij} 才能解除對 j 的阻塞，則初始時算作一個阻塞
            if p[i] > delta.get((i, j), 0):
                block_count[j] += 1

    # 用兩個堆維護就緒任務和運行任務：
    ready_heap = []        # 最大堆（Python無直接max堆，透過存入負D實現）
    running_min_heap = []  # 最小堆（按照任務優先值 D），方便取得最低優先的運行任務
    running_tasks = {}     # 當前運行中的任務 -> 剩餘處理時間
    # 用集合快速判斷任務是否在就緒或運行狀態
    in_ready = set()
    in_running = set()

    # 初始將所有無前置阻塞的任務（block_count為0）且已達到發布時間的任務加入就緒池
    for j in V:
        if block_count[j] == 0 and r.get(j, 0) <= current_time:
            heapq.heappush(ready_heap, (-D[j], j))
            in_ready.add(j)

    # 輔助函式：開始執行一個任務（將其從就緒移到運行）
    def start_task(task):
        in_ready.discard(task)
        in_running.add(task)
        running_tasks[task] = remain[task]  # 將任務剩餘時間加入運行追蹤
        # 如該任務首次開始，記錄開始時間
        if schedule[task]["start"] is None:
            schedule[task]["start"] = current_time
        # 將任務按優先值 D 推入運行最小堆
        heapq.heappush(running_min_heap, (D[task], task))

    # 輔助函式：確保運行任務不超過 m 個（如超出則搶佔優先值最低者）
    def enforce_running_limit():
        while len(running_tasks) > m:
            # 取出當前運行中優先值最低的任務
            lowest_D, low_task = heapq.heappop(running_min_heap)
            if low_task in running_tasks:
                # 將其從運行中移除並記錄剩餘時間
                in_running.remove(low_task)
                remaining_time = running_tasks.pop(low_task)
                remain[low_task] = remaining_time  # 更新該任務剩餘處理時間
                # 若該任務尚未完成（剩餘時間 > 0），放回就緒池等待之後再次調度
                if remaining_time > 0:
                    heapq.heappush(ready_heap, (-D[low_task], low_task))
                    in_ready.add(low_task)
                # (如果 remaining_time == 0，表示任務正好完成，不需重新入隊)

    # 調度主迴圈：持續執行直到所有任務完成
    completed = set()  # 已完成任務集合
    while len(completed) < len(V):
        # 2.1 步驟：分配空閒機器給就緒任務
        while ready_heap and len(running_tasks) < m:
            # 取出優先權最高的就緒任務啟動
            _, task = heapq.heappop(ready_heap)
            if task in completed:
                continue  # 略過已完成任務（保險起見，正常情況不會發生）
            in_ready.discard(task)
            start_task(task)

        # 2.2 步驟：如果沒有任務可以立刻啟動（機器可能全忙或無就緒任務），則準備推進時間
        if not running_tasks:
            # 無任務運行，則將時間跳到下一個將發生任務發布的時間點
            next_release = None
            for j in V:
                if j not in completed and j not in running_tasks and j not in in_ready:
                    rel_j = r.get(j, 0)
                    if rel_j > current_time:
                        if next_release is None or rel_j < next_release:
                            next_release = rel_j
            if next_release is None:
                break  # 沒有後續事件則跳出
            current_time = next_release
            # 將所有在該時間之前發布且無阻塞的任務加入就緒
            for j in V:
                if j not in completed and j not in running_tasks and j not in in_ready:
                    if r.get(j, 0) <= current_time and block_count[j] == 0:
                        heapq.heappush(ready_heap, (-D[j], j))
                        in_ready.add(j)
            continue  # 重新檢查就緒任務填充機器

        # 2.3 步驟：確定下一個事件時間（任務完成、δ 閾值達成、或任務發布）
        # 計算所有運行任務中最早完成的時間
        next_finish_time = None
        finish_tasks = []  # 將在next_finish_time完成的任務列表（可能同時完成多個）
        for task, rem_time in running_tasks.items():
            finish_time = current_time + rem_time
            if next_finish_time is None or finish_time < next_finish_time:
                next_finish_time = finish_time
                finish_tasks = [task]
            elif finish_time == next_finish_time:
                finish_tasks.append(task)
        # 計算下一個 δ 阻塞解除（閾值達成）的時間
        next_thresh_time = None
        thresh_events = []  # (pred_i, succ_j) 在該時間解除阻塞的事件列表
        for i in list(running_tasks.keys()):
            rem_time = running_tasks[i]
            # 對每個仍被 i 阻塞的後繼任務 j，計算 i 在何時餘下 δ_{ij}
            for j in succ.get(i, []):
                if j in completed:
                    continue  # 已完成任務不需考慮
                if block_count[j] > 0:
                    d_ij = delta.get((i, j), 0)
                    if rem_time > d_ij:
                        # i 尚未進入重疊階段，將在 rem_time - d_ij 後達到閾值
                        thresh_time = current_time + (rem_time - d_ij)
                        if next_thresh_time is None or thresh_time < next_thresh_time:
                            next_thresh_time = thresh_time
                            thresh_events = [(i, j)]
                        elif thresh_time == next_thresh_time:
                            thresh_events.append((i, j))
        # 計算下一個未發布任務的發布時間
        next_release = None
        release_tasks = []
        for j in V:
            if j not in completed and j not in running_tasks and j not in in_ready:
                rel_j = r.get(j, 0)
                if rel_j > current_time:
                    if next_release is None or rel_j < next_release:
                        next_release = rel_j
                        release_tasks = [j]
                    elif rel_j == next_release:
                        release_tasks.append(j)
        # 取得上述三類事件中最早的事件時間
        event_times = [t for t in [next_finish_time, next_thresh_time, next_release] if t is not None]
        next_event_time = min(event_times) if event_times else None
        if next_event_time is None:
            break  # 無事件則跳出
        # 將時間推進到 next_event_time
        time_passed = next_event_time - current_time
        # 扣除所有運行任務的剩餘時間
        for task in list(running_tasks.keys()):
            running_tasks[task] -= time_passed
            if running_tasks[task] < 1e-9:  # 考慮浮點誤差，小於極小值視為0
                running_tasks[task] = 0.0
        current_time = next_event_time

        # 3.1 處理任務完成事件
        if next_finish_time is not None and abs(current_time - next_finish_time) < 1e-9:
            for task in finish_tasks:
                if task in running_tasks and running_tasks[task] == 0:
                    # 任務 task 完成
                    completed.add(task)
                    in_running.discard(task)
                    running_tasks.pop(task, None)
                    schedule[task]["finish"] = current_time  # 記錄完成時間
                    # 更新其後繼任務的阻塞計數
                    for j in succ.get(task, []):
                        if j in completed:
                            continue
                        if block_count[j] > 0:
                            block_count[j] -= 1
                            if block_count[j] < 0:
                                block_count[j] = 0
                        # 如解除所有阻塞且已發布，則將後繼任務加入就緒
                        if block_count[j] == 0 and r.get(j, 0) <= current_time:
                            if j not in in_ready and j not in running_tasks:
                                heapq.heappush(ready_heap, (-D[j], j))
                                in_ready.add(j)
        # 3.2 處理 δ 閾值（重疊啟動）事件
        if next_thresh_time is not None and abs(current_time - next_thresh_time) < 1e-9:
            for (i, j) in thresh_events:
                if i in running_tasks and j not in completed:
                    # 前置任務 i 現在剩餘時間 <= δ_{ij}，解除對 j 的阻塞
                    if block_count[j] > 0:
                        block_count[j] -= 1
                        if block_count[j] < 0:
                            block_count[j] = 0
                    # 如果 j 所有前置均已解除阻塞，且已到達發布時間，加入就緒
                    if block_count[j] == 0 and r.get(j, 0) <= current_time:
                        if j not in in_ready and j not in running_tasks:
                            heapq.heappush(ready_heap, (-D[j], j))
                            in_ready.add(j)
        # 3.3 處理任務發布事件
        if next_release is not None and abs(current_time - next_release) < 1e-9:
            for j in release_tasks:
                if j not in completed:
                    # 任務 j 現在發布，若無阻塞則進入就緒
                    if block_count[j] == 0:
                        if j not in in_ready and j not in running_tasks:
                            heapq.heappush(ready_heap, (-D[j], j))
                            in_ready.add(j)

        # 3.4 更新就緒/運行集合並處理可能的搶佔
        # 釋放的機器數 = 剛完成的任務數，可立即啟動相同數量的新就緒任務
        while ready_heap and len(running_tasks) < m:
            _, task = heapq.heappop(ready_heap)
            if task in completed:
                continue  # 跳過已完成任務（以防重複加入的情況）
            in_ready.discard(task)
            start_task(task)
        # 若運行任務數超過 m，搶佔多餘的任務
        if len(running_tasks) > m:
            enforce_running_limit()
        # 檢查是否存在就緒任務優先級高於當前運行任務的最低優先級，如是則進行搶佔
        if ready_heap and running_tasks:
            # 取得就緒池中最高優先的任務 D 值（取負存儲，因此取反）
            top_ready_D = -ready_heap[0][0]
            # 取得當前運行中最低優先的任務 D 值
            # 注意需跳過已經完成或不在運行集合中的任務（運行最小堆可能含過期項）
            while running_min_heap and running_min_heap[0][1] not in running_tasks:
                heapq.heappop(running_min_heap)
            if running_min_heap:
                lowest_running_D, lowest_task = running_min_heap[0]
                if top_ready_D > lowest_running_D:
                    # 將優先值最低的運行任務搶佔下線
                    heapq.heappop(running_min_heap)  # 移除該運行任務
                    if lowest_task in running_tasks:
                        in_running.remove(lowest_task)
                        remaining_time = running_tasks.pop(lowest_task)
                        remain[lowest_task] = remaining_time
                        if remaining_time > 0:
                            heapq.heappush(ready_heap, (-D[lowest_task], lowest_task))
                            in_ready.add(lowest_task)
                    # 啟動優先值最高的就緒任務
                    _, high_task = heapq.heappop(ready_heap)
                    in_ready.discard(high_task)
                    start_task(high_task)
                    # 確保運行任務數不超限
                    if len(running_tasks) > m:
                        enforce_running_limit()
                    # 檢查是否需要進一步搶佔（迴圈檢查下一個最高就緒對當前最低運行）
                    while ready_heap:
                        # 清理運行最小堆中可能失效的項
                        while running_min_heap and running_min_heap[0][1] not in running_tasks:
                            heapq.heappop(running_min_heap)
                        if not running_min_heap:
                            break
                        top_ready_D = -ready_heap[0][0]
                        lowest_running_D, lowest_task = running_min_heap[0]
                        if top_ready_D > lowest_running_D:
                            heapq.heappop(running_min_heap)
                            if lowest_task in running_tasks:
                                in_running.remove(lowest_task)
                                remaining_time = running_tasks.pop(lowest_task)
                                remain[lowest_task] = remaining_time
                                if remaining_time > 0:
                                    heapq.heappush(ready_heap, (-D[lowest_task], lowest_task))
                                    in_ready.add(lowest_task)
                            _, high_task = heapq.heappop(ready_heap)
                            in_ready.discard(high_task)
                            start_task(high_task)
                            if len(running_tasks) > m:
                                enforce_running_limit()
                        else:
                            break

    return schedule

# （本函式返回每個任務的開始與完成時間，如需查看調度過程，可額外打印狀態）
