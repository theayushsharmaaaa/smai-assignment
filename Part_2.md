# The Elemental Maze: A State Space Search Problem

- **Course**: Search Methods in AI
- **Assignment 1 Part 2**: State Space Search Representation
- **Submitted by**: Ayush Sharma (U20220024) and Manjree Kothari (U20220104)

## Introduction
The Elemental Maze is a challenging puzzle that serves as a platform for exploring and comparing different state space search algorithms. In this report, we delve into the implementation and analysis of three fundamental search strategies: Breadth First Search (BFS), Depth First Search (DFS), and Best First Search. Each of these algorithms offers unique approaches to navigating the maze, moving elemental orbs to their respective goal positions while adhering to the constraints of the puzzle.

Our objective is to provide a comprehensive understanding of how these search algorithms operate within the context of the Elemental Maze problem. We will examine their implementations, step through example scenarios, and compare their performance characteristics. This analysis will shed light on the strengths and weaknesses of each approach, offering insights into which algorithm might be most suitable under various circumstances.

## Breadth First Search/;
Implementation:
The BFS class inherits from ElementalMazeSearch and implements the search method. Here's how it works:
a. Initialization:
  - A queue is initialized with the starting orb positions.
  - A visited set is used to keep track of explored states.
  - A parent dictionary is used to reconstruct the path.

b. Main Loop:
While the queue is not empty:
  - Dequeue the next state (current_positions).
  - Check if it's the goal state using isGoal().
  - If it's the goal, reconstruct and return the path.
  - If not, generate all possible next states using moveGen().
  - For each new state:
    - If not visited, add to the queue, mark as visited, and update the parent.

c. Path Reconstruction:
  - If a goal is found, backtrack using the parent dictionary to construct the path.

## Depth First Search:
Implementation:
The DFS class, like the BFS class, inherits from ElementalMazeSearch and implements the search method. Here's how it works:
1. Initialization:
  - A stack is initialized with the starting orb positions.
  - A visited set is used to keep track of explored states.
  - A parent dictionary is used to reconstruct the path.

2. Main Loop:
While the stack is not empty:
  - Pop the next state (current_positions) from the stack.
  - Check if it's the goal state using isGoal().
  - If it's the goal, reconstruct and return the path.
  - If not, generate all possible next states using moveGen().
  - For each new state:
    - If not visited, add to the stack, mark as visited, and update the parent.

3. Path Reconstruction:
   - If a goal is found, backtrack using the parent dictionary to construct the path.

## Best First Search:
Implementation:
The BestFirstSearch class inherits from ElementalMazeSearch and implements the search method along with a heuristic function. Here's how it works:
1. Heuristic Function:
  - The heuristic() method calculates the estimated cost to reach the goal from a given state.
  - It sums the Manhattan distances between each orb and its corresponding goal.

2. Initialization:
  - A priority queue (pq) is initialized with the starting orb positions, prioritized by the heuristic value.
  - A visited set is used to keep track of explored states.
  - A parent dictionary is used to reconstruct the path.
  - A counter is used to break ties in the priority queue.

3. Main Loop:
While the priority queue is not empty:
  - Pop the state with the lowest heuristic value (current_positions).
  - Check if it's the goal state using isGoal().
  - If it's the goal, reconstruct and return the path.
  - If not, generate all possible next states using moveGen().
  - For each new state:
    - If not visited, calculate its heuristic value, add to the priority queue, mark as visited, and update the parent.

4. Path Reconstruction:
  - If a goal is found, backtrack using the parent dictionary to construct the path.


## Example Implementation:
## Example 1:
```python
maze = [
    ['#', '#', '#', '#', '#', '#', '#'],
    ['#', '.', '.', '.', '.', 'V', '#'],
    ['#', '.', '#', '#', '#', '.', '#'],
    ['#', '.', '.', 'P', '.', 'L', '#'],
    ['#', '#', '#', '.', '#', '#', '#'],
    ['#', 'M', '.', '.', '.', 'T', '#'],
    ['#', '#', '#', '#', '#', '#', '#']
]

orb_positions = {
    'F': (1, 1),  # Fire orb
    'W': (3, 1),  # Water orb
    'E': (5, 1),  # Earth orb
    'A': (3, 3)   # Air orb
}
```
### Breadth First Search:
1. Start with the initial state: {F: (1,1), W: (3,1), E: (5,1), A: (3,3)}
  - Add this state to the queue and mark it as visited.
