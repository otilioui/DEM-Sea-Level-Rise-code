# DEM-Sea-Level-Rise-code
import arcpy
from arcpy import env

arcpy.env.overwriteOutput = True 
arcpy.env.workspace = r"F:\URI\Term Project\WC.gdb"

dem = r"F:\URI\Term Project\WC.gdb\WestCoastUSA"

arcpy.CheckOutExtension("spatial")

max_r = float(arcpy.GetRasterProperties_management(dem, "MAXIMUM").getOutput(0))
print(max_r)

scenarios = [0, 1, 2, 5, 25]

for num in scenarios:
    print("Analysing scenario showing {}m sea level rise".format(num))
    out_reclass = "Sealevel_{}m".format(num)
    
    remap = arcpy.sa.RemapValue([[-10, float(num), 1], [float(num), max_r, "NODATA"]])
    reclassed = arcpy.sa.Reclassify(dem, "VALUE", remap)
    
    arcpy.env.workspace = r"F:\URI\Term Project\WC.gdb"
    
    reclassed.save(out_reclass)
    
    arcpy.env.workspace = r"F:\URI\Term Project\WC.gdb"
    
    out_fc = "fcSealevel_{}m".format(num)
    out_poly = arcpy.conversion.RasterToPolygon(out_reclass, out_fc, "NO_SIMPLIFY", "VALUE")

arcpy.CheckInExtension("spatial")  

print("Finito! Analysis completed for all scenarios.")
