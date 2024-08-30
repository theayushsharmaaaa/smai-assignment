# The Elemental Maze: A State Space Search Problem

- **Course**: Search Methods in AI
- **Assignment 1 Part 1**: State Space Search Representation
- **Submitted by**: Ayush Sharma (U20220024) and Manjree Kothari (U20220104)

## Problem Description

The Elemental Maze is an innovative puzzle game that presents a unique state space search problem. In this game, players must navigate four elemental orbs (Fire, Water, Earth, and Air) through a complex maze-like environment to reach their respective goal locations. The challenge lies in managing the interactions between elements, utilizing special features of the maze, and coordinating the movement of multiple agents simultaneously.

## Domain Representation

The state of the Elemental Maze is represented by:

1. A 2D grid (NxM matrix) representing the maze layout.
2. The positions of the four elemental orbs.

### Grid Elements:
- '#': Wall (impassable)
- '.': Empty space (passable)
- 'O': Elemental node (orbs can stop here)
- 'P': Portal (allows teleportation)
- 'F', 'W', 'E', 'A': Fire, Water, Earth, and Air orbs
- 'V', 'L', 'M', 'T': Volcano, Lake, Mountain, Wind Tunnel (goal positions)

### State Representation:
```python
# 5x5 grid
state = (
    [
        ['#', '#', '#', '#', '#'],
        ['#', 'O', '.', 'P', '#'],
        ['#', '.', '#', '.', '#'],
        ['#', 'P', '.', 'O', '#'],
        ['#', '#', '#', '#', '#']
    ],
    {
        'F': (1, 1),  # Fire orb at (1, 1)
        'W': (3, 3),  # Water orb at (3, 3)
        'E': (2, 1),  # Earth orb at (2, 1)
        'A': (3, 1)   # Air orb at (3, 1)
    }
)
```

```python
# 10x10 grid
state = (
    [
        ['#', '#', '#', '#', '#', '#', '#', '#', '#', '#'],
        ['#', 'O', '.', '.', 'P', '.', '.', '.', 'V', '#'],
        ['#', '.', '#', '#', '#', '.', '#', '#', '.', '#'],
        ['#', '.', '.', '.', '.', '.', '.', 'P', '.', '#'],
        ['#', '#', '#', '.', '#', '#', '#', '#', '.', '#'],
        ['#', 'L', '.', '.', '.', '.', '.', '.', '.', '#'],
        ['#', '#', '#', '.', '#', '#', '#', '#', '.', '#'],
        ['#', '.', 'P', '.', '.', '.', '.', '.', '.', '#'],
        ['#', 'M', '#', '#', '#', '.', '#', '#', 'T', '#'],
        ['#', '#', '#', '#', '#', '#', '#', '#', '#', '#']
    ],
    {
        'F': (1, 1),  # Fire orb at (1, 1)
        'W': (5, 1),  # Water orb at (5, 1)
        'E': (8, 1),  # Earth orb at (8, 1)
        'A': (7, 7)   # Air orb at (7, 7)
    }
)
```

## MoveGen / Neighbourhood Function

The MoveGen function takes the current state as input and returns a list of all valid next states. It considers:

1. Regular moves: Orbs can move to adjacent empty spaces or elemental nodes.
2. Portal usage: Orbs can teleport between portal locations.
3. Elemental interactions: Certain moves may be invalid based on elemental properties.

```python
def move_gen(state):
    grid, orb_positions = state
    new_states = []
    
    for orb, position in orb_positions.items():
        for move in get_valid_moves(grid, orb, position):
            new_orb_positions = orb_positions.copy()
            new_orb_positions[orb] = move
            new_states.append((grid, new_orb_positions))
    
    return new_states

def get_valid_moves(grid, orb, position):
    x, y = position
    valid_moves = []
    directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]  # Right, Down, Left, Up
    
    for dx, dy in directions:
        new_x, new_y = x + dx, y + dy
        if is_valid_move(grid, orb, new_x, new_y):
            valid_moves.append((new_x, new_y))
    
    # Check for portal teleportation
    if grid[x][y] == 'P':
        for px, py in find_portals(grid):
            if (px, py) != (x, y) and is_valid_move(grid, orb, px, py):
                valid_moves.append((px, py))
    
    return valid_moves

def is_valid_move(grid, orb, x, y):
    if not (0 <= x < len(grid) and 0 <= y < len(grid[0])):
        return False
    if grid[x][y] in ['#', 'V', 'L', 'M', 'T']:
        return False
    # Add elemental interaction rules here
    return True

def find_portals(grid):
    return [(x, y) for x in range(len(grid)) for y in range(len(grid[0])) if grid[x][y] == 'P']
```

This MoveGen function ensures that all generated states respect the domain constraints, such as not allowing orbs to move through walls or onto occupied spaces.