2. Dequeue the first state and generate all possible moves:
  - F can move to (1,2)
  - W can move to (3,2)
  - E can move to (5,2)
  - A can move to (3,2), (3,4), or use the portal to (3,3)
Add all these new states to the queue.
3. Dequeue the next state (where F moved to (1,2)) and generate all possible moves:
  - F can move to (1,3)
  - W, E, A remain the same as the initial state
Add this new state to the queue.
4. Continue this process, exploring all possible single moves before considering any double moves.

BFS will systematically explore the maze level by level, ensuring it finds the shortest path to the goal state.

### Depth First Search:
1. Start with the initial state: {F: (1,1), W: (3,1), E: (5,1), A: (3,3)}
  - Push this state onto the stack and mark it as visited.
2. Pop the top state and generate all possible moves (same as BFS Step 2).
  - Push all these new states onto the stack.
3. Pop the top state (likely the last one pushed in Step 2, where A used the portal).
Generate all possible moves from this state:
  - F, W, E remain in their initial positions
  - can move to (3,2) or (3,4)
Push these new states onto the stack.
4. Continue this process, always exploring the most recently discovered state first.

DFS will dive deep into one particular path before backtracking to explore alternatives. It might find a solution quickly if it luckily chooses the right path, but it's not guaranteed to find the shortest solution.

### Best First Search:
1. Start with the initial state: {F: (1,1), W: (3,1), E: (5,1), A: (3,3)}
  Calculate the heuristic value:
  - F to V: |1-1| + |1-5| = 4
  - W to L: |3-3| + |1-5| = 4
  - E to M: |5-5| + |1-1| = 0
  - A to T: |3-5| + |3-5| = 4
  - Total heuristic: 4 + 4 + 0 + 4 = 12
Add this state to the priority queue with its heuristic value.
2. Pop the state with the lowest heuristic value (which is the initial state).
  - Generate all possible moves (same as BFS Step 2).
  - Calculate the heuristic value for each new state and add to the priority queue.
3. Pop the state with the lowest heuristic value. This is likely to be a state where E moved to (5,2), as it's already at its goal column.
  - Generate all possible moves from this state.
  - Calculate the heuristic value for each new state and add to the priority queue.
4. Continue this process, always exploring the state with the lowest heuristic value next.

Best-First Search will prioritize moves that seem to bring the orbs closer to their goals. In this maze, it's likely to focus on moving E to its goal first, then possibly F or W (as they're further from their goals), and finally A.


## Example 2:
```python
maze = [
    ['#', '#', '#', '#', '#', '#', '#', '#', '#', '#'],
    ['#', '.', '.', '.', '#', '.', '.', '.', 'V', '#'],
    ['#', '#', '#', '.', '#', '.', '#', '#', '.', '#'],
    ['#', '.', '.', '.', '.', '.', '.', 'P', '.', '#'],
    ['#', '.', '#', '#', '#', '#', '#', '#', '.', '#'],
    ['#', '.', '.', '.', 'P', '.', '.', '.', '.', '#'],
    ['#', '#', '#', '#', '#', '.', '#', '#', '#', '#'],
    ['#', 'M', '.', '.', '.', '.', '.', '.', 'L', '#'],
    ['#', '.', '#', '#', '#', '#', '#', '#', '.', '#'],
    ['#', 'T', '.', '.', '.', '.', '.', '.', '.', '#'],
    ['#', '#', '#', '#', '#', '#', '#', '#', '#', '#']
]

orb_positions = {
    'F': (1, 1),  # Fire orb
    'W': (3, 1),  # Water orb
    'E': (5, 1),  # Earth orb
    'A': (7, 1)   # Air orb
}
```

