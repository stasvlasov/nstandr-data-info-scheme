---
author: Stas Vlasov
---

```{=org}
#+export_file_name: README.md
```
# JSON Schema for describing and coding datasets

With this schema I describe and code datasets that provide standard
organizational names in order to benchmark name standardization
procedures of [nstandr](https://github.com/stasvlasov/nstandr) R
package.

``` {#schema .javascript org-language="js"}
{
  "$schema": "https://json-schema.org/draft/2019-09/schema",
  "$id": "nil",
  "$defs": {
    "info": {
      "type": "object",
      "properties": {
        "name": {
          "type": "string"
        },
        "title": {
          "type": "string"
        },
        "description": {
          "type": "string"
        },
        "tags": {
          "type": "array",
          "items": {
            "type": "string"
          }
        },
        "docs": {
          "type": "array",
          "items": { "$ref": "#/$defs/file" }
        }
      }
    },
    "file": {
      "allOf": [
        { "$ref": "#/$defs/info" },
        {
          "type": "object",
          "properties": {
            "url": {
              "type": "string"
            },
            "modified": {
              "type": "number"
            },
            "created": {
              "type": "string"
            }
          }
        }
      ]
    },
    "src": {
      "allOf": [
        { "$ref": "#/$defs/file" },
        {
          "type": "object",
          "properties": {
            "lang": {
              "type": "string"
            },
            "developers": {
              "type": "array",
              "items": {
                "type": "string"
              }
            },
            "implementations": {
              "type": "array",
              "items": { "$ref": "#/$defs/src" }
            }
          }
        }
      ]
    }
  },
  "allOf": [
    { "$ref": "#/$defs/info" },
    {
      "type": "object",
      "properties": {
        "src": {
          "type": "array",
          "items": { "$ref": "#/$defs/src" }
        },
        "scripts": {
          "type": "array",
          "items": { "$ref": "#/$defs/file" }
        },
        "files": {
          "type": "array",
          "items": {
            "allOf": [
              { "$ref": "#/$defs/file" },
              {
                "type": "object",
                "properties": {
                  "period": {
                    "type": "string"
                  },
                  "sources": {
                    "type": "array",
                    "items": { "$ref": "#/$defs/info" }
                  }
                }
              }
            ]
          }
        }
      }
    }
  ]
}
```

# List of coded datasets

I described and coded the following datasets in plain yaml formal so it
can be easealy asseced with [papis (powerful and highly extensible
command-line based document and bibliography
manager)](https://github.com/papis/papis).

``` {#tabulate-datasets .r org-language="R"}
"~/org/data" |>
    papisr::collect_papis_records() |>
    papisr::tabulate_papis_records(
                `Reference` = paste0("cite:@", info$ref, "")
              , `link` = paste0("[[", path, "][" , basename(path), "]]")
              ## , `Deployed Data Size` =
              ##       path |>
              ##       file.path("data") |>
              ##       list.files(recursive = TRUE, full.names = TRUE) |>
              ##       sapply(file.size) |>
              ##       sum() |>
              ##       utils:::format.object_size("auto")
              , `Deployment Scripts` = "https://github.com/stasvlasov"
              , `GS Citations` =
                    if(!is.null(info$google_scholar_citations)) {
                        paste0(
                            "*", info$google_scholar_citations[[1]]$count, "*"
                          , " as of ", info$google_scholar_citations[[1]]$date
                        )
                    }
            )
```

::: {#tabulate-datasets-results}
  Reference            link                                                Deployment Scripts                GS Citations
  -------------------- --------------------------------------------------- --------------------------------- ---------------------------
  cite:@magerman2006   [eee-ppat](/Users/stas/org/data/patents/eee-ppat)   <https://github.com/stasvlasov>   **119** as of 2022-06-02
  cite:@Coffano2014    [icrios](/Users/stas/org/data/patents/icrios)       <https://github.com/stasvlasov>   **62** as of 2022-06-02
  cite:@Thoma2010      [mdpt](/Users/stas/org/data/patents/mdpt)           <https://github.com/stasvlasov>   **140** as of 2022-06-02
  cite:@Hall2001       [nber-pdp](/Users/stas/org/data/patents/nber-pdp)   <https://github.com/stasvlasov>   **4869** as of 2022-06-02
  cite:@               [oecd-han](/Users/stas/org/data/patents/oecd-han)   <https://github.com/stasvlasov>   

  : Table 1: []{#tabulate-datasets-results}Datasets with Standardized
  Organizational Names
:::
