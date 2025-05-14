# IUPC-CGPA

## Project Description

This project ranks universities based on how well their teams perform in various IUPCs (International University Programming Contests). It collects contest results from online judges and calculates scores using a weighted ranking system.

Along with generating rankings, the project also automatically distributes contest slots to institutions based on their performance. The final output includes a ready-to-use Excel sheet showing the slot allocations, with space for adjustments if needed.

## File Structure

```
.
├── input/                              # All input files required to run the analysis
│   ├── grading/                        # Files related to scoring and ranking
│   │   ├── contest_files/              # HTML standings saved from online judges
│   │   │   ├── Example.html
|   |   |
│   │   ├── contests.json               # Metadata for contests (name, parser, filename)
│   │   ├── credits.json                # Contest weights
│   │   └── institutions.json           # List of institutions and aliases
|   |
│   └── distribution/                   # Files controlling slot distribution
│       ├── registered.txt              # Registered institutions (one per line)
│       └── options.json                # Configurable parameters for slot distribution
│
├── result/                             # Intermediate outputs after grading
│   ├── contests.json                   # Parsed contest results
│   ├── institutions.json               # Aggregated institution scores
│   └── marksheet.csv                   # Final ranked list of institutions
│
├── output/                             # Final deliverables for organizers
│   └── slots.csv                       # Slot distribution results
│
├── grading.ipynb                      # Notebook to process contest results and generate marksheet
├── distribution.ipynb                 # Notebook to allocate slots based on marksheet
├── environment.yml                    # Conda environment configuration
├── LICENSE                            # MIT License
└── README.md                          # This file

```

## Setup Instructions

### Clone the repository

Clone the repository to your local machine and navigate to the project directory.

```bash
git clone https://github.com/ir-rafio/IUPC-CGPA.git
cd IUPC-CGPA
```

### Setup Conda Environment

If you don't have Anaconda, [install it first](https://www.anaconda.com/products/distribution).

The project includes an `environment.yml` file that specifies the required dependencies. To set up the environment, use the provided configuration file to create a new conda environment and activate it.

```bash
conda deactivate
conda env remove --name iupcrank
conda env create -f environment.yml
conda activate iupcrank
```

## Usage Instructions

### Prepare the Required Files

The following files must be prepared before running the analysis.

#### Under `input/grading/`

| File                | Description                                                                                                     |
| ------------------- | --------------------------------------------------------------------------------------------------------------- |
| `contests.json`     | Metadata for each contest — includes name, filename (HTML), and parser name.                                    |
| `credits.json`      | Weights (credits) assigned to each contest to determine their impact on final scores.                           |
| `institutions.json` | Official institution names along with their known aliases, used for accurate identification.                    |
| `contest_files/`    | Folder containing raw HTML files downloaded directly from contest standings (Toph, BAPSOJ currently supported). |

#### Under `input/distribution/`

| File             | Description                                                                   |
| ---------------- | ----------------------------------------------------------------------------- |
| `registered.txt` | List of registered institutions for the current contest cycle (one per line). |
| `options.json`   | Configuration for slot distribution (e.g., total slots, max per institution). |

### Run the Analysis

Once the environment is set up and the necessary files are in place, run the Jupyter notebooks:

- Run `grading.ipynb` to generate the marksheet.
- Run `distribution.ipynb` to distribute the slots automatically.

## Slot Distribution Criteria

1. **Team Data Extraction**  
   Team standings are parsed from all contests listed in `input/grading/contests.json`.

2. **Institution CGPA Calculation**

   - **Team GP (Grade Point) in a Contest**

     - The top team in a contest receives `4.00` grade points.
     - Each subsequent team gets `2%` less than the previous one.  
       For example, the second team gets `4.00 * 0.98 = 3.92`, the third gets `3.92 * 0.98`, and so on.
     - A multiplier based on the number of problems solved is applied:
       `multiplier = sqrt(S / S_max)`
       where `S` is the number of problems solved by the team, and `S_max` is the maximum solve count in that contest.
     - `Final GP = base GP * multiplier`

   - **Institution GP in a Contest**

     - Calculated using the L-pi norm of its teams’ GPs.
     - This gives more importance to stronger teams when aggregating.

   - **Institution CGPA**
     - An institution's CGPA is calculated by combining its performance across all contests, giving more importance to contests with higher credit.
     - Each contest has a credit value that reflects its importance.
     - The institution’s performance (GP) in each contest is multiplied by that contest’s credit, and then all these weighted scores are combined using L2 norm.

3. **Slot Allocation**
   - Every institution gets 1 default slot.
   - Remaining slots are distributed based on priority.
   - The priority of the i-th slot for an institution is calculated using the formula: `priority = CGPA / i`
   - Slots are assigned one at a time to the institution with the current highest priority.
   - A cap is applied to ensure that no institution receives more slots than the maximum limit specified in `input/distribution/options.json`.