### Breadth First Search:
1. Start with the initial state: {F: (1,1), W: (3,1), E: (5,1), A: (7,1)}
2. Add this state to the queue and mark it as visited.
3. Begin the main loop:
  a. Dequeue the first state.
  b. Check if it's the goal state (it's not).
  c. Generate all possible moves for each orb:
    - Fire (F) can move right or down.
    - Water (W) can move right, left, or down.
    - Earth (E) can move right, left, or down.
    - Air (A) can move left or down.
  d. Add all these new states to the queue if they haven't been visited.
4. Continue this process, expanding the search breadth-first.
5. The search will explore moving each orb towards its goal:
  - Fire (F) needs to reach V at (1,8)
  - Water (W) needs to reach L at (7,8)
  - Earth (E) needs to reach M at (7,1)
  - Air (A) needs to reach T at (9,1)

The search will also consider using the portals at (3,7) and (5,4) when orbs reach these positions.
The process continues until a state is found where all orbs are in their goal positions.
Once the goal state is reached, the path is reconstructed using the parent dictionary.

The BFS approach ensures that the shortest path (in terms of the number of moves) is found. However, for complex mazes like this one, the search space can become very large, potentially leading to long execution times or high memory usage.

### Depth First Search:
1. Start with the initial state: {F: (1,1), W: (3,1), E: (5,1), A: (7,1)}
2. Push this state onto the stack and mark it as visited.
3. Begin the main loop:
  a. Pop the top state from the stack.
  b. Check if it's the goal state (it's not).
  c. Generate all possible moves for each orb:
    - Fire (F) can move right or down.
    - Water (W) can move right, left, or down.
    - Earth (E) can move right, left, or down.
    - Air (A) can move left or down.
  d. Push all these new states onto the stack if they haven't been visited.
4. Continue this process, expanding the search depth-first.
5. The search will explore moving one orb as far as possible before considering moves for the other orbs:
  - It might first try to move Fire (F) all the way to its goal V at (1,8)
  - Then it could backtrack and try different paths for Fire
  - Only after exhausting Fire's possibilities would it start moving Water (W) significantly
6. The search will consider using the portals at (3,7) and (5,4) when orbs reach these positions, potentially leading to deep explorations through portal paths.
7. The process continues until a state is found where all orbs are in their goal positions, or until all possibilities have been exhausted.
8. Once the goal state is reached, the path is reconstructed using the parent dictionary.

Depth-First Search (DFS) is a memory-efficient search algorithm, especially suitable when memory is a constraint, as it only stores the current path and unexplored sibling nodes. However, DFS is not guaranteed to find the shortest path, and in complex scenarios like mazes with cycles or portals, it risks getting stuck in infinite loops without a visited set. While DFS may quickly find a solution if the target lies deep in the first direction it explores, it can also waste time exploring deep, unfruitful paths. Despite this, when implemented with proper cycle detection, DFS is complete and will eventually find a solution if one exists.

In this specific example, DFS might find a different solution than BFS, and it might take a very different amount of time to find it, depending on how the search space is structured and which direction it explores first.

### Best First Search:
1. Start with the initial state: {F: (1,1), W: (3,1), E: (5,1), A: (7,1)}
2. Calculate the heuristic value for this state:
  - F to V: |1-1| + |1-8| = 7
  - W to L: |3-7| + |1-8| = 11
  - E to M: |5-7| + |1-1| = 2
  - A to T: |7-9| + |1-1| = 2
  - Total heuristic: 7 + 11 + 2 + 2 = 22
3. Add this state to the priority queue with its heuristic value.
4. Begin the main loop:
  a. Pop the state with the lowest heuristic value from the priority queue.
  b. Check if it's the goal state (it's not).
  c. Generate all possible moves for each orb.
  d. For each new state:
    - Calculate its heuristic value.
    - Add to the priority queue if not visited.
5. Continue this process, always exploring the state with the lowest heuristic value next.
6. The search will tend to prioritize moves that bring orbs closer to their goals:
  - It might first focus on moving Earth (E) to M and Air (A) to T, as they're closest to their goals.
  - Then it could focus on Fire (F) moving towards V.
  - Water (W) might be moved last as it's furthest from its goal.
