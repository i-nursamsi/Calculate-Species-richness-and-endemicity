# Calculate-Species-richness-and-endemicity

# 
#----------------------------------------------------------
# ArcGIS Version:   Pro 1.0-2.x
# Python Version:   3.6
# Author: Ilyas Nursamsi
# Last revision: 2/1/2022
#-------------------------------------------------------------
###Finalpoints
import arcpy, csv, sys, string, os, glob, numpy, arcgisscripting
from arcpy import env
from arcpy.sa import *
arcpy.CheckOutExtension("Spatial")

arcpy.env.overwriteOutput = True
gp = arcgisscripting.create()


scriptPath = sys.path[0]
inHex = sys.argv[1]
inPtsObsShp = sys.argv[2]
inPtsObsField = sys.argv[3]
outFolder = sys.argv[4]
gridName = sys.argv[5]

arcpy.CreateFolder_management(outFolder,"TEMP_ilUli")
arcpy.CreateFolder_management(outFolder,"TEMP_ilUlii")
arcpy.CreateFolder_management(outFolder,"TEMP_ilUliii")
arcpy.CreateFolder_management(outFolder,"TEMP_ilUliiii")
arcpy.CreateFolder_management(outFolder,"TEMP_ilUliiiii")
arcpy.CreateFolder_management(outFolder,"TEMP_ilUlvi")
arcpy.CreateFolder_management(outFolder,"TEMP_ilUlvii")
arcpy.CreateFolder_management(outFolder,"TEMP_ilUlviii")
arcpy.CreateFolder_management(outFolder,"TEMP_ilUlviiii")
arcpy.CreateFolder_management(outFolder,"TEMP_ilUlxx")

outFolderTemp= outFolder + "/TEMP_ilUli"
outFolderTemp2= outFolder + "/TEMP_ilUlii"
outFolderTemp3= outFolder + "/TEMP_ilUliii"
outFolderTemp4= outFolder + "/TEMP_ilUliiii"
outFolderTemp5= outFolder + "/TEMP_ilUliiiii"
outFolderTemp6 = outFolder + "/TEMP_ilUlvi"
outFolderTemp7 = outFolder + "/TEMP_ilUlvii"
outFolderTemp8 = outFolder + "/TEMP_ilUlviii"
outFolderTemp9 = outFolder + "/TEMP_ilUlviiii"
outFolderTemp10 = outFolder + "/TEMP_ilUlxx"

TempShp1 = outFolderTemp + "/temp1.shp"
TempShp2 = outFolderTemp + "/temp2.shp"
outTable = outFolderTemp + "/tempTable.dbf"
gp.AddMessage("...setting up environment")


#create point for extraction of hex values
arcpy.FeatureToPoint_management(in_features=inHex, out_feature_class=TempShp1, point_location="INSIDE")

###calcuated resoltion of rasters
arcpy.gp.ZonalGeometryAsTable_sa(inHex,"FID",outTable,"#")
naAA = arcpy.da.TableToNumPyArray(outTable,"AREA")
valAA= numpy.mean(naAA["AREA"])
naPER = arcpy.da.TableToNumPyArray(outTable,"PERIMETER")
valPER= numpy.mean(naPER["PERIMETER"])
#change the number if resolution is too large
valDD=0.2*(valAA/valPER)
arcpy.env.cellSize = valDD

