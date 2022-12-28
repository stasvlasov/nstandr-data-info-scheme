

# JSON Schema for describing and coding datasets

With this schema I describe and code datasets that provide standard organizational names in order to benchmark name standardization procedures of [nstandr](https://github.com/stasvlasov/nstandr) R package.

    {
      "$schema": "https://json-schema.org/draft/2019-09/schema",
      "$id": "<<github-link-to-tangled-file()>>",
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
            "ref": {
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
                  "type": "string"
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
              "description": "Source code or software used for creating this dataset",
              "type": "array",
              "items": { "$ref": "#/$defs/src" }
            },
            "scripts": {
              "description": "Scripts for working with this dataset. E.g., data deployment, data derivations, etc.",
              "type": "array",
              "items": { "$ref": "#/$defs/file" }
            },
            "files": {
              "description": "List of data files",
              "type": "array",
              "items": {
                "allOf": [
                  { "$ref": "#/$defs/file" },
                  {
                    "type": "object",
                    "properties": {
                      "period": {
                        "description": "Time period that the data files describe",
                        "type": "string"
                      },
                      "sources": {
                        "description": "Sources used for making the data files", 
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


# JSON Schema cli tools and oneliners

Generate schema from multiple files

    tmp_json_data_array=$(mktemp) && yq eval-all --output-format=json '[.data] as $item ireduce ([]; . *+ $item )' $(find . -name 'info.yml') > $tmp_json_data_array && generate-schema --json-schema $tmp_json_data_array | jq ' {"$schema"} + .items' > schema2.json
    
    # remove the 'items' in schema ~jq ' {"$schema"} + .items ' schema.jsonq~

Validate all `info.yml` files in current directory

    schema_json=$(mktemp) && curl https://raw.githubusercontent.com/stasvlasov/nstandr-data-info-scheme/master/nstandr-data-info-scheme.json -o "$schema_json" && for file in $(find . -name 'info.yml'); do echo "$file" ; tmp_json=$(mktemp) && {yq --output-format=json '.data' "$file" > $tmp_json ; ajv validate --all-errors --errors=text -s "$schema_json" -d "$tmp_json" ; rm "$tmp_json"}; done
    
    
    for file in $(find . -name 'info.yml'); do echo "$file" ; tmp_json=$(mktemp data-info.json) && {yq --output-format=json '.data' "$file" > $tmp_json ; ajv validate --all-errors --errors=text -s ~/org/research/nstandr-data-info-scheme/nstandr-data-info-scheme.json -d "$tmp_json" ; rm "$tmp_json"}; done
    
    # taking apart
    yq --output-format=json '.data' info.yaml > info.json
    
    ajv validate --all-errors --errors=text -s ~/org/research/nstandr-data-info-scheme/nstandr-data-info-scheme.json -d info
    
    mktemp data-info.json
    
    # without mktemp
    for file in $(find . -name "info.yml"); do yq --output-format=json ".data" "$file" > "$file.json" && ajv validate --spec=draft2019 --all-errors --errors=text -s ~/org/research/nstandr-data-info-schema/nstandr-data-info-schema.json -d "$file.json" && rm "$file.json"; done

    
    file= && yq --output-format=json ".data" "$file" > "$file.json" && ajv validate --spec=draft2019 --all-errors --errors=text -s ~/org/research/nstandr-data-info-schema/nstandr-data-info-schema.json -d "$file.json" && rm "$file.json"

note: in `mktemp --suffix=.json` &#x2013;suffix does not work on MacOS


# NoWeb Snippets (injecting)

    (concat
     (thread-last
       (shell-command-to-string "git config --get remote.origin.url")
       (string-replace ".git"
                       "/master/")
       (string-replace "https://github.com/"
                       "https://raw.githubusercontent.com/")
       (string-trim))
     (save-excursion (org-babel-goto-named-src-block "nstandr-data-info-schema")
                     (thread-last (org-babel-get-src-block-info 'light)
                                  (nth 2)
                                  (alist-get :tangle))))

    (save-excursion
      (mapconcat
       'identity
       (org-map-entries
        (lambda ()
          (org-babel-next-src-block)
          (let ((title (org-no-properties (org-get-heading 'no-tags)))
                (tags (org-entry-get nil "script_tags"))
                (url (thread-last (org-babel-get-src-block-info 'light)
                                  (nth 2)
                                  (nth 2)
                                  (cdr))))
            (concat "- title: " title "\n"
                    "  tags: [" tags "]\n"
                    "  url: " url)))
        "script_tags={.}" 'file)
         "\n"))

    papis --clear-cache
    papis export --format=yaml "ref:$ref"


# List of coded datasets

I described and coded the following datasets in plain yaml formal so it can be easealy asseced with [papis (powerful and highly extensible command-line based document and bibliography manager)](https://github.com/papis/papis).

<table id="org42cb33f" border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">
<caption class="t-above"><span class="table-number">Table 1:</span> Datasets with Standardized Organizational Names</caption>

<colgroup>
<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left">Reference</th>
<th scope="col" class="org-left">link</th>
<th scope="col" class="org-left">Data Size</th>
<th scope="col" class="org-left"><a href="https://github.com/stasvlasov/nstandr-data-info-scheme">schema</a></th>
<th scope="col" class="org-left">GS Citations</th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left"><a href="@magerman2006">@magerman2006</a></td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/eee-ppat">eee-ppat</a></td>
<td class="org-left">12.9 Gb</td>
<td class="org-left">valid</td>
<td class="org-left"><b>119</b> as of 2022-06-02</td>
</tr>


<tr>
<td class="org-left"><a href="@Coffano2014">@Coffano2014</a></td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/icrios">icrios</a></td>
<td class="org-left">110.6 Mb</td>
<td class="org-left">valid</td>
<td class="org-left"><b>62</b> as of 2022-06-02</td>
</tr>


<tr>
<td class="org-left"><a href="@Thoma2010">@Thoma2010</a></td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/mdpt">mdpt</a></td>
<td class="org-left">1.1 Gb</td>
<td class="org-left">valid</td>
<td class="org-left"><b>140</b> as of 2022-06-02</td>
</tr>


<tr>
<td class="org-left"><a href="@Hall2001">@Hall2001</a></td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/nber-pdp">nber-pdp</a></td>
<td class="org-left">6.3 Gb</td>
<td class="org-left">valid</td>
<td class="org-left"><b>4869</b> as of 2022-06-02</td>
</tr>


<tr>
<td class="org-left">cite:@</td>
<td class="org-left"><a href="file:///Users/stas/org/data/patents/oecd-han">oecd-han</a></td>
<td class="org-left">4.6 Gb</td>
<td class="org-left">valid</td>
<td class="org-left">&#xa0;</td>
</tr>
</tbody>
</table>

The table above was generated with the following R script from the data description files (info.yml) on my computer using R package [papisr](https://github.com/stasvlasov/papisr).

    "~/org/data/patents" |>
        papisr::collect_papis_records() |>
        papisr::tabulate_papis_records(
                    `Reference` = paste0("cite:@", info$ref, "")
                  , `link` = paste0("[[", path, "][" , basename(path), "]]")
                  , `Size` =
                        romRDS::rom_rds(paste0("data_size_", basename(path)) , {
                        path |>
                        file.path("data") |>
                        list.files(recursive = TRUE, full.names = TRUE) |>
                        sapply(file.size) |>
                        sum() |>
                        utils:::format.object_size("auto")
                        })
                  ## , `Deployment Scripts` = "https://github.com/stasvlasov"
                  , `[[https://github.com/stasvlasov/nstandr-data-info-scheme][info.yml]]` = {
                      exit_code <- system(paste0('file="', path, '/info.yml" && yq --output-format=json ".data" "$file" > "$file.json" && ajv validate --spec=draft2019 --all-errors --errors=text -s ~/org/research/nstandr-data-info-schema/nstandr-data-info-schema.json -d "$file.json" && rm "$file.json"'))
                      if(exit_code == 0) {'valid'} else {'invalid'}
                  }
                  , `GS Citations` =
                        if(!is.null(info$google_scholar_citations)) {
                            paste0(
                                "*", info$google_scholar_citations[[1]]$count, "*"
                              , " as of ", info$google_scholar_citations[[1]]$date
                            )
                        }
                )

