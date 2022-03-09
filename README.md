# sgsload
A package used to load the Seurat single cell analysis object into SGS cellbrowser


## Installation
the package can install from github like this:

```
# install from github
devtools::install_github("bio-xtt/sgs_test") 

# install from source
install.packages("/home/bio-xtt/Desktop/sgsload_0.1.0.tar.gz", type=source)
```


## Usage

### Quick start
quickly loadding a scRNAseq object created by Seurat into SGS cellbrowser

```
# load the package
library(sgsload)

# get the speices id information
get_species_inform(user_id)

-> POST /api/species/list HTTP/1.1
-> Host: 47.74.241.105:6102
-> User-Agent: libcurl/7.68.0 r-curl/4.3.2 httr/1.4.2
-> Content-Type: application/json
-> Accept: application/json, text/javascript, */*; q=0.01
-> Accept-Encoding: gzip, deflate, br
-> Connection: keep-alive
-> Content-Length: 21
-> 
>> {"user_id":"user001"}

<- HTTP/1.0 200 OK
<- Content-Type: application/json
<- Content-Length: 427
<- Server: Werkzeug/2.0.1 Python/3.9.5
<- Date: Wed, 09 Mar 2022 07:15:35 GMT
<- 
get species id information successful!
{
  "species_list": [
    {
      "species_id": "11f4cd32439248149ac6a6ba46f91d00",
      "species_name": "mouse",
      "species_status": "done"
    },
    {
      "species_id": "25542c58734641fe98ba9f6b51d17a20",
      "species_name": "hg19",
      "species_status": "done"
    },
    {
      "species_id": "a7753f99184544eaaebbf6f22a2e5dfd",
      "species_name": "ecoli",
      "species_status": "done"
    }
  ]
} 

# load the example datasets: pbmc_small
data("pbmc_small")

# load the example marker genes: marker.tsv
marker_file <- system.file("extdata/scRNA", "markers.tsv", package = "sgsload")

# use the export function
result_json <- ExportToSGS(
  object = pbmc_small,
  species_id = "678d0bb42ebd4137b031ec5cc90dc0c5",
  track_name = "pbmc_small",
  track_type = "scRNA",
  select_group = c("groups", "RNA_snn_res.0.8", "letter.idents", "cluster"),
  assays = c("RNA"),
  matrix.slot = list("RNA" = "data"),
  assay.type = list("RNA" = "gene"),
  reductions = c("tsne", "umap"),
  marker.files = list("RNA" = marker_file) )
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
result <- ExportSC(
  object = atac,
  species_id = "678d0bb42ebd4137b031ec5cc90dc0c5",
  track_name = "atac",
  track_type = "scATAC",
  select_group = c("seurat_clusters"),
  assays = c("RNA", "chromvar", "peaks"),
  matrix.slot = list("RNA" = "data", "chromvar" = "data", "peaks" = "data"),
  assay.type = list("RNA" = "gene", "chromvar" = "motif", "peaks" = "peak"),
  reductions = c("lsi", "umap"),
  marker.files = markers)
```


## Result
the function returns the status of loadding single cell object into SGS cellbrowser:  

1:loadding successfull! 
2:loadding failed  
