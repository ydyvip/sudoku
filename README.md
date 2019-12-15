# Logical Sudoku Solver

A logical sudoku solver that outputs detailed descriptions of the techniques and moves required at each step to solve unique solution 9x9 sudokus. No backtracking or brute forcing will be used, only logical reasoning. The solver can read *.csv* files to solve multiple puzzles in a batch. Simple difficulty analysis has been implemented based on the number of moves requires, initial clues and technical strategies required to solve the puzzle.



## Terminology

* Candidates - *The possible values a cell can be. Various techniques will aim to reduce to number of candidates for each cell using logical reasoning.*

* Simple Sudoku - *A sudoku that can be solved only using the solo candidate and hidden candidate techniques.*

* Minimum Sudoku - *A sudoku where removing a clue from the initial configuration produces a sudoku without a unique solution.*

* Conjugate Pair - *Where a candidate is only valid in exactly two cells within a structure, the two cells form a conjugate pair.*

  

## Solution Techniques ##

##### Solo Candidate #####

The *solo candidate* technique is a simple technique for identifying the value of cells where a cell has only one candidate, therefore the cell must be that candidate. This technique has been implemented using a **O(N<sup>2</sup>)** complexity algorithm as every cell in the grid must be checked. Multiple solo candidates can be observed in one pass of the algorithm.

* Using only this strategy is not sufficient enough to solve any 17-clue sudokus.



##### Hidden Candidate ###

The *hidden candidate* technique is another simple technique for identifying the value of cells. If a candidate is valid in only one cell within a column, row or sector then that cell must be that value. This technique has been implemented using a **O(N<sup>3</sup>)** complexity algorithm as each cell in a structure (column, row, sector) must be checked against each candidate.

* Using only the *Single Candidate* and *Hidden Candidate* techniques, *44.6%* of the 49,151 17-clue sudokus were solved. However, these two strategies are sufficient enough to solve every simple sudoku.



##### Subset Cover (Pairs/Triples/Quads) #####

The *subset cover* technique eliminates candidates within a column, row or sector. If a subset of *N* cells within a structure covers *N* different candidates (i.e. union of candidates in the *N* cells is of size *N*) then the candidates must be contained within these *N* cells and cannot appear elsewhere in the structure. This technique is only valid for *2<=N<=4* as any subset of size *N* greater than 4 will automatically be composed of a smaller subset of size *(9-N)* which will be simpler to solve. *TODO [COMPLEXITY]*

* Implementing the *Subset Cover* technique boosted the accuracy by over *20%* up to *68.6%*.



##### Pointing Pairs/Triples #####

The *pointing pairs/triples* technique eliminates candidates within a column or row. If a candidate occurs either two or three times within a sector and these cells are all within the same column/row, then the value must be located within the sector and cannot occur elsewhere the column/row. This technique has been implemented using a **O(N<sup>3</sup>)** complexity algorithm as each cell in every sector must be checked for each candidate.

* Implementing the *Pointing Pairs* technique boosted the accuracy by *8.9%* up to *77.5%*. 



##### Box/Line Intersection #####

The *box/line intersection* technique eliminates candidates within a sector. If a candidate value in a column/row only appears within one sector, then that candidate must occur in the sector in that column/row, and so the candidate can be elinianted from the other cells in the same sector. This technique has been implemented using a **O(N<sup>3</sup>)** complexity algorithm as each cell in a column/row must be checked for every column/row.

* Implementing the *Box/Line Reduction* technique boosted the accuracy by *0.3%* up to *77.8%*.



##### Singles Chain

The *singles chain* technique firstly identifies for a given candidate all the conjugate pairs. Then constructs a connected graph of conjugate pairs with nodes of alternating state (ON/OFF). The conjugate pairs are used to find either violations of cells in the graph (two cells of the same state that are in the same structure) or cells not in the graph that can see nodes of both states. This technique has been implemented using an adjacency list to store the graph on conjugate pairs leading to an algorithmic complexity of **O(N<sup>3</sup>)**.

