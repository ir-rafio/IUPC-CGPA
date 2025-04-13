# IUPC-CGPA

## Project Description

This project ranks universities based on how well their teams perform in various IUPCs (International University Programming Contests). It collects contest results from online judges and calculates scores using a weighted ranking system.

Along with generating rankings, the project also automatically distributes contest slots to institutions based on their performance. The final output includes a ready-to-use Excel sheet showing the slot allocations, with space for adjustments if needed.

## Setup Instructions

1. **Clone the repository**:
   Clone the repository to your local machine and navigate to the project directory.

2. **Setup Conda Environment**:
   If you don't have Anaconda, [install it first](https://www.anaconda.com/products/distribution).
   The project includes an `environment.yml` file that specifies the required dependencies. To set up the environment, use the provided configuration file to create a new conda environment and activate it.

   ```bash
   conda deactivate
   conda env remove --name iupcrank
   conda env create -f environment.yml
   conda activate iupcrank
   ```

3. **Prepare the Required Files**:
   The following files are required for running the analysis:

   - **`contests.json`**: Contains information about the contests, including their names, filenames (which should correspond to the HTML files of the contests), and the parser to be used
   - **`institutions.json`**: Lists institutions and their alternate names
   - **`credits.json`**: Specifies the weights assigned to each contest for ranking
   - **`options.json`**: Defines configurable parameters for the slot distribution process
   - **HTML files**: These should be placed in the `input/contest_files` directory. The HTML pages should be directly saved from the standings pages of online judges. Currently, the supported OJs are Toph and BAPSOJ.

4. **Run the Analysis**:
   Once the environment is set up and the necessary files are in place, run the Jupyter notebook `notebook.ipynb` to perform the analysis.

## Overview of Key Files

- `environment.yml`: Conda environment configuration
- `notebook.ipynb`: Jupyter notebook with the rating logic and analysis
- `input/contest_files/`: Directory containing HTML files for each contest
- `input/contests.json`: JSON file with contest details
- `input/institutions.json`: JSON file with institution details
- `input/credits.json`: JSON file with credits for each contest
- `input/options.json`: JSON file with configurable parameters for the slot distribution process
- `output`: Directory for generated outputs

### Slot Distribution Criteria

1. **Team Data Extraction**  
   Team standings are parsed from all contests listed in `contests.json`.

2. **Institution CGPA Calculation**

   - **Team GP (Grade Point) in a Contest**

     - The top team in a contest receives 4.00 grade points.
     - Each subsequent team gets 2% less than the previous one.  
       For example, the second team gets 4.00 _ 0.98 = 3.92, the third gets 3.92 _ 0.98, and so on.
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
   - A cap is applied to ensure that no institution receives more slots than the maximum limit specified in `options.json`.
