# AzureAI_DocCustomClassifierSDK
This will serve as Custom SDK for Custom Doc Classifier on MSFT using Layout API 


Azure AI Document Intelligence client library for Python
Azure AI Document Intelligence (previously known as Form Recognizer) is a cloud service that uses machine learning to analyze text and structured data from your documents. It includes the following main features:

Layout - Extract content and structure (ex. words, selection marks, tables) from documents.
Document - Analyze key-value pairs in addition to general layout from documents.
Read - Read page information from documents.
Prebuilt - Extract common field values from select document types (ex. receipts, invoices, business cards, ID documents, U.S. W-2 tax documents, among others) using prebuilt models.
Custom - Build custom models from your own data to extract tailored field values in addition to general layout from documents.
Classifiers - Build custom classification models that combine layout and language features to accurately detect and identify documents you process within your application.
Add-on capabilities - Extract barcodes/QR codes, formulas, font/style, etc. or enable high resolution mode for large documents with optional parameters.
Source code | Package (PyPI) | API reference documentation | Product documentation | Samples

Disclaimer
The latest service API is currently only available in some Azure regions, the available regions can be found from here.

Getting started
Installating the package
python -m pip install azure-ai-documentintelligence
This table shows the relationship between SDK versions and supported API service versions:

SDK version	Supported API service version
1.0.0b1	2023-10-31-preview
1.0.0b2	2024-02-29-preview
Older API versions are supported in azure-ai-formrecognizer, please see the Migration Guide for detailed instructions on how to update application.

Prequisites
Python 3.8 or later is required to use this package.
You need an Azure subscription to use this package.
An existing Azure AI Document Intelligence instance.
Create a Cognitive Services or Document Intelligence resource
Document Intelligence supports both multi-service and single-service access. Create a Cognitive Services resource if you plan to access multiple cognitive services under a single endpoint/key. For Document Intelligence access only, create a Document Intelligence resource. Please note that you will need a single-service resource if you intend to use Azure Active Directory authentication.

You can create either resource using:

Option 1: Azure Portal.
Option 2: Azure CLI.
Below is an example of how you can create a Document Intelligence resource using the CLI:

# Create a new resource group to hold the Document Intelligence resource
# if using an existing resource group, skip this step
az group create --name <your-resource-name> --location <location>
# Create the Document Intelligence resource
az cognitiveservices account create \
    --name <your-resource-name> \
    --resource-group <your-resource-group-name> \
    --kind FormRecognizer \
    --sku <sku> \
    --location <location> \
    --yes
For more information about creating the resource or how to get the location and sku information see here.

Authenticate the client
In order to interact with the Document Intelligence service, you will need to create an instance of a client. An endpoint and credential are necessary to instantiate the client object.

Get the endpoint
You can find the endpoint for your Document Intelligence resource using the Azure Portal or Azure CLI:

# Get the endpoint for the Document Intelligence resource
az cognitiveservices account show --name "resource-name" --resource-group "resource-group-name" --query "properties.endpoint"
Either a regional endpoint or a custom subdomain can be used for authentication. They are formatted as follows:

Regional endpoint: https://<region>.api.cognitive.microsoft.com/
Custom subdomain: https://<resource-name>.cognitiveservices.azure.com/
A regional endpoint is the same for every resource in a region. A complete list of supported regional endpoints can be consulted here. Please note that regional endpoints do not support AAD authentication.

A custom subdomain, on the other hand, is a name that is unique to the Document Intelligence resource. They can only be used by single-service resources.

Get the API key
The API key can be found in the Azure Portal or by running the following Azure CLI command:

az cognitiveservices account keys list --name "<resource-name>" --resource-group "<resource-group-name>"
Create the client with AzureKeyCredential
To use an API key as the credential parameter, pass the key as a string into an instance of AzureKeyCredential.

from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")
document_intelligence_client = DocumentIntelligenceClient(endpoint, credential)
Create the client with an Azure Active Directory credential
AzureKeyCredential authentication is used in the examples in this getting started guide, but you can also authenticate with Azure Active Directory using the azure-identity library. Note that regional endpoints do not support AAD authentication. Create a custom subdomain name for your resource in order to use this type of authentication.

