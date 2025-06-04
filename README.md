# Chlamy_AP-MS_Analysis_2025
This repository contains the code (Jupyter notebooks) and files needed to perform the analysis of our AP-MS dataset.

The following instructions can be used to reproduce the data analysis performed in our publication:

## Run contamination flagging on summary sheet of spectral counts
   Parameters:
   
   contamination_thresh = 4 (min number for a local max to be considered a possible source of contamination)
      
   decay_thresh = 0.5 (multiplied by previous neighbor from peak to determine max for a point to be flagged – eg must be less than half the previous point, otherwise it’s unlikely all the spectral counts are due to contamination)
      
   k = 1.4 (exponential decay constant, used to determine how many from local max to flag, with exponential decay curve where initial value is local max spectral counts)
      
   Exponential decay profile: y = x0 * exp(-kx)
   
   Note: your flagging results may vary from ours because we had some IP experiments that were not included in the summary sheet provided, so the chronological order of experiments run on the same gel/consecutively on the MS instrument is not totally preserved

## Run “WD Score Calculator for Phytozome.ipynb” to calculate WD scores
   You may need to change input filename in second cell
   
   After running, manually insert bait info from separate output file into main output (WD_counts1_zscore…) (you can insert with transpose in Excel)
   
   Note: DO NOT change the p value or z score sheets, only change the format for the WD score sheet
   
   Also run this for the contamination flagged spectral counts sheet (change which filename is active at the beginning of the Jupyter notebook)

## Run “Create Flag Matrix.ipynb” to create binary flagging matrix (1 for flagged, 0 for not flagged)
   Use the flagged spectral counts sheet as the input

## Run “Analyze WD and Z Scores, SAINT Scores, Heatmaps, TreeView for Phytozome.ipynb” for dataset stats
   Make localization match thresholding Venn diagrams, WD and Z score threshold profiles, etc
   
   Export nonzero interactions list
      
   Note: you will have to manually add % localization match column based on % localization match Venn diagram

## Run SAINTexpress:
   Made bait list, prey list (with number of residues for each prey), and interaction list input files
   
   Run SAINTexpress for spectral counts
   
   Analyze SAINT scores with “Analyze WD and Z Scores, SAINT Scores, Heatmaps, TreeView for Phytozome.ipynb”

## Cluster + TreeView 
   Download Cluster 3.0: http://bonsai.hgc.jp/~mdehoon/software/cluster/software.htm#ctv 
   
   Download TreeView: https://jtreeview.sourceforge.net/
   
   In “Analyze WD and Z Scores, SAINT Scores, Heatmaps, TreeView for Phytozome.ipynb”, go to TreeView section and run section to make localization match % matrix (gives each interaction the loc match % from Venn Diagram sections) with negative values for flagged interactions
   
   In Excel, make desired annotations to bait and prey CreIDs and put in the matrix for Cluster (row 1 = baits + annotations, col 1 = prey + annotations)
      
   - Can use =CONCATENATE in Excel
   - Save as tab delimited .txt
   
   Load this into the Cluster 3.0 software (cluster genes aka prey only, with centered correlation metric and average linkage method) and run clustering by clicking on a clustering method

   This will output a .cdt file – put this filename into the next section in the Jupyter notebook and run that section to sort baits in same order as clustered prey (create diagonal of self-interactions)
      
   - This gives heatmap of all interactions in dataset
   
   Load the output from this (another .cdt file) in TreeView to view heatmap, can adjust color scheme and intensity scaling as needed
In TreeView on startup: Help > Plugins > put in path: “/Applications/TreeView-1.2.0-osx/TreeView/plugins” > Scan for plugins > load file output from Cluster
      ~ Note: our files are available in this repository to load directly into TreeView (includes only high-confidence and medium-confidence interactions):
- "clusterBaitPrey_highConf_correlationCentered_avgLinkage_20240814_loc_match_matrix_negFlag_20240813_loc_match_matrix_20240724_WD_counts1_zscore_20240709_PBv6_BaitPreyInfo.cdt" (must unzip first)
- "clusterBaitPrey_highConf_correlationCentered_avgLinkage_20240814_loc_match_matrix_negFlag_20240813_loc_match_matrix_20240724_WD_counts1_zscore_20240709_PBv6_BaitPreyInfo.gtr"
- "clusterBaitPrey_highConf_correlationCentered_avgLinkage_20240814_loc_match_matrix_negFlag_20240813_loc_match_matrix_20240724_WD_counts1_zscore_20240709_PBv6_BaitPreyInfo.atr"

  ~ For these files, clustered on arrays (bait) and genes (prey), using similarity metric "Correlation (centered)" and the "Average Linkage" clustering method, values are the LM% for the interaction (negative values interactions flagged for contamination)
   
   In Excel, delete rows and columns in which no interactions are in high confidence list → zero out any values < 0.7 loc match % → repeat the process with this high-confidence interaction matrix to make a heatmap

## Cytoscape
   The interactions lists found in Supplemental Tables S2 and S4 can be uploaded into Cytoscape to create interaction networks and perform network analysis.

## Comparing Scaffold and MaxQuant
   "output_SAINTexpress_ScaffoldSpecCounts_FDR5.txt" contains output of SAINTexpress for spectral counts from Scaffold
   
   "output_SAINTexpress_AtlasMaxLFQ_FDR5.txt" contains output of SAINTexpress for intensities from MaxQuant
   
   Run "Analyze MaxQuant vs Spectral Counts.ipynb"
