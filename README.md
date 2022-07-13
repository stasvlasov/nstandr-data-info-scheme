
# Table of Contents

1.  [JSON Schema for describing and coding datasets](#orgc5773be)
2.  [List of coded datasets](#org5369291)



<a id="orgc5773be"></a>

# JSON Schema for describing and coding datasets

With this schema I describe and code datasets that provide standard organizational names in order to benchmark name standardization procedures of [nstandr](https://github.com/stasvlasov/nstandr) R package.

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


<a id="org5369291"></a>

# List of coded datasets

I described and coded the following datasets in plain yaml formal so it can be easealy asseced with [papis (powerful and highly extensible command-line based document and bibliography manager)](https://github.com/papis/papis).

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

<table id="orgd3bfd2a" border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">
<caption class="t-above"><span class="table-number">Table 1:</span> Datasets with Standardized Organizational Names</caption>

<colgroup>
<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left">Reference</th>
<th scope="col" class="org-left">link</th>
<th scope="col" class="org-left">Deployment Scripts</th>
<th scope="col" class="org-left">GS Citations</th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left"><a href="@magerman2006">@magerman2006</a></td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/eee-ppat">eee-ppat</a></td>
<td class="org-left"><a href="https://github.com/stasvlasov">https://github.com/stasvlasov</a></td>
<td class="org-left"><b>119</b> as of 2022-06-02</td>
</tr>


<tr>
<td class="org-left"><a href="@Coffano2014">@Coffano2014</a></td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/icrios">icrios</a></td>
<td class="org-left"><a href="https://github.com/stasvlasov">https://github.com/stasvlasov</a></td>
<td class="org-left"><b>62</b> as of 2022-06-02</td>
</tr>


<tr>
<td class="org-left"><a href="@Thoma2010">@Thoma2010</a></td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/mdpt">mdpt</a></td>
<td class="org-left"><a href="https://github.com/stasvlasov">https://github.com/stasvlasov</a></td>
<td class="org-left"><b>140</b> as of 2022-06-02</td>
</tr>


<tr>
<td class="org-left"><a href="@Hall2001">@Hall2001</a></td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/nber-pdp">nber-pdp</a></td>
<td class="org-left"><a href="https://github.com/stasvlasov">https://github.com/stasvlasov</a></td>
<td class="org-left"><b>4869</b> as of 2022-06-02</td>
</tr>


<tr>
<td class="org-left">cite:@</td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/oecd-han">oecd-han</a></td>
<td class="org-left"><a href="https://github.com/stasvlasov">https://github.com/stasvlasov</a></td>
<td class="org-left">&#xa0;</td>
</tr>
</tbody>
</table>