#Create centroids pts (nice for sampling values of coasts)
#arcpy.FeatureToPoint_management(inHex,outFolderTemp + "/temp1.shp","INSIDE")
arcpy.FeatureToRaster_conversion(inHex,"FID",outFolderTemp + "/temp",valDD)
arcpy.gp.Slice_sa(outFolderTemp + "/temp",outFolderTemp + "/" + "consTemp","1","EQUAL_INTERVAL","0")
ConstRas1= outFolderTemp + "/" + "consTemp"
outTempRas1 = ConstRas1
arcpy.CopyRaster_management(outTempRas1,outFolderTemp +"/TEMP231F.tif","","","","","","32_BIT_FLOAT")
arcpy.CopyRaster_management(outTempRas1,outFolderTemp +"/TEMP231B.tif","","","","","","32_BIT_FLOAT")
arcpy.CopyRaster_management(outTempRas1,outFolderTemp +"/TEMP231C.tif","","","","","","#")
arcpy.CopyRaster_management(outTempRas1,outFolderTemp +"/TEMP231D.tif","","","","","","32_BIT_FLOAT")
arcpy.CopyRaster_management(outTempRas1,outFolderTemp +"/TEMP231E.tif","","","","","","32_BIT_FLOAT")
arcpy.CopyRaster_management(outTempRas1,outFolderTemp +"/TEMP231G.tif","","","","","","32_BIT_FLOAT")
arcpy.CopyRaster_management(outTempRas1,outFolderTemp +"/TEMP231H.tif","","","","","","32_BIT_FLOAT")
outTempRas1 = outFolderTemp +"/TEMP231F.tif"
outTempRas2 = outFolderTemp +"/TEMP231B.tif"
outTempRas3 = outFolderTemp + "/TEMP231D.tif"
outTempRas4 = outFolderTemp + "/TEMP231E.tif"
outTempRas5 = outFolderTemp + "/TEMP231H.tif"
outTempRas6 = outFolderTemp + "/TEMP231G.tif"
TempShp1 = outFolderTemp + "/temp1.shp"
TempShp2 = outFolderTemp + "/temp2.shp"
TempShp3 = outFolderTemp + "/temp3.shp"
outMASK = outFolderTemp + "/TEMP231C.tif"
arcpy.env.extent = outFolderTemp + "/temp"
gp.AddMessage("spatial environment established")
#----------BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB----------  
##create a blank grid at resolution and extent of summing
#######CONVERT POINTS TO BUFFER THEN INSERT INTO ALL OTHER ANALYSIS
gp.AddMessage("separating points to separate files")
gp.AddMessage("....processing point data")


#Get the input feature class, optional fields and the output filename
inputFile = inPtsObsShp
inField = inPtsObsField
theFName = "#"
outFolderZ = outFolderTemp6
rows = arcpy.SearchCursor(inputFile)
row = rows.next()
attribute_types = set([])
cdz="attribute_types.add("+"row."+inField+")"

gp.AddMessage(attribute_types)
while row:
       #attribute_types.add(row.+inField+)
       eval(cdz)
       #gp.AddMessage(row.getValue(inField))
       row = rows.next()
# Output a Shapefile for each different attribute
gp.AddMessage(attribute_types)
for each_attribute in attribute_types:
       if theFName == "#" :
          outSHP = outFolderZ + "\\"+ each_attribute + u".shp"
       else:
          outSHP = outFolderZ + "\\"+ theFName+ each_attribute + u".shp"
       gp.AddMessage(outSHP)
       arcpy.Select_analysis(inputFile, outSHP, inField + " = '" + each_attribute + "'")    
del rows, row, attribute_types

###-----------------------------------------------


#if inPtsObsShp != "#":
#    theFilesAA = glob.glob(outFolderTemp6+"/*.shp")
#    inPtsObsDistN=float(inPtsObsDist)
#    for i in theFilesAA:
#      inSHP = str(i).replace("\\","/")
#      outName = os.path.split(inSHP)[1] 
#      outSHP = (outFolderTemp7 + "/" + str(outName)[:-4]+ ".shp").replace("\\","/")
#      gp.AddMessage("Applying buffer: " + outName)
#      try: 
#        arcpy.Buffer_analysis(inSHP,outSHP,inPtsObsDistN,"FULL","ROUND","ALL","#")
#      except: 
#        gp.AddMessage(gp.GetMessages())
##Convert ShapeFile to raster
if inPtsObsShp != "#":
    theFilesBB = glob.glob(outFolderTemp6+"/*.shp")
    for i in theFilesBB:
      inSHP = str(i).replace("\\","/")
      outName = os.path.split(inSHP)[1] 
      outTIF = (outFolderTemp7 + "/" + str(outName)[:-4]+ ".TIF").replace("\\","/")
      gp.AddMessage("Converting to raster: " + outName)
      try: 
        #arcpy.PolygonToRaster_conversion(inSHP,"FID",outTIF,"CELL_CENTER","NONE","#")
        arcpy.PointToRaster_conversion(inSHP,inField,outTIF,"MOST_FREQUENT","NONE","#")
      except: 
        gp.AddMessage(gp.GetMessages())
##Convert NoData Values to 0 pre-summation
if inPtsObsShp != "#":
    theFilesCC = glob.glob(outFolderTemp7+"/*.TIF")
    for i in theFilesCC:
      inTIF = str(i).replace("\\","/")
      outName = os.path.split(inTIF)[1] 
      outTIF = (outFolderTemp8 + "/" + str(outName)[:-4]+ ".TIF").replace("\\","/")
      gp.AddMessage("Changing NoData to Zero: " + outName)
      try: 
        arcpy.gp.Reclassify_sa(inTIF,"Value","0 1;NODATA 0",outTIF,"DATA") 
      except: 
        gp.AddMessage(gp.GetMessages())