## GoalTest Function

The GoalTest function checks if the current state satisfies the goal conditions:

```python
def goal_test(state):
    grid, orb_positions = state
    goal_positions = find_goal_positions(grid)
    return (orb_positions['F'] == goal_positions['V'] and
            orb_positions['W'] == goal_positions['L'] and
            orb_positions['E'] == goal_positions['M'] and
            orb_positions['A'] == goal_positions['T'])

def find_goal_positions(grid):
    goals = {}
    for x in range(len(grid)):
        for y in range(len(grid[0])):
            if grid[x][y] in ['V', 'L', 'M', 'T']:
                goals[grid[x][y]] = (x, y)
    return goals
```

This function returns True if all orbs are in their correct goal positions, and False otherwise.

## User Specification

To allow users to specify the initial state and goal, we implement a simple interface:

```python
def create_maze():
    print("Enter the maze layout (use '#' for walls, '.' for empty spaces, 'O' for nodes, 'P' for portals):")
    print("Maximum grid size is 12x12. Enter an empty line to finish.")
    maze = []
    while True:
        row = input()
        if row == "":
            break
        if len(maze) >= 12 or len(row) > 12:
            print("Error: Maximum grid size exceeded. Please keep it within 12x12.")
            return create_maze()  # Restart the input process
        maze.append(list(row))
    
    if len(maze) == 0 or len(maze[0]) == 0:
        print("Error: The maze cannot be empty.")
        return create_maze()  # Restart the input process
    
    if not all(len(row) == len(maze[0]) for row in maze):
        print("Error: All rows must have the same length.")
        return create_maze()  # Restart the input process
    
    return maze

def place_orbs_and_goals(maze):
    orb_positions = {}
    elements = {'F': 'Fire', 'W': 'Water', 'E': 'Earth', 'A': 'Air'}
    goal_types = {'V': 'volcano', 'L': 'lake', 'M': 'mountain', 'T': 'wind tunnel'}

    for element, name in elements.items():
        while True:
            try:
                x, y = map(int, input(f"Enter {name} orb position (x y): ").split())
                if 0 <= x < len(maze) and 0 <= y < len(maze[0]) and maze[x][y] in ['.', 'O']:
                    orb_positions[element] = (x, y)
                    maze[x][y] = element
                    break
                else:
                    print("Invalid position. Please try again.")
            except ValueError:
                print("Invalid input. Please enter two integers separated by a space.")

    for goal, name in goal_types.items():
        while True:
            try:
                x, y = map(int, input(f"Enter {name} position (x y): ").split())
                if 0 <= x < len(maze) and 0 <= y < len(maze[0]) and maze[x][y] == '.':
                    maze[x][y] = goal
                    break
                else:
                    print("Invalid position. Please try again.")
            except ValueError:
                print("Invalid input. Please enter two integers separated by a space.")

    return maze, orb_positions

# Usage
maze = create_maze()
maze, initial_orb_positions = place_orbs_and_goals(maze)
initial_state = (maze, initial_orb_positions)
```

This interface allows users to:
1. Define the maze layout
2. Specify initial positions for each elemental orb
3. Set the locations for each goal (volcano, lake, mountain, wind tunnel)

## Unique Aspects and Challenges

1. **Multi-Agent Coordination**: Managing four orbs simultaneously adds complexity to the search space and requires careful planning to avoid conflicts.

2. **Non-Linear Navigation**: The inclusion of portals introduces non-linear movement options, making the search space more interesting and challenging to navigate efficiently.

3. **Elemental Interactions**: Each orb has unique properties based on its element, which affects how it interacts with the environment and other orbs. For example:
   - Fire orbs might be extinguished by water or create steam when passing through water spaces.
   - Earth orbs could create bridges over gaps or block paths for other orbs.
   - Air orbs might have the ability to float over certain obstacles.
   - Water orbs could flow through narrow passages or create ice bridges in cold areas.

4. **Dynamic Environment**: The state of the maze can change as orbs move through it, adding a temporal aspect to the problem. For instance, a fire orb passing through might temporarily melt ice, creating new pathways.

5. **Flexible Problem Definition**: Users can create varied and challenging mazes, making each instance of the problem unique. This flexibility allows for a wide range of difficulty levels and puzzle designs.

6. **Constraint Satisfaction**: The need to place all orbs in their correct goal positions simultaneously adds a constraint satisfaction aspect to the problem.

7. **Path Interdependence**: The optimal path for one orb may depend on the movements of the others, creating interdependencies that must be considered in the search strategy.

The Elemental Maze presents a rich, complex state space search problem that combines elements of classic maze-solving with multi-agent coordination, special game mechanics, and dynamic environmental interactions. This unique combination of features sets it apart from common textbook examples and provides an engaging challenge for developing and testing search algorithms.