To use the DefaultAzureCredential type shown below, or other credential types provided with the Azure SDK, please install the azure-identity package:

pip install azure-identity

You will also need to register a new AAD application and grant access to Document Intelligence by assigning the "Cognitive Services User" role to your service principal.

Once completed, set the values of the client ID, tenant ID, and client secret of the AAD application as environment variables: AZURE_CLIENT_ID, AZURE_TENANT_ID, AZURE_CLIENT_SECRET.

"""DefaultAzureCredential will use the values from these environment
variables: AZURE_CLIENT_ID, AZURE_TENANT_ID, AZURE_CLIENT_SECRET
"""
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.identity import DefaultAzureCredential

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
credential = DefaultAzureCredential()

document_intelligence_client = DocumentIntelligenceClient(endpoint, credential)
Key concepts
DocumentIntelligenceClient
DocumentIntelligenceClient provides operations for analyzing input documents using prebuilt and custom models through the begin_analyze_document API. Use the model_id parameter to select the type of model for analysis. See a full list of supported models here. The DocumentIntelligenceClient also provides operations for classifying documents through the begin_classify_document API. Custom classification models can classify each page in an input file to identify the document(s) within and can also identify multiple documents or multiple instances of a single document within an input file.

Sample code snippets are provided to illustrate using a DocumentIntelligenceClient here. More information about analyzing documents, including supported features, locales, and document types can be found in the service documentation.

DocumentIntelligenceAdministrationClient
DocumentIntelligenceAdministrationClient provides operations for:

Building custom models to analyze specific fields you specify by labeling your custom documents. A DocumentModelDetails is returned indicating the document type(s) the model can analyze, as well as the estimated confidence for each field. See the service documentation for a more detailed explanation.
Creating a composed model from a collection of existing models.
Managing models created in your account.
Listing operations or getting a specific model operation created within the last 24 hours.
Copying a custom model from one Document Intelligence resource to another.
Build and manage a custom classification model to classify the documents you process within your application.
Please note that models can also be built using a graphical user interface such as Document Intelligence Studio.

Sample code snippets are provided to illustrate using a DocumentIntelligenceAdministrationClient here.

Long-running operations
Long-running operations are operations which consist of an initial request sent to the service to start an operation, followed by polling the service at intervals to determine whether the operation has completed or failed, and if it has succeeded, to get the result.

Methods that analyze documents, build models, or copy/compose models are modeled as long-running operations. The client exposes a begin_<method-name> method that returns an LROPoller or AsyncLROPoller. Callers should wait for the operation to complete by calling result() on the poller object returned from the begin_<method-name> method. Sample code snippets are provided to illustrate using long-running operations below.

Examples
The following section provides several code snippets covering some of the most common Document Intelligence tasks, including:

Extract Layout
Extract Figures from Documents
Analyze Documents Result in PDF
Using the General Document Model
Using Prebuilt Models
Build a Custom Model
Analyze Documents Using a Custom Model
Manage Your Models
Add-on Capabilities
Get Raw JSON Result
Extract Layout
Extract text, selection marks, text styles, and table structures, along with their bounding region coordinates, from documents.

from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeResult

def _in_span(word, spans):
    for span in spans:
        if word.span.offset >= span.offset and (word.span.offset + word.span.length) <= (span.offset + span.length):
            return True
    return False

def _format_polygon(polygon):
    if not polygon:
        return "N/A"
    return ", ".join([f"[{polygon[i]}, {polygon[i + 1]}]" for i in range(0, len(polygon), 2)])

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))
with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-layout", analyze_request=f, content_type="application/octet-stream"
    )
result: AnalyzeResult = poller.result()

if result.styles and any([style.is_handwritten for style in result.styles]):
    print("Document contains handwritten content")
else:
    print("Document does not contain handwritten content")

