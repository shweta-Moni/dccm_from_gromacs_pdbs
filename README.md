# dccm_from_gromacs_pdbs
This pipeline calculates DCCM from GROMACS trajectories to analyze correlated motions between residues. Developed by Prof. Abhijit Mitra's group (IIIT Hyderabad), it uses C for numerical computations and Perl for data parsing.



# Prerequisites
- GROMACS (for trajectory processing)
- Perl (`run1-extract-CA-xyz-only_gromacs1.pl`)
- C compiler (gcc)
- GNUPLOT (visualization)

---

# Step-by-Step Workflow

1. Convert XTC Trajectory to PDB
 
gmx trjconv -n index.ndx -f md_3ns.xtc -s md_3ns.tpr -o md_3nstraj.pdb -pbc whole
# Select "Protein" (option 1) to retain only protein coordinates
 

2. Extract CA Atom Coordinates
 
perl run1-extract-CA-xyz-only_gromacs1.pl md_3nstraj.pdb md_run1
# Output: No of residues 313 → 313 residues detected
 

3. Calculate Mean & Deviations
 
gcc run2-mean-n-dev4mmean.c -o run2
./run2 md_run1 md_run2 313
# Syntax: <input> <output> <ResidueCount>
 

4. Compute Cross-Correlations
 
gcc run4-frame.c -lm -o run4
./run4 md_run2 md_run4 313
# Generates final DCCM matrix
 

---

# Visualization with GNUPLOT
 
# Install GNUPLOT (if needed)
sudo apt-get install gnuplot

# Launch GNUPLOT and execute commands:
gnuplot> set pm3d map
gnuplot> set size square
gnuplot> set palette defined (-1 "red", 0 "white", 1 "blue")
gnuplot> set xrange [0:310]
gnuplot> set yrange [0:310]
gnuplot> splot 'md_run4' using 1:2:3 with pm3d
 

---

# Interpretation
| Color  | Correlation Range | Meaning               |
|--------|-------------------|-----------------------|
| Blue   | +1.0 to +0.2      | Strong positive       |
| White  | -0.2 to +0.2      | Uncorrelated          |
| Red    | -0.2 to -1.0      | Strong negative       |

---

