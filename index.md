---
title: "Catalog of IDR images formatted as OME-NGFF"
layout: default
---
<script type="application/ld+json">
{
  "@context": "http://schema.org",
  "@type": "DataCatalog",
  "inLanguage": "en-US",
  "name": "IDR OME-NGFF Samples",
  "publisher": {
    "@type": "Organization",
    "name": "GitHub"
  },
  "copyrightYear": "2022",
  "discussionUrl": "https://github.com/IDR/ome-ngff-samples/issues",

  "accessMode": "visual",
  "measurementTechnique": "microscopy"

}
</script>

<style>
    .page-content .wrapper {
        box-sizing: border-box;
        width: 100%;
        max-width: 100%;
    }
    .dataTables_scrollHeadInner {
        margin: 0 auto;
    }
    .icon {
        width: 24px;
        height: 24px;
    }
    .icon.vizarr {
        width: 27px;
    }
    .no_border {
        border: none;
        background: none;
        padding: 0;
    }
    .shake {
        animation: 0.1s linear 0s infinite alternate seesaw;
    }

    @-webkit-keyframes seesaw { from { transform: rotate(-0.05turn) } to { transform: rotate(0.05turn); }  }
    @keyframes seesaw { from { transform: rotate(-0.05turn) } to { transform: rotate(0.05turn); }  }
</style>

<p>To download samples using the AWS CLI with a custom S3-compatible endpoint:</p>

<code>
$ aws s3 sync --endpoint-url https://uk1s3.embassy.ebi.ac.uk --no-sign-request s3://idr/zarr/v0.4/idr0062A/6001240.zarr/ 6001240.zarr
</code>

<table class="display table" id="table">
    <thead>
<!-- TODO: should be read from data file -->
        <tr>
            <th>OME-NGFF version</th>
            <th>Thumbnail</th>
            <th>EMBL-EBI S3 key</th>
            <th>SizeX</th>
            <th>SizeY</th>
            <th>SizeZ</th>
            <th>SizeC</th>
            <th>SizeT</th>
            <th>Axes</th>
            <th>Wells</th>
            <th>Fields</th>
            <th>Keywords</th>
            <th>License</th>
            <th>Study</th>
            <th>DOI</th>
            <th>Date added</th>
        </tr>
    </thead>
    <tbody>
{% assign s3key = "EMBL-EBI bucket (current)" %}
{% assign studykey = "Study" %}
{% for rec in site.data.table %}
{% capture image_name %}{{ rec[s3key] | split: "/" | last }}{% endcapture %}
{% capture image_id %}{{ image_name | split: "." | first}}{% endcapture %}
        <tr>
            <td>{{ rec["OME-NGFF version"] }}</td>
            <td>
                <a target="_blank"
                    title="Open NGFF {% if rec['Wells'] %}Plate{% else %}Image{% endif %} in Vizarr"
                    href="http://hms-dbmi.github.io/vizarr/?source={{ rec[s3key] }}">
                    <img
                        alt="IDR thumbnail for Image:{{ rec["Representative Image ID"] }}"
                        style="margin:0"
                        src="https://idr.openmicroscopy.org/webclient/render_thumbnail/{{ rec["Representative Image ID"] }}/"
                    />
                </a>
            </td>
            <td>
                <a href="{{ rec[s3key] }}">
                    {{ image_name }}
                </a><br>
                <button class="no_border" title="Show download options" onclick="showS3Options('{{ rec[s3key] }}', '{{ image_name }}')">
                    <img class="icon" src="assets/img/copy.png"/>
                </button>
                <!-- vizarr supports Plate or Non-bioformats2raw images -->
                {% if rec['Wells'] or rec['Keywords'] != "bioformats2raw.layout" %}
                <a title="View NGFF {% if rec['Wells'] %}Plate{% else %}Image{% endif %} in Vizarr" target="_blank"
                    href="http://hms-dbmi.github.io/vizarr/?source={{ rec[s3key] }}">
                    <img class="icon vizarr" src="assets/img/vizarr_logo.png"/></a>
                {% endif %}
                <a title="Validate NGFF with 'ome-ngff-validator' in new browser tab" target="_blank"
                    href="https://ome.github.io/ome-ngff-validator/?source={{ rec[s3key] }}">
                    <img class="icon" style="opacity: 0.5" src="assets/img/check.png"/></a>
                {% unless rec['Wells'] %}
                {% unless rec["OME-NGFF version"] == "0.1" or rec["OME-NGFF version"] == "0.2" or rec["OME-NGFF version"] == "0.3" %}
                <a title="Open with itk-vtk-viewer in new browser tab" target="_blank"
                    href="https://kitware.github.io/itk-vtk-viewer/app/?rotate=false&fileToLoad={{ rec[s3key] }}">
                    <img class="icon" src="assets/img/itkvtk_logo.png"/></a>
                {% endunless %}
                {% endunless %}
            </td>
            <td>{{ rec["SizeX"] }}</td>
            <td>{{ rec["SizeY"] }}</td>
            <td>{{ rec["SizeZ"] }}</td>
            <td>{{ rec["SizeC"] }}</td>
            <td>{{ rec["SizeT"] }}</td>
            <td>{{ rec["Axes"] }}</td>
            <td>{{ rec["Wells"] }}</td>
            <td>{{ rec["Fields"] }}</td>
            <td>{{ rec["Keywords"] }}</td>
            <td>{{ rec["License"] }}</td>
            <td>
                <a href="https://idr.openmicroscopy.org/search/?query=Name:{{ rec[studykey] }}">
                    {{ rec["Study"] }}
                </a>
                <br>
                {% if rec["Wells"] %}
                    <a target="_blank" title="View Plate in IDR"
                        href="https://idr.openmicroscopy.org/webclient/?show=plate-{{ image_id }}">
                        <img class="icon" src="assets/img/plate16.png"/>
                    </a>
                {% else %}
                    <a target="_blank" title="View Image in IDR"
                        href="https://idr.openmicroscopy.org/webclient/img_detail/{{ image_id }}/">
                        <img class="icon" src="assets/img/view.svg"/>
                    </a>
                {% endif %}
            </td>
            <td>{{ rec["DOI"] }}</td>
            <td>{{ rec["Date added"] }}</td>
        </tr>
{% endfor %}
    </tbody>