for page in result.pages:
    print(f"----Analyzing layout from page #{page.page_number}----")
    print(f"Page has width: {page.width} and height: {page.height}, measured with unit: {page.unit}")

    if page.lines:
        for line_idx, line in enumerate(page.lines):
            words = []
            if page.words:
                for word in page.words:
                    print(f"......Word '{word.content}' has a confidence of {word.confidence}")
                    if _in_span(word, line.spans):
                        words.append(word)
            print(
                f"...Line # {line_idx} has word count {len(words)} and text '{line.content}' "
                f"within bounding polygon '{_format_polygon(line.polygon)}'"
            )

    if page.selection_marks:
        for selection_mark in page.selection_marks:
            print(
                f"Selection mark is '{selection_mark.state}' within bounding polygon "
                f"'{_format_polygon(selection_mark.polygon)}' and has a confidence of {selection_mark.confidence}"
            )

if result.paragraphs:
    print(f"----Detected #{len(result.paragraphs)} paragraphs in the document----")
    # Sort all paragraphs by span's offset to read in the right order.
    result.paragraphs.sort(key=lambda p: (p.spans.sort(key=lambda s: s.offset), p.spans[0].offset))
    print("-----Print sorted paragraphs-----")
    for paragraph in result.paragraphs:
        if not paragraph.bounding_regions:
            print(f"Found paragraph with role: '{paragraph.role}' within N/A bounding region")
        else:
            print(f"Found paragraph with role: '{paragraph.role}' within")
            print(
                ", ".join(
                    f" Page #{region.page_number}: {_format_polygon(region.polygon)} bounding region"
                    for region in paragraph.bounding_regions
                )
            )
        print(f"...with content: '{paragraph.content}'")
        print(f"...with offset: {paragraph.spans[0].offset} and length: {paragraph.spans[0].length}")

if result.tables:
    for table_idx, table in enumerate(result.tables):
        print(f"Table # {table_idx} has {table.row_count} rows and " f"{table.column_count} columns")
        if table.bounding_regions:
            for region in table.bounding_regions:
                print(
                    f"Table # {table_idx} location on page: {region.page_number} is {_format_polygon(region.polygon)}"
                )
        for cell in table.cells:
            print(f"...Cell[{cell.row_index}][{cell.column_index}] has text '{cell.content}'")
            if cell.bounding_regions:
                for region in cell.bounding_regions:
                    print(
                        f"...content on page {region.page_number} is within bounding polygon '{_format_polygon(region.polygon)}'"
                    )

print("----------------------------------------")
Extract Figures from Documents
Extract figures from the document as cropped images.

from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeOutputOption, AnalyzeResult

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))

with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-layout",
        analyze_request=f,
        output=[AnalyzeOutputOption.FIGURES],
        content_type="application/octet-stream",
    )
result: AnalyzeResult = poller.result()
operation_id = poller.details["operation_id"]

if result.figures:
    for figure in result.figures:
        if figure.id:
            response = document_intelligence_client.get_analyze_result_figure(
                model_id=result.model_id, result_id=operation_id, figure_id=figure.id
            )
            with open(f"{figure.id}.png", "wb") as writer:
                writer.writelines(response)
else:
    print("No figures found.")
Analyze Documents Result in PDF
Convert an analog PDF into a PDF with embedded text. Such text can enable text search within the PDF or allow the PDF to be used in LLM chat scenarios.

Note: For now, this feature is only supported by prebuilt-read. All other models will return error.

from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeOutputOption, AnalyzeResult

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))

with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-read",
        analyze_request=f,
        output=[AnalyzeOutputOption.PDF],
        content_type="application/octet-stream",
    )
result: AnalyzeResult = poller.result()
operation_id = poller.details["operation_id"]

response = document_intelligence_client.get_analyze_result_pdf(model_id=result.model_id, result_id=operation_id)
with open("analyze_result.pdf", "wb") as writer:
    writer.writelines(response)
Using the General Document Model
Analyze key-value pairs, tables, styles, and selection marks from documents using the general document model provided by the Document Intelligence service. Select the General Document Model by passing model_id="prebuilt-document" into the begin_analyze_document method:

from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import DocumentAnalysisFeature, AnalyzeResult

def _in_span(word, spans):
    for span in spans:
        if word.span.offset >= span.offset and (word.span.offset + word.span.length) <= (span.offset + span.length):
            return True
    return False