7. The search will consider using the portals at (3,7) and (5,4) when orbs reach these positions, potentially leading to significant heuristic improvements.
8. The process continues until a state is found where all orbs are in their goal positions, or until all possibilities have been exhausted.
9. Once the goal state is reached, the path is reconstructed using the parent dictionary.

In this specific example, Best-First Search might find a solution more quickly than BFS or DFS, especially if the heuristic accurately reflects the difficulty of moving each orb to its goal. However, the solution might not be the shortest possible path, as the search might make moves that look good according to the heuristic but aren't actually optimal.
The use of Manhattan distance in the heuristic is particularly suitable for this maze, as it reflects the grid-like movement of the orbs. However, it doesn't account for walls or portals, which might lead to some inaccuracies in state evaluation.

## Comparison of performance
### Breadth First Search:

- **Data structure**: Queue (implemented using deque)
- **Time complexity**: O(b^d), where b is the branching factor and d is the depth of the shallowest solution
- **Space complexity**: O(b^d)
- **Completeness**: Complete (will find a solution if one exists)
- **Optimality**: Optimal for unweighted graphs (finds the shortest path in terms of number of steps)

### Depth First Search:

- **Data structure**: Stack
- **Time complexity**: O(b^m), where b is the branching factor and m is the maximum depth of the search tree
- **Space complexity**: O(bm), where b is the branching factor and m is the maximum depth
- **Completeness**: Not complete in infinite spaces, but complete in finite spaces
- **Optimality**: Not optimal (may find a solution that is not the shortest path)

### Best First Search:

- **Data structure**: Priority Queue (implemented using heapq)
- **Time complexity**: O(b^m), where b is the branching factor and m is the maximum depth of the search tree
- **Space complexity**: O(b^m), storing all generated nodes
- **Completeness**: Not complete (can get stuck in infinite loops if not implemented with cycle detection)
- **Optimality**: Not guaranteed to be optimal, depends on the heuristic function

### Key differences:
- BFS explores all nodes at the current depth before moving to the next level, while DFS explores as far as possible along each branch before backtracking. Best-First Search explores nodes based on a heuristic function.
- BFS guarantees the shortest path in unweighted graphs, while DFS and Best-First Search do not.
- BFS typically requires more memory than DFS, as it needs to store all nodes at the current level. Best-First Search's memory usage depends on the effectiveness of the heuristic.
- Best-First Search can potentially find a solution faster than BFS or DFS if the heuristic function is good, but it may not find the optimal solution.
- BFS is complete in all cases, DFS is complete only in finite spaces, and Best-First Search is not guaranteed to be complete without additional mechanisms.

In the context of the Elemental Maze problem:
- BFS will find the shortest solution path but might be slower for large mazes.
- DFS might find a solution quickly but not necessarily the shortest one.
- Best-First Search could potentially find a good solution quickly if the heuristic (Manhattan distance to goals) is effective for the specific maze layout.

## Conclusion
The Elemental Maze problem provides a rich environment for exploring the nuances of different search algorithms. Through our implementation and analysis of Breadth First Search, Depth First Search, and Best First Search, we've gained valuable insights into how these algorithms perform in a complex state space.

Breadth First Search, with its guarantee of finding the optimal solution, proves to be the most reliable choice when the shortest path is crucial. However, its memory requirements can be substantial, especially in large mazes. Depth First Search offers a memory-efficient alternative, potentially finding solutions quickly in certain maze configurations, but without the guarantee of optimality. Best First Search, guided by our heuristic function, strikes a balance between the two, often finding good solutions efficiently, though not always the optimal one.

The choice of algorithm ultimately depends on the specific requirements of the problem at hand. If memory is constrained but a solution must be found, DFS might be preferable. For scenarios where the optimal solution is necessary and memory is not a concern, BFS is the clear choice. In large, complex mazes where a good solution is needed quickly, Best First Search could be the most effective approach.
