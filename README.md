# IUPC-Rating

## Project Description

This project attempts to calculate ratings of different universities based on their teams' performances in different IUPCs (International University Programming Contests). The results are collected from online judges, and the rankings are computed based on contest performance data. The system supports multiple contests and institutions, and ranks institutions according to the weighted performance of their teams in each contest.

## Setup Instructions

1. **Clone the repository**:
   Clone the repository to your local machine and navigate to the project directory.

2. **Setup Conda Environment**:
   If you don't have Anaconda, [install it first](https://www.anaconda.com/products/distribution).
   The project includes an `environment.yml` file that specifies the required dependencies. To set up the environment, use the provided configuration file to create a new conda environment and activate it.

  ```bash
  conda env create -f environment.yml
  ```

  ```bash
  conda activate iupcrank
  ```

3. **Prepare the Required Files**:
   The following files are required for running the analysis:
   
   - **`contests.json`**: Contains information about the contests, including their names, filenames (which should correspond to the HTML files of the contests), and the parser to be used.
   - **`institutions.json`**: Lists institutions and their alternate names.
   - **`contest_weights.json`**: Specifies the weights assigned to each contest for ranking.
   - **HTML files**: These should be placed in the `input/contest_files` directory. The HTML pages should be directly saved from the standings pages of online judges. Currently, the supported OJs are Toph and BAPSOJ.

4. **Run the Analysis**:
   Once the environment is set up and the necessary files are in place, run the Jupyter notebook `notebook.ipynb` to perform the analysis.

## File Structure
- `environment.yml`: Conda environment configuration.
- `notebook.ipynb`: Jupyter notebook with the rating logic and analysis.
- `input/contest_files/`: Directory containing HTML files for each contest.
- `input/contests.json`: JSON file with contest details.
- `input/institutions.json`: JSON file with institution details.
- `input/contest_weights.json`: JSON file with contest weights.
- `output`: Directory for generated outputs.