def _format_bounding_region(bounding_regions):
    if not bounding_regions:
        return "N/A"
    return ", ".join(
        f"Page #{region.page_number}: {_format_polygon(region.polygon)}" for region in bounding_regions
    )

def _format_polygon(polygon):
    if not polygon:
        return "N/A"
    return ", ".join([f"[{polygon[i]}, {polygon[i + 1]}]" for i in range(0, len(polygon), 2)])

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))
with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-layout",
        analyze_request=f,
        features=[DocumentAnalysisFeature.KEY_VALUE_PAIRS],
        content_type="application/octet-stream",
    )
result: AnalyzeResult = poller.result()

if result.styles:
    for style in result.styles:
        if style.is_handwritten:
            print("Document contains handwritten content: ")
            print(",".join([result.content[span.offset : span.offset + span.length] for span in style.spans]))

print("----Key-value pairs found in document----")
if result.key_value_pairs:
    for kv_pair in result.key_value_pairs:
        if kv_pair.key:
            print(
                f"Key '{kv_pair.key.content}' found within "
                f"'{_format_bounding_region(kv_pair.key.bounding_regions)}' bounding regions"
            )
        if kv_pair.value:
            print(
                f"Value '{kv_pair.value.content}' found within "
                f"'{_format_bounding_region(kv_pair.value.bounding_regions)}' bounding regions\n"
            )

for page in result.pages:
    print(f"----Analyzing document from page #{page.page_number}----")
    print(f"Page has width: {page.width} and height: {page.height}, measured with unit: {page.unit}")

    if page.lines:
        for line_idx, line in enumerate(page.lines):
            words = []
            if page.words:
                for word in page.words:
                    print(f"......Word '{word.content}' has a confidence of {word.confidence}")
                    if _in_span(word, line.spans):
                        words.append(word)
            print(
                f"...Line #{line_idx} has {len(words)} words and text '{line.content}' within "
                f"bounding polygon '{_format_polygon(line.polygon)}'"
            )

    if page.selection_marks:
        for selection_mark in page.selection_marks:
            print(
                f"Selection mark is '{selection_mark.state}' within bounding polygon "
                f"'{_format_polygon(selection_mark.polygon)}' and has a confidence of "
                f"{selection_mark.confidence}"
            )

if result.tables:
    for table_idx, table in enumerate(result.tables):
        print(f"Table # {table_idx} has {table.row_count} rows and {table.column_count} columns")
        if table.bounding_regions:
            for region in table.bounding_regions:
                print(
                    f"Table # {table_idx} location on page: {region.page_number} is {_format_polygon(region.polygon)}"
                )
        for cell in table.cells:
            print(f"...Cell[{cell.row_index}][{cell.column_index}] has text '{cell.content}'")
            if cell.bounding_regions:
                for region in cell.bounding_regions:
                    print(
                        f"...content on page {region.page_number} is within bounding polygon '{_format_polygon(region.polygon)}'\n"
                    )
print("----------------------------------------")
Read more about the features provided by the prebuilt-document model here.
Using Prebuilt Models
Extract fields from select document types such as receipts, invoices, business cards, identity documents, and U.S. W-2 tax documents using prebuilt models provided by the Document Intelligence service.

For example, to analyze fields from a sales receipt, use the prebuilt receipt model provided by passing model_id="prebuilt-receipt" into the begin_analyze_document method:

from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeResult

def _format_price(price_dict):
    return "".join([f"{p}" for p in price_dict.values()])

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))
with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        "prebuilt-receipt", analyze_request=f, locale="en-US", content_type="application/octet-stream"
    )
receipts: AnalyzeResult = poller.result()