###
if inPtsObsShp != "#":
    theFilesCC = glob.glob(outFolderTemp8+"/*.TIF")
    for i in theFilesCC:
      inTIF = str(i).replace("\\","/")
      outName = os.path.split(inTIF)[1] 
      outTIF = (outFolderTemp3  + "/" + str(outName)[:-4] + ".TIF").replace("\\","/")   
      #outTIF2 = (outFolderTemp3  + "/" + str(outName)[:-4] + ".TIF").replace("\\","/")
      gp.AddMessage("Converting to float: " + outName)
      try: 
         gp.CopyRaster_management(outFolderTemp + "/" + "consTemp",outFolderTemp + "/" + "consTemp2","#","#","#","NONE","NONE","#")
         gp.CopyRaster_management(outFolderTemp + "/" + "consTemp",outFolderTemp + "/" + "consTemp3","#","#","#","NONE","NONE","#")
         ConstRas2= (outFolderTemp + "/" + "consTemp2").replace("\\","/")
         arcpy.Mosaic_management(inTIF,ConstRas2,"MAXIMUM","FIRST","#","#","NONE","0","NONE")
         arcpy.gp.Float_sa(inTIF,outTIF)
      except: 
         gp.AddMessage(gp.GetMessages())

###******************************************************
##*******************************************************
###CREATE WE and RICH RASTERS FOR HEX
theFilesC = glob.glob(outFolderTemp3 + "/*.TIF")
for i in theFilesC:
  inTIF = str(i).replace("\\","/")
  outName = os.path.split(inTIF)[1] 
  outTIF = (outFolderTemp4 + "/" + str(outName)[:-4]+ "_DIV.TIF").replace("\\","/")
  WErasOUT = (outFolderTemp5 + "/" + str(outName)[:-4]+ "_WE.TIF").replace("\\","/")
  PARAMS='"""RASTERVALU "RASTERVALU" true true false 8 Double 0 0 ,First,#,'+TempShp2+',RASTERVALU,-1,-1"""'
  gp.AddMessage("Generating weighted layers: " + outName)
  
  try: 
    TempShp2 = outFolderTemp + "/temp2.shp"
    arcpy.gp.ZonalStatistics_sa(inHex,"FID",inTIF,outTIF,"MAXIMUM","DATA")
    arcpy.gp.ExtractValuesToPoints_sa(TempShp1,outTIF,TempShp2,"NONE","VALUE_ONLY")
    naZ = arcpy.da.TableToNumPyArray(TempShp2,"RASTERVALU")
    val2= numpy.count_nonzero(naZ["RASTERVALU"])
    arcpy.gp.Divide_sa(outTIF,val2,WErasOUT)
    arcpy.Delete_management(TempShp2)
  except: 
    gp.AddMessage(gp.GetMessages())

###Summing all WE rasters to create WE 
theFilesE = glob.glob(outFolderTemp5+"/*.TIF")
for i in theFilesE:
  inTIF = str(i).replace("\\","/")
  outName = os.path.split(inTIF)[1] 
  gp.AddMessage("Calculating weighted endemism. Processing: " + outName)
  try: 
    arcpy.gp.Plus_sa(inTIF,outTempRas1,outTempRas2)
    arcpy.CopyRaster_management(outTempRas2,outTempRas1,"","","","","","")
  except: 
    gp.AddMessage(gp.GetMessages())

###Summing of all occur rasters to create richness 
theFilesZ = glob.glob(outFolderTemp4+"/*.TIF")
for i in theFilesZ:
  inTIF = str(i).replace("\\","/")
  outName = os.path.split(inTIF)[1] 
  gp.AddMessage("Calculating richness. Processing: " + outName)
  try: 
    arcpy.gp.Plus_sa(inTIF,outTempRas3,outTempRas4)
    arcpy.CopyRaster_management(outTempRas4,outTempRas3,"","","","","","")
  except: 
    gp.AddMessage(gp.GetMessages())

gp.AddMessage("Creating final layers")

#Save final WE GRID

arcpy.Mosaic_management(outTempRas2,outTempRas5,"LAST","#","#","#","NONE","0","NONE")
arcpy.CopyRaster_management(outTempRas5,outFolder + "/" + gridName + "_WE.tif","","","","","","")
arcpy.CopyRaster_management(outTempRas3,outFolder + "/" + gridName + "_Spp_Rich.tif","","","","","","")

#ID final layers
InFinRich= outFolder + "/" + gridName + "_Spp_Rich.tif"
InFinWE=outFolder + "/" + gridName + "_WE.tif"
InFinCWE=outFolderTemp + "/" + gridName + "_CWE_M.tif"

