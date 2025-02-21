# Water Jug Challenge

## Description
This project implements the Water Jug Challenge using Vue 3 and TypeScript. The challenge consists of using two jugs with different capacities to measure an exact amount of water through a series of steps.

## Running the Project
1. Clone the repository.
2. Install dependencies:
   ```sh
   npm install
   ```
3. Start the development server:
   ```sh
   npm run dev
   ```


## Technologies Used
- Vue 3 with Composition API
- TypeScript
- CSS for styling


## Usage
### Automatic mode
1. Enter the capacities of the two jugs.
2. Set the desired goal.
3. Click "Solve Automatically" to see the step-by-step solution.
4. The result will be displayed, or an error message will appear if the goal is not possible.

### Manual mode
1. Enter the capacities of the two jugs.
2. Set the desired goal.
3. Find the solution by clicking on the different buttons and see the steps below.
4. The result will be displayed, or an error message will appear if the goal is not possible.



## How It Works
The algorithm solves the challenge by simulating the process of filling, emptying, and transferring water between two jugs until the desired amount is obtained in either jug.


## ⚙️ Problem Rules
The following operations are allowed on the jugs:

1. **Fill a jug completely** to its maximum capacity.
2. **Empty a jug completely**, leaving the jug empty.
3. **Pour water from one jug to another**, transferring water until:
 - The receiving jug is full.
 - The jug being poured from is empty.

❌ **Restrictions**:
 - Partial measurements inside the jugs are not allowed.
 - Intermediate volumes cannot be marked in the jugs.



## 🧮 Conditions for a Solution
For the problem to have a solution, the following mathematical conditions must be met:

1. **The goal must be a multiple of the greatest common divisor (GCD) of the capacities of both jugs**.
   - That is, if `G % GCD(J1, J2) ≠ 0`, it is impossible to obtain the desired amount of water.
   
   - **In the code**: `const divisor = gcd(jug1Capacity.value, jug2Capacity.value);` calculates the GCD of the two jugs' capacities. Then, it checks if the goal `goal.value` is a multiple of the GCD by performing the check `goal.value % divisor === 0`.

2. **The goal cannot be greater than the capacity of the largest jug**.
   - If `G > max(J1, J2)`, it is impossible to obtain the desired amount, as one of the jugs cannot contain the goal.
   
   - **In the code**: `goal.value <= Math.max(jug1Capacity.value, jug2Capacity.value)` ensures that the goal is not greater than the capacity of the larger jug.

3. **Both jugs can't have the same capacity**.

If these conditions are met, then there exists at least one sequence of steps that allows obtaining the exact amount of water in one of the jugs.

---

## 📝 Example of the Problem

### ✅ Valid Example:
- Jug 1 capacity: 5 liters
- Jug 2 capacity: 3 liters
- Goal: 1 liter

In this case, the GCD(5, 3) = 1, and the goal is a multiple of the GCD (1 % 1 = 0), so it is possible to obtain exactly 1 liter.

### ❌ Invalid Example:
- Jug 1 capacity: 4 liters
- Jug 2 capacity: 6 liters
- Goal: 5 liters

In this case, the GCD(4, 6) = 2, but 5 is not a multiple of 2 (5 % 2 ≠ 0), so it is impossible to obtain exactly 5 liters.

### Steps of the Algorithm


### 🏆 Optimal Strategy for Solving the Problem

1. **Initialize the jugs**:  
   Both jugs start empty.

2. **Determine the optimal strategy**:  
   - **First condition**: If the difference between the smaller jug's capacity and the larger jug's capacity is equal to the goal, the algorithm can solve the problem with a simple **two-step transfer**.
     - The first step involves filling the larger jug, and the second step consists of transferring water to the smaller jug.
   - Otherwise:
     - If the absolute difference between the goal and the larger jug's capacity is ***less than or equal*** to that of the smaller jug, **prioritize filling the larger jug first**.
     - Otherwise, **prioritize filling the smaller jug first**.

3. **Perform actions based on the chosen strategy**:  
   - If the prioritized jug is **empty**, fill it to its full capacity.  
   - If the **other jug is full**, empty it.  
   - Otherwise, **transfer water** between the jugs until one is full or empty.

4. **Repeat until the goal is reached in either jug**.


### 🔎 Example Scenarios

#### ✅ Example 1: Solvable Case with First condition of differente
**Jugs:** 6L and 4L  
**Goal:** 2L  

In this case, The difference between the jugs is equal to the goal so the algorithm will perform the two-step transfer, filling the larger jug and transfering the water to the smallest one:

1. Fill Jug 1 (6L) - `(6,0)`
2. Transfer 6L from Jug 2 to Jug 1 - `(2,4)`🎯 *Goal achieved!*

---

#### ✅ Example 2: Solvable Case using the First Strategy (Larger Jug First)
**Jugs:** 5L and 3L  
**Goal:** 4L  

In this case, the GCD(5, 3) = 1, and the goal is divisible by the GCD. The algorithm uses the second strategy of filling the smaller jug first:

In this case, the algorithm prioritizes filling the larger jug. The steps are:

1. Fill Jug 1 (5L) - `(5,0)`
2. Transfer 3L from Jug 1 to Jug 2 - `(2,3)`
3. Empty Jug 2 - `(2,0)`
4. Transfer 2L from Jug 1 to Jug 2 - `(0,2)`
5. Fill Jug 1 (5L) - `(5,2)`
6. Transfer 1L from Jug 1 to Jug 2 - `(4,3)` 🎯 *Goal achieved!*

---

#### ✅ Example 3: Solvable Case using the First Strategy (Larger Jug First)
**Jugs:** 5L and 1L  
**Goal:** 4L  

Using the second strategy of filling the smaller jug, we proceed as follows:

1. Fill Jug 2 (1L) - `(5,0)`
2. Transfer 1L from Jug 2 to Jug 1 - `(4,1)` 
3. Fill Jug 2 (1L) - `(1,1)`
4. Transfer 1L from Jug 2 to Jug 1 - `(2,0)`
5. Fill Jug 2 (1L) - `(2,1)`
6. Transfer 1L from Jug 2 to Jug 1 - `(3,0)`
7. Fill Jug 2 (1L) - `(3,1)`
8. Transfer 1L from Jug 2 to Jug 1 - `(4,0)` 🎯 *Goal achieved!*

This method takes more steps compared to the ***First strategy***, which would solve it in just 2 steps:

1. Fill Jug 1 (5L) - `(5,0)`
2. Transfer 1L from Jug 1 to Jug 2 - `(4,1)` 🎯 *Goal achieved!*


The algorithm will recognize that the absolute difference between the goal and the larger jug's capacity is ***equal or smaller*** than that of the smaller jug so it will execute the first strategy.

---

#### ✅ Example 4: Solvable Case with Second Strategy (Small Jug First)
**Jugs:** 2L and 5L  
**Goal:** 1L  

With a GCD of 1, the goal is achievable. The algorithm uses the second strategy (smaller jug first):

1. Filled Jug 1 (2L) - `(2,0)`
2. Transferred 2L from Jug 1 to Jug 2 - `(0,2)`
3. Filled Jug 1 (2L) - `(2,2)`
4. Transferred 2L from Jug 1 to Jug 2 - `(0,4)`
5. Filled Jug 1 (2L) - `(2,4)`
6. Transferred 1L from Jug 1 to Jug 2 - `(1,5)` 🎯 *Goal achieved!*

---