if receipts.documents:
    for idx, receipt in enumerate(receipts.documents):
        print(f"--------Analysis of receipt #{idx + 1}--------")
        print(f"Receipt type: {receipt.doc_type if receipt.doc_type else 'N/A'}")
        if receipt.fields:
            merchant_name = receipt.fields.get("MerchantName")
            if merchant_name:
                print(
                    f"Merchant Name: {merchant_name.get('valueString')} has confidence: "
                    f"{merchant_name.confidence}"
                )
            transaction_date = receipt.fields.get("TransactionDate")
            if transaction_date:
                print(
                    f"Transaction Date: {transaction_date.get('valueDate')} has confidence: "
                    f"{transaction_date.confidence}"
                )
            items = receipt.fields.get("Items")
            if items:
                print("Receipt items:")
                for idx, item in enumerate(items.get("valueArray")):
                    print(f"...Item #{idx + 1}")
                    item_description = item.get("valueObject").get("Description")
                    if item_description:
                        print(
                            f"......Item Description: {item_description.get('valueString')} has confidence: "
                            f"{item_description.confidence}"
                        )
                    item_quantity = item.get("valueObject").get("Quantity")
                    if item_quantity:
                        print(
                            f"......Item Quantity: {item_quantity.get('valueString')} has confidence: "
                            f"{item_quantity.confidence}"
                        )
                    item_total_price = item.get("valueObject").get("TotalPrice")
                    if item_total_price:
                        print(
                            f"......Total Item Price: {_format_price(item_total_price.get('valueCurrency'))} has confidence: "
                            f"{item_total_price.confidence}"
                        )
            subtotal = receipt.fields.get("Subtotal")
            if subtotal:
                print(
                    f"Subtotal: {_format_price(subtotal.get('valueCurrency'))} has confidence: {subtotal.confidence}"
                )
            tax = receipt.fields.get("TotalTax")
            if tax:
                print(f"Total tax: {_format_price(tax.get('valueCurrency'))} has confidence: {tax.confidence}")
            tip = receipt.fields.get("Tip")
            if tip:
                print(f"Tip: {_format_price(tip.get('valueCurrency'))} has confidence: {tip.confidence}")
            total = receipt.fields.get("Total")
            if total:
                print(f"Total: {_format_price(total.get('valueCurrency'))} has confidence: {total.confidence}")
        print("--------------------------------------")
You are not limited to receipts! There are a few prebuilt models to choose from, each of which has its own set of supported fields. See other supported prebuilt models here.

Build a Custom Model
Build a custom model on your own document type. The resulting model can be used to analyze values from the types of documents it was trained on. Provide a container SAS URL to your Azure Storage Blob container where you're storing the training documents.

More details on setting up a container and required file structure can be found in the service documentation.

# Let's build a model to use for this sample
import uuid
from azure.ai.documentintelligence import DocumentIntelligenceAdministrationClient
from azure.ai.documentintelligence.models import (
    DocumentBuildMode,
    BuildDocumentModelRequest,
    AzureBlobContentSource,
    DocumentModelDetails,
)
from azure.core.credentials import AzureKeyCredential

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]
container_sas_url = os.environ["DOCUMENTINTELLIGENCE_STORAGE_CONTAINER_SAS_URL"]

document_intelligence_admin_client = DocumentIntelligenceAdministrationClient(endpoint, AzureKeyCredential(key))
poller = document_intelligence_admin_client.begin_build_document_model(
    BuildDocumentModelRequest(
        model_id=str(uuid.uuid4()),
        build_mode=DocumentBuildMode.TEMPLATE,
        azure_blob_source=AzureBlobContentSource(container_url=container_sas_url),
        description="my model description",
    )
)
model: DocumentModelDetails = poller.result()

print(f"Model ID: {model.model_id}")
print(f"Description: {model.description}")
print(f"Model created on: {model.created_date_time}")
print(f"Model expires on: {model.expiration_date_time}")
if model.doc_types:
    print("Doc types the model can recognize:")
    for name, doc_type in model.doc_types.items():
        print(f"Doc Type: '{name}' built with '{doc_type.build_mode}' mode which has the following fields:")
        if doc_type.field_schema:
            for field_name, field in doc_type.field_schema.items():
                if doc_type.field_confidence:
                    print(
                        f"Field: '{field_name}' has type '{field['type']}' and confidence score "
                        f"{doc_type.field_confidence[field_name]}"
                    )
