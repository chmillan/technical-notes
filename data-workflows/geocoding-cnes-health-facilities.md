# Geocoding Health Facilities in Brazil: Recovering Missing Coordinates from CNES Data
## 1. Context

This note documents a geocoding effort developed as part of the Connectivity in Healthcare (Conectividade na Saúde) project, conducted in cooperation with the Brazilian Ministry of Health.

The project aims to support connectivity expansion to 18,000 Basic Health Units (UBS) across Brazil.

During data preparation, approximately 4,000 health facilities in the CNES database lacked geographic coordinates, which limited spatial analysis of connectivity coverage and infrastructure planning.

This note records the practical approach used to recover missing locations and some observations about geocoding health facility data in Brazil.


## 2. Why this matters

Reliable geographic coordinates are essential for spatial analysis of healthcare infrastructure. In the Connectivity in Healthcare project, facility locations are used to:

- analyze internet connectivity coverage
- identify underserved areas
- support infrastructure deployment planning

Missing geolocations reduce the ability to perform these analyses. Recovering coordinates therefore improves the quality of evidence used for connectivity policy decisions.

## 3. Workflow

**The problem with CNES addresses**

In principle, geocoding requires a structured address (street, number, city, state).In practice, CNES address data frequently deviates from this format.

Common issues include:
- generic location descriptions (e.g. Sítio, Fazenda, Povoado)
- missing street numbers
- inconsistent formatting across address fields
- municipality names with encoding inconsistencies
- facilities known locally by name rather than address

These issues are particularly common in rural municipalities, where formal street addressing is less standardized.

**Geocoding strategy**

The approach followed a simple iterative strategy:

1. Attempt geocoding using the available address fields
2. Reprocess unmatched records with adjusted search queries
3. Include facility names when address-based searches failed
4. Geocoding queries were executed using the OpenStreetMap Nominatim API.

Address completeness varies substantially across records, particularly for rural facilities.

**Address Normalization**

Before geocoding, address fields were standardized to reduce inconsistencies in formatting.

Example:

```r
library(stringr)

normalize_address <- function(x) {
  x |>
    str_to_upper() |>
    str_trim()
}
```
**Detecting low information addresses**

Some facilities are located in rural areas where formal street addressing is limited. In these cases, the address field often contains generic descriptors such as sítio, fazenda, or estrada.

A heuristic function was used to detect such cases:

```r
is_generic_place <- function(logradouro_norm) {
  s <- str_to_upper(logradouro_norm)

  str_detect(
    s,
    "\\b(SITIO|SÍTIO|FAZENDA|POVOADO|ESTRADA|BR\\b|RODOVIA|SENTIDO|VIA DE CIRCULACAO|VILA\\b)"
  )
}
```

**Geocoding via OpenStreetMap**

Geocoding queries were performed using the Nominatim API, which provides search access to OpenStreetMap data.

Example query:

```r
library(httr)

query_nominatim <- function(address) {

  url <- "https://nominatim.openstreetmap.org/search"

  res <- GET(
    url,
    query = list(
      q = address,
      format = "json",
      limit = 1
    )
  )

  content(res, as = "parsed")
}
```
Each query attempted to retrieve latitude and longitude for the provided address string.


**First round**: direct geocoding attempt using the full address information available in the dataset.

**Second round**:records without coordinates were reprocessed after adjustments to the query string and address formatting.

**Third round**:for the remaining unmatched cases, the facility name was included in the search query, as some establishments are indexed in OpenStreetMap by name rather than address.


## 4. Limitations

Some establishments could not be geolocated due to: incomplete address information, rural descriptions without identifiable reference points,absence from OpenStreetMap databases

These cases may require manual verification or alternative administrative sources.

## 5. Reproducibility

The geolocation workflow was implemented in R, using standard packages for text processing, API requests, and data manipulation.