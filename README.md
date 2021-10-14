# Getting Started
To access this online tool, visit:\
https://Voineagulab.github.io/TDAview

## Examples
In addition to the two examples described in Walsh et al. Bioinformatics 2020 (RNAseq Neuronal Differentiation, RNAseq Prefrontal Cortex) here we describe 2 more example datasets.
The examples below are purely illustrative for the functionalities of TDAview, while the examples in Walsh et al. Bioinformatics 2020 show biologically relevant results.
#
You can view and download all four [example datasets](https://github.com/Voineagulab/tdaview/tree/master/example%20files/), or import them directly using the TDAview **examples** drop-down. Furthermore, the TDAview **load** tab contains precalculated mapper objects for the **RNAseq Neuronal Differentiation** and **RNAseq Prefrontal Cortex** with the parameters described in Walsh et al. 2020. For the **Climate Change** and **Falcon Heavy Launch** data sets below, we illustrate the effect of varying some of the Mapper parameters. 
# 
### Climate Change
Climate data from 1983 to 2008 compiled by [Mithilesh Kumar](https://www.kaggle.com/econdata/climate-change). A main feature of this data-set is its inclusion of various greenhouse gas levels alongside global temperature. We include in the [data](https://raw.githubusercontent.com/Voineagulab/tdaview/master/example%20files/Climate%20Change/climatedata.csv) file _(Year, Month, Temp)_, so each data point has two temporal coordinates (Year and Month), as well as a third coordinate (Temp). The [metadata](https://raw.githubusercontent.com/Voineagulab/tdaview/master/example%20files/Climate%20Change/climatemeta.csv) file contains all our candidate contributors to global warming _(MEI, CO2, CH4, N2O, CFC.11, CFC.12, TSI, Aerosols)_. The data-point IDs are defined by the combination of year and month.

To investigate these 308 data points, we use both Mapper1D and Mapper2D. Since each year has 12 data points we start by using 12 as a number of intervals. Since this is a low-dimensional (3-dimensional) dataset, we will use Euclidean distance as a distance metric. The graphs obtained by both mapper 1D and 2D clearly reflect the CO2 emissions (but not Aerosols). This example shows how Mapper1D and 2D results differ, with Mapper2D being a spatially refined representation of the Mapper1D graph.
![](https://raw.githubusercontent.com/Voineagulab/tdaview/master/example%20files/Climate%20Change/climate.png)

### Falcon Heavy Launch
Rocket data was originally parsed from the 2018 SpaceX stream, sourced with additional analysis from [shahar603](https://github.com/shahar603/Telemetry-Data/tree/master/Falcon%20Heavy%20Demo%201) and includes data for 519 time-points, 1s apart. We use as [data](https://raw.githubusercontent.com/Voineagulab/tdaview/master/example%20files/Falcon%20Heavy%20Rocket%20Launch/falcondata.csv) 
 velocity and acceleration measurements_(velocity, velocity_x, velocity_y, acceleration)_ three and investigate how the temporal variation of velocity and acceleration relate to [metadata](https://raw.githubusercontent.com/Voineagulab/tdaview/master/example%20files/Falcon%20Heavy%20Rocket%20Launch/falconmeta.csv)variables:_(altitude, downrange_distance, angle, stage)_ datasets. Data-point IDs are defined as the _time_ since the launch in seconds.
We use this 4-dimensional dataset of 519 data-points to illustrate how the choice of parameters influences the Mapper1D graph. The Mapper1D graph captures the temporal variation of velocity and acceleration with altitude. This result is robust to the choice of number of intervals (10,25 or 50), with the only difference being the number of nodes in the graph and the number of data points within each node. 
![](https://raw.githubusercontent.com/Voineagulab/tdaview/master/example%20files/Falcon%20Heavy%20Rocket%20Launch/falcon.png)

# Importing Data and Generating the Graph
## Loading Mapper Object
TDAview accepts JSON serialised Mapper files. These can be generated with TDAmapper and exported with:

```R
    library(jsonlite)
    write_json(mapper_object, outfile_path, force=TRUE)
```
Then navigate to this file in the Load tab.

## Running Mapper

While the Mapper algorithm is quite fast, generating distance matrices and running some filter functions can take time. For large datasets, using an R session to generate the mapper object and then using tdaview for visualisation is suggested.

TDAview requires as input files the DATA and METADATA in .CSV format, with the following formatting requirements:
  - DATA: Data matrix with columns specifying data points (eg. samples for inter-sample graphs or genes for co-expression graphs) and rows specifying measured features. The first column should contain feature names and the first row should contain data-point names. 
  - METADATA: The first column should contain the data-point names identical to the colum names of DATA. The order doesn't need to be identical, as TDAview will match the column names of DATA with the first column in METADATA. However, the number of datapoints should be the same in DATA and METADATA. 
  - MAPPER object: If using TDAview only for visulisation, a MAPPER object can be generated with the TDAmapper R package, and exported as a .json file using the write_json function in the jsonlite R library. This .jason file can then be uploaded in TDAview. Note that the DATA and METADATA files need to be imported even if the MAPPER object is upoaded, for the visualisation component of TDAview.
Example datasets are available in the EXAMPLES folder, and can also be loaded from TDAview's drop-down menu.

To remove the uploaded data refresh the browser page.

TDAview implements TDAmapper, which requires the following parameters for generating the graph. Further details explaining these parameters are available in the TDAview publication (Walsh et al. Bioinformatics 2020) and TDAmapper documentation:https://cran.r-project.org/web/packages/TDAmapper/index.html
- Filter dimensions: Mapper1D or Mapper2D
- Number of intervals
- Percent overlap
- Number of bins when clustering


### Technical Notes
Due to the PCA library used, Firefox can become prohibitively slow for calculating filter values. Chrome is recommended.
Note you should convert all empty columns or omit them before exporting to tdaview. JavaScript also struggles to parse numbers containing commas. TDAview's mapper2D uses the same number of intervals and percent overlap for both dimensions.

# Formatting Nodes
## Node Color
Nodes may be coloured by a uniform value or continuous/categorical variables. The gradient picker behaviour depends on which option is selected. Furthermore, legends are generated to highlight the relationship between these colors and the data.

### Uniform
Nodes are colored by a single value.

The gradient picker acts like a simple color picker. Hue can be chosen using the strip on the right, saturation and brightness are decided by the circle's position within the square.

There is no legend for this option.

### Continuous
Nodes are colored by the mean of their contained points for the variable chosen. These means are then normalised into a [0, 1] range where 0 is the leftermost side of the gradient and 1 the rightermost.

The gradient picker may be used to describe a gradient with no limitations on color, number or position of stops. Color stops may be added by double clicking on an empty part of the bar, or removed by double clicking directly on a stop. There must always be a minimum of two stops. Dragging stops repositions them. The currently selected stop is lighter, and editing the color picker's values modifies the properties of this stop.

The legend is a simple titled bar that show's the gradient chosen by the user. Minimum and maximum values are labeled at the left and right sides of the legend respectively.

### Categorical
Nodes are colored by a pie chart describing the proportional representation of categories for the points they contain.

The gradient picker behaves as a restricted multi-color chooser, where each step can be used to color an individual category. Note steps cannot be moved, added or removed.

## Node Size
* Uniform - all nodes are scaled to the same size.
* Number of points - all nodes are scaled to the number of points in their cluster
* Degree - all nodes are scaled to their number of edges
* Variable - all nodes are scaled to the mean of each cluster for a given _continuous_ variable

All values are normalised internally between a set minimum and maximum node size.

## Label Source
* None - all node labels are hidden
* Number of points - all node labels specify number of points in their cluster
* Degree - all node labels specify their number of edges
* Variable - all nodes labels specify the mean of each cluster for a given _continuous_ variable

## Label Color
* From background - automatically selects white or black to maximise readability
* Uniform - all label colors are set using the color picker

## Label Size
This value coincides with the actual page font size of the labels.

### Technical Notes
The node renderer is implemented in WebGL using ThreeJS and designed for accurate, anti-aliased pie charts at any zoom level with low-overhead color modification. As a result, colors are stored in a single 256 bit 1D texture, with nodes storing UV coordinates to this texture. Hence, for time complexity, changing the color is O(1) but changing the variable is O(n). Since linear filtering is used for sub-pixel accuracy, increasing the size of the texture is only important for gradients with a lot of stops. 

The number of categories for any given node is hardware-limited by the maximum attributes or maximum varyings available to the shader. The current approach is heavily dependant on branching and hence may be inefficient on old hardware - a separate shader could be written for continuous coloring for this reason.

While the WebGL approach should support very large graphs on recent hardware, labels still utilise HTML and hence panning, zooming and dragging nodes typically requires a full style recalculation.

Node labels are positioned by finding the maximum angle or "gap" between any two of its adjacent edges. Adjustments are made to this based on the contents of the label to prevent letters from overlapping with the node they pertain to. Other node or label overlaps are not considered for fast computation, but the force layout should intrinsically limit these occurrences.

# Formatting Edges
## Edge Color
* From nodes - all edges are colored as a linear gradient from their node source color to destination color. In the case of pie charts, these colors will be that of the largest slice
* Uniform - all edges are colored using the color picker
# Edge Opacity
This value defines how much the edge should be mixed with the background, where 1.0 indicates no mixing. It is not true transparency since edges still occlude each other.
# Edge Thickness
This value defines the proportion of the edge width the source and destination node radii, where 1.0 ensures the edge fully encloses it's connected nodes.

## Label Source
* None - all edge labels are hidden
* Points in common - all edge labels specify number of points in the intersection of the source and destination node clusters

## Label Color
* From background - automatically selects white or black to maximise readability
* Uniform - all label colors are set using the color picker

## Label Size
This value coincides with the actual page font size of the labels.

### Technical Notes
Due to the customisability of the edge widths, they they are drawn using geometry rather than gl.LINES. This can result in more noticeable aliasing for thin edges, so it is suggested the opacity be lowered. Rendering at higher resolutions and downsampling can eliminate these artefacts.

# Graph Controls
## Navigation
Dragging in empty space pans the view, while dragging the zoom slider changes the size of the graph where 0.0 corresponds to the graph filling the view with a small margin.

## Selection
Clicking a node renders it selected, and a small pane appears at the bottom of the sidebar to view and download the data of this cluster. Similar functionality is available when clicking on edges.

### Technical Notes
Since the node renderer uses gl.POINTS to display nodes, nodes may disappear on some hardware when zoomed in very far. Saving to PDF should still display these nodes since it does not use WebGL.