Analyze Documents Using a Custom Model
Analyze document fields, tables, selection marks, and more. These models are trained with your own data, so they're tailored to your documents. For best results, you should only analyze documents of the same document type that the custom model was built with.

from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeResult

def _print_table(header_names, table_data):
    # Print a two-dimensional array like a table.
    max_len_list = []
    for i in range(len(header_names)):
        col_values = list(map(lambda row: len(str(row[i])), table_data))
        col_values.append(len(str(header_names[i])))
        max_len_list.append(max(col_values))

    row_format_str = "".join(map(lambda len: f"{{:<{len + 4}}}", max_len_list))

    print(row_format_str.format(*header_names))
    for row in table_data:
        print(row_format_str.format(*row))

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]
model_id = os.getenv("CUSTOM_BUILT_MODEL_ID", custom_model_id)

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))

# Make sure your document's type is included in the list of document types the custom model can analyze
with open(path_to_sample_documents, "rb") as f:
    poller = document_intelligence_client.begin_analyze_document(
        model_id=model_id, analyze_request=f, content_type="application/octet-stream"
    )
result: AnalyzeResult = poller.result()

if result.documents:
    for idx, document in enumerate(result.documents):
        print(f"--------Analyzing document #{idx + 1}--------")
        print(f"Document has type {document.doc_type}")
        print(f"Document has document type confidence {document.confidence}")
        print(f"Document was analyzed with model with ID {result.model_id}")
        if document.fields:
            for name, field in document.fields.items():
                field_value = field.get("valueString") if field.get("valueString") else field.content
                print(
                    f"......found field of type '{field.type}' with value '{field_value}' and with confidence {field.confidence}"
                )

    # Extract table cell values
    SYMBOL_OF_TABLE_TYPE = "array"
    SYMBOL_OF_OBJECT_TYPE = "object"
    KEY_OF_VALUE_OBJECT = "valueObject"
    KEY_OF_CELL_CONTENT = "content"

    for doc in result.documents:
        if not doc.fields is None:
            for field_name, field_value in doc.fields.items():
                # Dynamic Table cell information store as array in document field.
                if field_value.type == SYMBOL_OF_TABLE_TYPE and field_value.value_array:
                    col_names = []
                    sample_obj = field_value.value_array[0]
                    if KEY_OF_VALUE_OBJECT in sample_obj:
                        col_names = list(sample_obj[KEY_OF_VALUE_OBJECT].keys())
                    print("----Extracting Dynamic Table Cell Values----")
                    table_rows = []
                    for obj in field_value.value_array:
                        if KEY_OF_VALUE_OBJECT in obj:
                            value_obj = obj[KEY_OF_VALUE_OBJECT]
                            extract_value_by_col_name = lambda key: (
                                value_obj[key].get(KEY_OF_CELL_CONTENT)
                                if key in value_obj and KEY_OF_CELL_CONTENT in value_obj[key]
                                else "None"
                            )
                            row_data = list(map(extract_value_by_col_name, col_names))
                            table_rows.append(row_data)
                    _print_table(col_names, table_rows)

                elif (
                    field_value.type == SYMBOL_OF_OBJECT_TYPE
                    and KEY_OF_VALUE_OBJECT in field_value
                    and field_value[KEY_OF_VALUE_OBJECT] is not None
                ):
                    rows_by_columns = list(field_value[KEY_OF_VALUE_OBJECT].values())
                    is_fixed_table = all(
                        (
                            rows_of_column["type"] == SYMBOL_OF_OBJECT_TYPE
                            and Counter(list(rows_by_columns[0][KEY_OF_VALUE_OBJECT].keys()))
                            == Counter(list(rows_of_column[KEY_OF_VALUE_OBJECT].keys()))
                        )
                        for rows_of_column in rows_by_columns
                    )

                    # Fixed Table cell information store as object in document field.
                    if is_fixed_table:
                        print("----Extracting Fixed Table Cell Values----")
                        col_names = list(field_value[KEY_OF_VALUE_OBJECT].keys())
                        row_dict: dict = {}
                        for rows_of_column in rows_by_columns:
                            rows = rows_of_column[KEY_OF_VALUE_OBJECT]
                            for row_key in list(rows.keys()):
                                if row_key in row_dict:
                                    row_dict[row_key].append(rows[row_key].get(KEY_OF_CELL_CONTENT))
                                else:
                                    row_dict[row_key] = [
                                        row_key,
                                        rows[row_key].get(KEY_OF_CELL_CONTENT),
                                    ]

                        col_names.insert(0, "")
                        _print_table(col_names, list(row_dict.values()))