###Create CWE layer
arcpy.gp.Divide_sa(InFinWE,InFinRich,InFinCWE)
arcpy.Mosaic_management(InFinCWE,outTempRas6,"LAST","#","#","#","NONE","0","NONE")
arcpy.CopyRaster_management(outTempRas6,outFolder + "/" + gridName + "_CWE.tif","","","","","","")

gp.AddMessage("Extracting biodiversity values to hexagon layer")
#extract Rich, CWE, and We to points
arcpy.Copy_management(TempShp1,outFolderTemp + "/" +"outMASK2_CP_PTS.shp","ShapeFile")
arcpy.Copy_management(TempShp1,outFolderTemp + "/" +"outMASK2_CP_PTS2.shp","ShapeFile")
arcpy.Copy_management(TempShp1,outFolderTemp + "/" +"outMASK2_CP_PTS3.shp","ShapeFile")
arcpy.Copy_management(TempShp1,outFolderTemp + "/" +"outMASK2_CP_PTS4.shp","ShapeFile")
arcpy.Copy_management(inHex,outFolderTemp + "/" +"outMASK2_CP.shp","ShapeFile")
FFmask=outFolderTemp + "/" +"outMASK2_CP_PTS.shp"
FFmask2=outFolderTemp + "/" +"outMASK2_CP_PTS2.shp"
FFmask3=outFolderTemp + "/" +"outMASK2_CP_PTS3.shp"
FFmask4=outFolderTemp + "/" +"outMASK2_CP_PTS4.shp"
FFmaskShp=outFolderTemp + "/" +"outMASK2_CP.shp"
  
gp.AddMessage("Cleaning up hexagon layer: part 1")

arcpy.gp.ExtractValuesToPoints_sa(FFmask,InFinRich,FFmask2,"NONE","VALUE_ONLY")
arcpy.AddField_management(FFmask2, "Richness", "DOUBLE", "#","","","","")
arcpy.CalculateField_management(FFmask2, "Richness", "!RASTERVALU!", "PYTHON","#")
arcpy.DeleteField_management(FFmask2,["RASTERVALU","Id","ORIG_FID"])
arcpy.gp.ExtractValuesToPoints_sa(FFmask2,InFinWE,FFmask3,"NONE","VALUE_ONLY")
arcpy.AddField_management(FFmask3, "WE", "DOUBLE", "#","","","","")
arcpy.CalculateField_management(FFmask3, "WE", "!RASTERVALU!", "PYTHON","#")
arcpy.DeleteField_management(FFmask3,["RASTERVALU","Id","ORIG_FID"])
arcpy.gp.ExtractValuesToPoints_sa(FFmask3,InFinCWE,FFmask4,"NONE","VALUE_ONLY")
arcpy.AddField_management(FFmask4, "CWE", "DOUBLE", "#","","","","")
arcpy.CalculateField_management(FFmask4, "CWE", "!RASTERVALU!", "PYTHON","#")
arcpy.DeleteField_management(FFmask4,["RASTERVALU","Id","ORIG_FID"])

gp.AddMessage("Cleaning up hexagon layer: part 2")

#join points with clipped grid
outLast16=outFolder+"/"+gridName+".shp"
arcpy.SpatialJoin_analysis(FFmaskShp,FFmask4,outLast16,"JOIN_ONE_TO_MANY","KEEP_ALL","","INTERSECT","","")
arcpy.DeleteField_management(outLast16,["TARGET_FID","JOIN_FID","Id","Join_Count","Input_FID","OBJECTID_1","Input_FI_1","Shape_le_1","Shape_Ar_1",])


gp.AddMessage("Cleaning up hexagon layer: part 3")
  
desc = arcpy.Describe(outLast16)
fields = desc.fields
rows = arcpy.UpdateCursor(outLast16)

for row in rows:
    if row.Richness == -9999:
        row.Richness = 0
    else:
        row.Richness = row.Richness
    rows.updateRow(row)

del rows

desc = arcpy.Describe(outLast16)
fields = desc.fields
rows = arcpy.UpdateCursor(outLast16)

for row in rows:
    if row.CWE == -9999:
        row.CWE = 0
    else:
        row.CWE = row.CWE
    rows.updateRow(row)

del rows

desc = arcpy.Describe(outLast16)
fields = desc.fields
rows = arcpy.UpdateCursor(outLast16)

for row in rows:
    if row.WE == -9999:
        row.WE = 0
    else:
        row.WE = row.WE
    rows.updateRow(row)

del rows

