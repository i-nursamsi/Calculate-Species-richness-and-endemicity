This tool estimate three common biodiversity metrics: species richness, weighted endemism and corrected weighted endemism. The input file format of this tool is occurence points. This tool  calculates these metrics for tessellated hexagons created by the 5a. Create tessellated hexgons of regiontool (Basic tools: Shapefile and CSV tools). 


Species Richness(SR) is sum of species per cell. 

SR = K (the total number of species in a grid cell)


Weighted Endemism (WE), which is the sum of the reciprocal of the total number of cells each species in a grid cell is found in. A WE emphasizes areas that have a high proportion of species with restricted ranges.

WE = ∑ 1/C (C is the number of grid cells each endemic occurs in)


Corrected Weighted Endemism(CWE). The corrected weighted endemism is simply the weighted endemism divided by the total number of species in a cell. A CWE emphasizes areas that have a high proportion of species with restricted ranges, but are not necessarily areas that are species rich. 

CWE = WE/K (K is the total number of species in a grid cell)