print("------------------------------------")
Additionally, a document URL can also be used to analyze documents using the begin_analyze_document method.

from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceClient
from azure.ai.documentintelligence.models import AnalyzeDocumentRequest, AnalyzeResult

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

document_intelligence_client = DocumentIntelligenceClient(endpoint=endpoint, credential=AzureKeyCredential(key))
url = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/main/sdk/documentintelligence/azure-ai-documentintelligence/samples/sample_forms/receipt/contoso-receipt.png"
poller = document_intelligence_client.begin_analyze_document(
    "prebuilt-receipt", AnalyzeDocumentRequest(url_source=url)
)
receipts: AnalyzeResult = poller.result()
Manage Your Models
Manage the custom models attached to your account.

# Let's build a model to use for this sample
import uuid
from azure.ai.documentintelligence import DocumentIntelligenceAdministrationClient
from azure.ai.documentintelligence.models import (
    DocumentBuildMode,
    BuildDocumentModelRequest,
    AzureBlobContentSource,
    DocumentModelDetails,
)
from azure.core.credentials import AzureKeyCredential

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]
container_sas_url = os.environ["DOCUMENTINTELLIGENCE_STORAGE_CONTAINER_SAS_URL"]

document_intelligence_admin_client = DocumentIntelligenceAdministrationClient(endpoint, AzureKeyCredential(key))
poller = document_intelligence_admin_client.begin_build_document_model(
    BuildDocumentModelRequest(
        model_id=str(uuid.uuid4()),
        build_mode=DocumentBuildMode.TEMPLATE,
        azure_blob_source=AzureBlobContentSource(container_url=container_sas_url),
        description="my model description",
    )
)
model: DocumentModelDetails = poller.result()

print(f"Model ID: {model.model_id}")
print(f"Description: {model.description}")
print(f"Model created on: {model.created_date_time}")
print(f"Model expires on: {model.expiration_date_time}")
if model.doc_types:
    print("Doc types the model can recognize:")
    for name, doc_type in model.doc_types.items():
        print(f"Doc Type: '{name}' built with '{doc_type.build_mode}' mode which has the following fields:")
        if doc_type.field_schema:
            for field_name, field in doc_type.field_schema.items():
                if doc_type.field_confidence:
                    print(
                        f"Field: '{field_name}' has type '{field['type']}' and confidence score "
                        f"{doc_type.field_confidence[field_name]}"
                    )
account_details = document_intelligence_admin_client.get_resource_info()
print(
    f"Our resource has {account_details.custom_document_models.count} custom models, "
    f"and we can have at most {account_details.custom_document_models.limit} custom models"
)
# Next, we get a paged list of all of our custom models
models = document_intelligence_admin_client.list_models()

print("We have the following 'ready' models with IDs and descriptions:")
for model in models:
    print(f"{model.model_id} | {model.description}")
my_model = document_intelligence_admin_client.get_model(model_id=model.model_id)
print(f"\nModel ID: {my_model.model_id}")
print(f"Description: {my_model.description}")
print(f"Model created on: {my_model.created_date_time}")
print(f"Model expires on: {my_model.expiration_date_time}")
if my_model.warnings:
    print("Warnings encountered while building the model:")
    for warning in my_model.warnings:
        print(f"warning code: {warning.code}, message: {warning.message}, target of the error: {warning.target}")
# Finally, we will delete this model by ID
document_intelligence_admin_client.delete_model(model_id=my_model.model_id)

from azure.core.exceptions import ResourceNotFoundError

try:
    document_intelligence_admin_client.get_model(model_id=my_model.model_id)
except ResourceNotFoundError:
    print(f"Successfully deleted model with ID {my_model.model_id}")
