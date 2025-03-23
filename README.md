# ai-practice
import heapq

class Puzzle2x5:
    def __init__(self, initial, goal):
        self.initial = tuple(map(tuple, initial))
        self.goal = tuple(map(tuple, goal))
        self.rows = 2
        self.cols = 5

    def find_blank(self, state):
        for r in range(self.rows):
            for c in range(self.cols):
                if state[r][c] == 0:
                    return r, c
        return None

    def get_neighbors(self, state):
        r, c = self.find_blank(state)
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
        neighbors = []
        
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < self.rows and 0 <= nc < self.cols:
                new_state = [list(row) for row in state]
                new_state[r][c], new_state[nr][nc] = new_state[nr][nc], new_state[r][c]
                neighbors.append(tuple(map(tuple, new_state)))
        
        return neighbors

    def manhattan_distance(self, state):
        distance = 0
        for r in range(self.rows):
            for c in range(self.cols):
                val = state[r][c]
                if val != 0:
                    goal_r, goal_c = divmod(self.goal.index(val), self.cols)
                    distance += abs(r - goal_r) + abs(c - goal_c)
        return distance

    def solve(self):
        pq = []
        heapq.heappush(pq, (0, self.initial, []))
        visited = set()
        
        while pq:
            _, current, path = heapq.heappop(pq)
            if current == self.goal:
                return path

            if current in visited:
                continue
            visited.add(current)

            for neighbor in self.get_neighbors(current):
                if neighbor not in visited:
                    new_path = path + [neighbor]
                    heapq.heappush(pq, (len(new_path) + self.manhattan_distance(neighbor), neighbor, new_path))

        return None

# 2x5 퍼즐 초기 상태 및 목표 상태
initial_state_2x5 = [[2, 1, 9, 4, 7],
                      [0, 5, 3, 6, 8]]

goal_state_2x5 = [[1, 2, 3, 4, 5],
                  [6, 7, 8, 9, 0]]

puzzle_2x5 = Puzzle2x5(initial_state_2x5, goal_state_2x5)
solution_2x5 = puzzle_2x5.solve()

if solution_2x5:
    print("Solution found in", len(solution_2x5), "steps!")
else:
    print("No solution found.")