###Adds new files to open map
gp.AddMessage("Adding results to ArcMap")
#mxd = arcpy.mapping.MapDocument("CURRENT")
#df = arcpy.mapping.ListDataFrames(mxd)[0]
#addLayer4 = arcpy.mapping.Layer(outFolder + "/" + gridName + ".shp")
#arcpy.mapping.AddLayer(df, addLayer4, "AUTO_ARRANGE")

gp.AddMessage("Adding results to ArcMap")
p = arcpy.mp.ArcGISProject("CURRENT") # REPLACEMENT FOR MXD
m = p.listMaps()[0]  # REPLACEMENT FOR DF
scriptPath = (p.homeFolder+"\\Scripts\\MaxEnt.lyrx")
addLayer4 =  m.addDataFromPath((outFolder + "\\" + gridName + ".shp"))

import datetime
#create table of inputs into SDMtoolbox
#change outFolder if needed, out file if needed, and inputs names
#NOTE: If script contains a "glob" n- write the first occured of the glob as: + str(globNameHere)+ "\n" + 
newLine = ""
file = open(outFolder+"/"+gridName+".SDMtoolboxInputs", "w")
file.write(newLine)
file.close()
addlineH=str(datetime.datetime.now())+ "\n" + "Input Parameters \n" + "In Hex: " + str(inHex) + " \n" + "Input files: "+str(theFilesBB)+ "\n" + "In point file: " +str(inPtsObsShp) + " \n" + "Point field with sp name: "+ str(inPtsObsField)+ " \n" + "Out folder: "+ str(outFolder)+ " \n" + "Out file name: "+ str(gridName)
filedataZ=""
###special for 'GRID' function
#rasters=gp.listrasters("", "ALL")
#raster= Rasters.next
#while raster:
#    print raster
#    newLineR =str(raster)+ ", "+filedataZ
#    filedataZ=newLineR
#    raster = rasters.next()
#END of special for 'GRID' function
file = open(outFolder+"/"+gridName+".SDMtoolboxInputs", "r")
filedata = file.read()
file.close()
#DELETE +"\nRasters input: "
newLine =addlineH+"\nRasters input: "+filedataZ+filedata
file = open(outFolder+"/"+gridName+".SDMtoolboxInputs", "w")
file.write(newLine)
file.close()
gp.AddMessage("*******************************************")
gp.AddMessage("Table of inputs were output: "+outFolder+"\ExtractbyMask.SDMtoolboxInputs")
#####step1_change_output_name_appropriately. For example: outFolder+"/ExtractByMask.SDMtoolboxInputs". If fixed name leave within ""; if calling user input: "/"+gridName+".SDMtoolboxInputs" (CHANGING gridName to name input)
###change 'outFolder' to match outfolder sytax in focal script 
####step3_change inputs to match script Line:9 (from top of python script
###step4 paste at bottom before file deletes



gp.AddMessage("Cleaning up workspace")
arcpy.Delete_management(outFolderTemp + "/consTemp")
arcpy.Delete_management(outFolderTemp + "/consTemp2")
arcpy.Delete_management(outFolderTemp + "/consTemp3")
arcpy.Delete_management(InFinCWE)
arcpy.Delete_management(outFolderTemp + "/outMASK2_CP.shp")
arcpy.Delete_management(outFolderTemp + "/outMASK2_CP_PTS.shp")
arcpy.Delete_management(outFolderTemp + "/temp1.shp")
arcpy.Delete_management(outTempRas1)
arcpy.Delete_management(outTempRas2)
arcpy.Delete_management(outTempRas3)
arcpy.Delete_management(outTempRas4)
arcpy.Delete_management(outTempRas5)
arcpy.Delete_management(outTempRas6)
arcpy.Delete_management(outMASK)
arcpy.Delete_management(outFolderTemp + "/outMASK2_CP_PTS2.shp")
arcpy.Delete_management(outFolderTemp + "/outMASK2_CP_PTS3.shp")
arcpy.Delete_management(outFolderTemp + "/outMASK2_CP_PTS4.shp")
arcpy.Delete_management(outFolderTemp + "/temp")
arcpy.Delete_management(outTable)
arcpy.Delete_management(outFolderTemp)
arcpy.Delete_management(outFolderTemp2)
arcpy.Delete_management(outFolderTemp3)
arcpy.Delete_management(outFolderTemp4)
arcpy.Delete_management(outFolderTemp5)
arcpy.Delete_management(outFolderTemp6)
arcpy.Delete_management(outFolderTemp7)
arcpy.Delete_management(outFolderTemp8)
arcpy.Delete_management(outFolderTemp9)
arcpy.Delete_management(outFolderTemp10)
gp.AddMessage("Finished successfully")
del gp
