Add-on Capabilities
Document Intelligence supports more sophisticated analysis capabilities. These optional features can be enabled and disabled depending on the scenario of the document extraction.

The following add-on capabilities are available in this SDK:

barcode/QR code
formula
font/style
high resolution mode
language
query fields
Note that some add-on capabilities will incur additional charges. See pricing: https://azure.microsoft.com/pricing/details/ai-document-intelligence/.

Get Raw JSON Result
Can get the HTTP response by passing parameter raw_response_hook to any client method.

import os
from azure.core.credentials import AzureKeyCredential
from azure.ai.documentintelligence import DocumentIntelligenceAdministrationClient

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

client = DocumentIntelligenceAdministrationClient(endpoint=endpoint, credential=AzureKeyCredential(key))

responses = {}

def callback(response):
    responses["status_code"] = response.http_response.status_code
    responses["response_body"] = response.http_response.json()

client.get_resource_info(raw_response_hook=callback)

print(f"Response status code is: {responses["status_code"]}")
response_body = responses["response_body"]
print(
    f"Our resource has {response_body['customDocumentModels']['count']} custom models, "
    f"and we can have at most {response_body['customDocumentModels']['limit']} custom models."
    f"The quota limit for custom neural document models is {response_body['customNeuralDocumentModelBuilds']['quota']} and the resource has"
    f"used {response_body['customNeuralDocumentModelBuilds']['used']}. The resource quota will reset on {response_body['customNeuralDocumentModelBuilds']['quotaResetDateTime']}"
)
Also, can use the send_request method to send custom HTTP requests and get raw JSON result from HTTP responses.

import os
from azure.core.credentials import AzureKeyCredential
from azure.core.rest import HttpRequest
from azure.ai.documentintelligence import DocumentIntelligenceAdministrationClient

endpoint = os.environ["DOCUMENTINTELLIGENCE_ENDPOINT"]
key = os.environ["DOCUMENTINTELLIGENCE_API_KEY"]

client = DocumentIntelligenceAdministrationClient(endpoint=endpoint, credential=AzureKeyCredential(key))

# The `send_request` method can send custom HTTP requests that share the client's existing pipeline,
# Now let's use the `send_request` method to make a resource details fetching request.
# The URL of the request should be absolute, and append the API version used for the request.
request = HttpRequest(method="GET", url=f"{endpoint}/documentintelligence/info?api-version=2024-07-31-preview")
response = client.send_request(request)
response.raise_for_status()
response_body = response.json()
print(
    f"Our resource has {response_body['customDocumentModels']['count']} custom models, "
    f"and we can have at most {response_body['customDocumentModels']['limit']} custom models."
    f"The quota limit for custom neural document models is {response_body['customNeuralDocumentModelBuilds']['quota']} and the resource has"
    f"used {response_body['customNeuralDocumentModelBuilds']['used']}. The resource quota will reset on {response_body['customNeuralDocumentModelBuilds']['quotaResetDateTime']}"
)
Troubleshooting
General
Document Intelligence client library will raise exceptions defined in Azure Core. Error codes and messages raised by the Document Intelligence service can be found in the service documentation.

Logging
This library uses the standard logging library for logging.

Basic information about HTTP sessions (URLs, headers, etc.) is logged at INFO level.

Detailed DEBUG level logging, including request/response bodies and unredacted headers, can be enabled on the client or per-operation with the logging_enable keyword argument.

See full SDK logging documentation with examples here.

Optional Configuration
Optional keyword arguments can be passed in at the client and per-operation level. The azure-core reference documentation describes available configurations for retries, logging, transport protocols, and more.

Next steps
More sample code
See the Sample README for several code snippets illustrating common patterns used in the Document Intelligence Python API.

Additional documentation
For more extensive documentation on Azure AI Document Intelligence, see the Document Intelligence documentation on docs.microsoft.com.

Contributing
This project welcomes contributions and suggestions. Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the Microsoft Open Source Code of Conduct. For more information, see the Code of Conduct FAQ or contact opencode@microsoft.com with any additional questions or comments.