* Implementing the *Singles Chain* technique boosted the accuracy up to *90.4%*.



##### X-Wing #####

The *X-Wing* technique is a subset of single value chaining strategies where a candidate is restricted in two cells along a column in two different columns that all share the same rows. The technique can also be expressed as two conjugate pairs joined by two weak links where the four cells form a rectangle. This technique has been implemented using a **O(N<sup>3</sup>)** complexity algorithm.

* *TODO*



##### Future Work

I have implemented only a few logical techniques, however there are far more complex and advanced techniques available but occur very rarely in practice. I may implement additional techniques as I come to understand them. Unfortunately I cannot hope to be able to solve all known sudokus as solving all using only logical techniques is still an incomplete problem.



## Benchmarking and Testing ##

##### Summary #####

| Technique          | Tests Passed ( /49,151) | Tests Passed (%) |
| ------------------ | ----------------------- | ---------------- |
| Solo Candidate     | 0                       | 0                |
| Hidden Candidate   | 21905                   | 44.6             |
| Subset Cover       | -                       | 68.6             |
| Pointing Pairs     | -                       | -                |
| Box/Line Reduction | -                       | -                |
| X-Wing             | -                       | -                |
| Singles Chain      | 39,740                  | 90.4             |

*Note: Accuracy is determined by applying the corresponding technique and all previous techniques across all 49,151 17-clue sudokus.*



##### Datasets

Datasets of different sudoku puzzles were tested against the solution in order to test completeness, speed and efficiency.

- [Gordon Royle's list of all known 17-clue Sudoku puzzles][2]
  - 17 clues (initial numbers on the grid) is the minimum number of clues any sudoku can have.
  - The solver currently solves *90.4%* of the Sudokus in this dataset. ~~Currently the goal is to achieve 90+% accuracy across the whole set by implementing new strategies.~~
  - Testing all 49,151 puzzles is time-consuming, so a subset of 1000 of these puzzles are used for continual testing purposes.
  - This dataset will be used as the primary benchmark.

- [1 million Simple Sudoku games][1]
  - All sudokus in this dataset are simple (require only solo candidate and hidden candidate to solve). The solver successfully solves 100% of the sudokus in this dataset.
  - A subset of 1000 of these puzzles are used to check for errors.



![image-20191208161251656](/home/kyle/.config/Typora/typora-user-images/image-20191208161251656.png)



Here is a partially solved sudoku:

* Cells with only one value (Yellow/Purple) are cells which the value is known to be the value in the cell.

* Cells with one or more values (Blue/Red) are cells which the value could be one of the values in the cell.
* Different colours are used to differentiate between the 3x3 sectors.



## Example Output



<img src="/home/kyle/Pictures/sudoku-moves-1.png" alt="sudoku-moves-1" style="zoom: 67%;" />



At each step the following information is output:

* Techniques used to gain information.
* Cells and structures used to gain information.
* Cells it affects.



## Getting Started ##



#### Installing and Running ####

Clone the repository.

``` bash
git clone https://github.com/KyleGough/sudoku.git
```



Change into the directory.

``` git
cd sudoku
```



Run the solver on 1000 simple Sudoku.

``` git
./sudoku tests/simple-1000.csv
```



The output should look something like this.

```
[ Tests ]
 - Solved 1000 out of 1000 tests. (100.0%)
```



There are two optional flags:

* [-m] - Will display the moves and techniques used at each stage of solving the sudoku.
* [-o] - Will display the initial grid and solution in the terminal.

```
./sudoku -m -o tests/simple-1000.csv
```



## References ##

[1]: https://www.kaggle.com/bryanpark/sudoku	"1,000,000 Sudoku"
[2]: http://staffhome.ecm.uwa.edu.au/~00013890/sudokumin.php	"17-Clue Sudokus"
