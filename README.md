# sgsload
A package used to load the Seurat single cell analysis object into SGS cellbrowser


## Installation
the package can install from github like this:

```
# install from github
devtools::install_github("bio-xtt/sgsload") 

# install from source
install.packages("/home/bio-xtt/Desktop/sgsload_0.1.0.tar.gz", type=source)
```


## Usage

### Quick start
quickly loadding a scRNAseq object created by Seurat into SGS cellbrowser

```
# load the package
library(sgsload)

# get the speices id information, the default userid is "user001".
get_species_inform(user_id)

```

```
# load the example datasets: pbmc_small
data("pbmc_small")

# load the example marker genes: marker.tsv
marker_file <- system.file("extdata/scRNA", "markers.tsv", package = "sgsload")

# use the export function
result_json <- ExportSC(
  object = pbmc_small,
  species_id = "25542c58734641fe98ba9f6b51d17a20",
  track_name = "pbmc_small",
  track_type = "scRNA",
  select_group = c("groups", "RNA_snn_res.0.8", "letter.idents", "cluster"),
  assays = "RNA",
  matrix.slot = list("RNA" = "data"),
  assay.type = list("RNA" = "gene"),
  reductions = c("tsne", "umap"),
  marker.files = list("RNA" = marker_file))
```


### Example1:
Loadding a scATAC object created by Signac into SGS cellbrowser 


```
# load the package
library(sgsload)

# get the speices id information
get_species_inform(user_id)

# load the example datasets: pbmc_small
data("atac")

# load the example marker files created by different assay: marker_gene.tsv、marker_motif.tsv、marker_peak.tsv
marker_genes <- system.file("extdata/scATAC", "marker_genes.tsv", package = "sgsload")
marker_motifs <- system.file("extdata/scATAC", "marker_motif.tsv", package = "sgsload")
marker_peaks <- system.file("extdata/scATAC", "marker_peaks.tsv", package = "sgsload")

# create a marker file list and name the list with the correlated assay name
markers <- list(marker_genes, marker_motifs, marker_peaks)
names(markers) <- c("RNA", "chromvar", "peaks")

# if you changed the fragments file path, you should update the fragments file path sorted in the signac object
new.path <- system.file("extdata/scATAC", "fragments.tsv.gz", package = "sgsload")
fragments <- CreateFragmentObject(
  path = new.path,
  cells = colnames(atac),
  validate.fragments = TRUE )
Fragments(atac) <- NULL
Fragments(atac) <- fragments

# use the export function to loadding the scATAC object into SGS cellbrowser
result_json2 <- ExportSC(
  object = atac,
  species_id = "25542c58734641fe98ba9f6b51d17a20",
  track_name = "atac_demo",
  track_type = "scATAC",
  select_group = c("seurat_clusters"),
  assays = c("RNA", "chromvar", "peaks"),
  matrix.slot = list("RNA" = "data", "chromvar" = "data", "peaks" = "data"),
  assay.type = list("RNA" = "gene", "chromvar" = "motif", "peaks" = "peak"),
  reductions = "umap",
  marker.files = markers)
```


## Result
the function returns the status of loadding single cell object into SGS cellbrowser:  

1:loadding successfull! 

2:loadding failed  