</table>

<script>
$(document).ready( function () {
    $('#table').DataTable( {
          "scrollX": true,
          "pageLength": 100,
          "order": [[ 15, 'desc' ]]
    });
} );

function generatePythonCode(httpsUrl) {
    const s3Url = httpsUrl.replace('https://uk1s3.embassy.ebi.ac.uk/', 's3://');
    const endpoint = 'https://uk1s3.embassy.ebi.ac.uk';

    const code = `import zarr

zarr_group = zarr.open(
    "${s3Url}",
    storage_options={
        "anon": True,
        "client_kwargs": {
            "endpoint_url": "${endpoint}"
        }
    }
)`;

    const html = `<span style="color: #0000ff;">import</span> zarr

zarr_group = zarr.<span style="color: #795e26;">open</span>(
    <span style="color: #008000;">"${s3Url}"</span>,
    storage_options={
        <span style="color: #008000;">"anon"</span>: <span style="color: #0000ff;">True</span>,
        <span style="color: #008000;">"client_kwargs"</span>: {
            <span style="color: #008000;">"endpoint_url"</span>: <span style="color: #008000;">"${endpoint}"</span>
        }
    }
)`;

    return { code, html };
}

function showS3Options(httpsUrl, imageName) {
    const s3Url = httpsUrl.replace('https://uk1s3.embassy.ebi.ac.uk/', 's3://');
    const endpoint = 'https://uk1s3.embassy.ebi.ac.uk';
    const awsCommand = `aws s3 sync --endpoint-url ${endpoint} --no-sign-request ${s3Url} ${imageName}`;
    const omeZarrCommand = `uvx --with ome-zarr ome_zarr download ${httpsUrl} --output ${imageName}`;
    const python = generatePythonCode(httpsUrl);

    const modal = document.getElementById('s3OptionsModal');
    document.getElementById('s3OptionsTitle').textContent = `Download Options: ${imageName}`;
    document.getElementById('awsCliCommand').textContent = awsCommand;
    document.getElementById('omeZarrCommand').textContent = omeZarrCommand;
    document.getElementById('s3Endpoint').textContent = endpoint;
    document.getElementById('s3UrlText').textContent = s3Url;
    document.getElementById('pythonCodeInOptions').innerHTML = python.html;

    document.getElementById('awsCliCommand').dataset.copyText = awsCommand;
    document.getElementById('omeZarrCommand').dataset.copyText = omeZarrCommand;
    document.getElementById('s3Endpoint').dataset.copyText = endpoint;
    document.getElementById('s3UrlText').dataset.copyText = s3Url;
    document.getElementById('pythonCodeInOptions').dataset.copyText = python.code;

    modal.style.display = 'block';
}

function closeS3Options() {
    document.getElementById('s3OptionsModal').style.display = 'none';
}

function copyFromDataset(element) {
    copyTextToClipboard(element.dataset.copyText);
}

function copyTextToClipboard(text) {
    var textArea = document.createElement("textarea");
    // Place in the top-left corner of screen regardless of scroll position.
    textArea.style.position = 'fixed';

    textArea.value = text;

    document.body.appendChild(textArea);
    textArea.focus();
    textArea.select();

    var successful;
    try {
        successful = document.execCommand('copy');
    } catch (err) {
        console.log('Oops, unable to copy');
    }
    document.body.removeChild(textArea);

    if (successful) {
        // Show "Copied!" feedback to the left of button
        let target = event.target;
        let feedback = document.createElement('span');
        feedback.textContent = 'Copied! ';
        feedback.style.color = '#28a745';
        feedback.style.fontSize = '12px';
        feedback.style.marginRight = '5px';
        target.parentNode.insertBefore(feedback, target);

        setTimeout(() => {
            feedback.remove();
        }, 1500)
    } else {
        console.log("Copying failed")
    }
}
</script>

<!-- Modal for S3 options -->
<div id="s3OptionsModal" style="display: none; position: fixed; z-index: 1000; left: 0; top: 0; width: 100%; height: 100%; background-color: rgba(0,0,0,0.4); overflow-y: auto;">
    <div style="background-color: white; margin: 5% auto 5% auto; padding: 20px; border-radius: 5px; width: 80%; max-width: 700px;">
        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px;">
            <h3 style="margin: 0;" id="s3OptionsTitle">Download Options</h3>
            <button onclick="closeS3Options()" style="background: none; border: none; font-size: 24px; cursor: pointer;">&times;</button>
        </div>

        <div style="margin-bottom: 20px;">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 5px; gap: 10px;">
                <h4 style="margin: 0;"><a href="https://ome-zarr.readthedocs.io/en/stable/index.html" target="_blank" style="color: #0066cc; text-decoration: underline;">ome-zarr</a> CLI Command:</h4>
                <div style="display: flex; align-items: center;">
                    <button onclick="copyFromDataset(document.getElementById('omeZarrCommand'))" style="padding: 6px 12px; cursor: pointer; background: #0066cc; color: white; border: none; border-radius: 3px; font-size: 12px;">
                        Copy
                    </button>
                </div>
            </div>
            <code id="omeZarrCommand" style="display: block; padding: 10px; background: #f5f5f5; border-radius: 3px; overflow-x: auto; white-space: nowrap;"></code>
        </div>

        <div style="margin-bottom: 20px;">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 5px; gap: 10px;">
                <h4 style="margin: 0;">AWS CLI Command:</h4>
                <div style="display: flex; align-items: center;">
                    <button onclick="copyFromDataset(document.getElementById('awsCliCommand'))" style="padding: 6px 12px; cursor: pointer; background: #0066cc; color: white; border: none; border-radius: 3px; font-size: 12px;">
                        Copy
                    </button>
                </div>
            </div>
            <code id="awsCliCommand" style="display: block; padding: 10px; background: #f5f5f5; border-radius: 3px; overflow-x: auto; white-space: nowrap;"></code>
        </div>

        <div style="margin-bottom: 20px;">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 5px; gap: 10px;">
                <h4 style="margin: 0;">S3 Endpoint URL:</h4>
                <div style="display: flex; align-items: center;">
                    <button onclick="copyFromDataset(document.getElementById('s3Endpoint'))" style="padding: 6px 12px; cursor: pointer; background: #0066cc; color: white; border: none; border-radius: 3px; font-size: 12px;">
                        Copy
                    </button>
                </div>
            </div>
            <code id="s3Endpoint" style="display: block; padding: 10px; background: #f5f5f5; border-radius: 3px; overflow-x: auto;"></code>
        </div>

        <div style="margin-bottom: 20px;">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 5px; gap: 10px;">
                <h4 style="margin: 0;">S3 URL:</h4>
                <div style="display: flex; align-items: center;">
                    <button onclick="copyFromDataset(document.getElementById('s3UrlText'))" style="padding: 6px 12px; cursor: pointer; background: #0066cc; color: white; border: none; border-radius: 3px; font-size: 12px;">
                        Copy
                    </button>
                </div>
            </div>
            <code id="s3UrlText" style="display: block; padding: 10px; background: #f5f5f5; border-radius: 3px; overflow-x: auto;"></code>
        </div>

        <div style="margin-bottom: 0;">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 5px; gap: 10px;">
                <h4 style="margin: 0;"><a href="https://zarr.readthedocs.io/en/stable/" target="_blank" style="color: #0066cc; text-decoration: underline;">Zarr-Python</a>:</h4>
                <div style="display: flex; align-items: center;">
                    <button onclick="copyFromDataset(document.getElementById('pythonCodeInOptions'))" style="padding: 6px 12px; cursor: pointer; background: #0066cc; color: white; border: none; border-radius: 3px; font-size: 12px;">
                        Copy
                    </button>
                </div>
            </div>
            <pre id="pythonCodeInOptions" style="margin: 0; padding: 15px; background: #f5f5f5; border-radius: 3px; overflow-x: auto;"></pre>
        </div>
    </div>
</div>
