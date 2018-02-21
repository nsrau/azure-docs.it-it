---
title: Modelli di API in Gestione API di Azure | Microsoft Docs
description: Informazioni su come personalizzare il contenuto delle pagine API nel portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 3642fd09-ba98-4358-93a6-c48ab0500431
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 2317047165f49de8c42c41dc864f0752df4af068
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="api-templates-in-azure-api-management"></a>Modelli di API in Gestione API di Azure
In Gestione API di Azure è possibile personalizzare le pagine del portale per sviluppatori usando un set di modelli che ne configurano il contenuto. La sintassi [DotLiquid](http://dotliquidmarkup.org/) usata insieme a un editor di propria scelta, quale [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e a un set di [risorse stringa](api-management-template-resources.md#strings), [risorse Glifo](api-management-template-resources.md#glyphs) e [controlli di pagina](api-management-page-controls.md) offre una grande flessibilità nella configurazione personalizzata del contenuto delle pagine attraverso questi modelli.  
  
I modelli in questa sezione consentono di personalizzare il contenuto delle pagine API del portale per sviluppatori.  
  
-   [Elenco API](#APIList)  
-   [Operazione](#Product)  
-   [Esempi di codice](#CodeSamples)  
    -   [Curl](#Curl)  
    -   [C#](#CSharp)  
    -   [Java](#Stub)  
    -   [JavaScript](#JavaScript)  
    -   [Objective C](#ObjectiveC)  
    -   [PHP](#PHP)  
    -   [Python](#Python)  
    -   [Ruby](#Ruby)  

> [!NOTE]
>  La documentazione seguente include alcuni modelli predefiniti di esempio. A causa dei continui miglioramenti che vengono apportati, questi modelli sono però soggetti a modifiche. È possibile visualizzare i modelli predefiniti direttamente nel portale per sviluppatori accedendo ai singoli modelli desiderati. Per ulteriori informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
##  <a name="APIList"></a> Elenco API  
 Il modello **Elenco API** consente di personalizzare il corpo della pagina di elenco API nel portale per sviluppatori.  
  
 ![Elenco API del portale per sviluppatori](./media/api-management-api-templates/APIM-Developer-Portal-Templates-API-List.png "Elenco API del portale per sviluppatori in Gestione API")  
  
### <a name="default-template"></a>Modello predefinito  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ApisStrings|PageTitleApis" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if apis.size > 0 %}  
    <ol class="list-unstyled">  
    {% for api in apis %}  
        <li>  
        <h3>  
          <a href="/docs/services/{{api.id}}">{{api.name}}</a>  
        </h3>  
       {{api.description}}  
      </li>  
    {% endfor %}  
    </ol>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Controlli  
 Il modello `API list` può usare i [controlli di pagina](api-management-page-controls.md) seguenti.  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [search-control](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Modello di dati  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|API|Raccolta di entità [Riepilogo dell'API](api-management-template-data-model-reference.md#APISummary).|Le API visibili all'utente corrente.|  
  
### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "apis": [  
        {  
            "id": "570275f1b16653124c8f9ba3",  
            "name": "Basic Calculator",  
            "description": "Arithmetics is just a call away!"  
        },  
        {  
            "id": "57026e30de15d80041040001",  
            "name": "Echo API",  
            "description": null  
        }  
    ],  
    "pageTitle": "APIs"  
}  
```  
  
##  <a name="Product"></a> Operazione  
 Il modello **Operazione** consente di personalizzare il corpo della pagina dell'operazione nel portale per sviluppatori.  
  
 ![Pagina dell'operazione nel portale per sviluppatori](./media/api-management-api-templates/APIM-Developer-Portal-templates-Operation-page.png "Pagina dell'operazione nel portale per sviluppatori in Gestione API")  
  
### <a name="default-template"></a>Modello predefinito  
  
```xml  
<h2>{{api.name}}</h2>  
<p>{{api.description }}</p>  
  
<div class="panel">  
    <h3>{{operation.name}}</h3>  
    <p>{{operation.description }}</p>  
    <a class="btn btn-primary" href="{{consoleUrl}}" id="btnOpenConsole" role="button">  
        Try it  
    </a>  
</div>  
  
<h4>{% localized "Documentation|SectionHeadingRequestUrl" %}</h4>  
<div class="panel">  
    <div class="panel-body">  
        <label>{{ sampleUrl | escape }}</label>  
    </div>  
</div>  
  
{% if operation.request %}  
    {% if operation.request.parameters.size > 0 %}  
        <h4>{% localized "Documentation|SectionHeadingRequestParameters" %}</h4>  
  
        <div class="panel">  
            {% for parameter in operation.request.parameters %}  
                 <div class="row panel-body">  
                    <div class="col-md-3">  
                        <label>{{parameter.name}}</label>  
                        {% unless parameter.required %}  
                            <span class="text-muted">({% localized "Documentation|FormLabelSubtextOptional" %})</span>  
                        {% endunless %}  
                    </div>  
                    <div class="col-md-1">  
                        {{parameter.typeName}}  
                    </div>  
                    <div class="col-md-8">  
                        {{parameter.description}}  
                    </div>  
                 </div>  
            {% endfor %}  
        </div>  
    {% endif %}  
  
    {% if operation.request.headers.size > 0 %}  
        <h4>{% localized "Documentation|SectionHeadingRequestHeaders" %}</h4>  
        <div class="panel">  
            {% for header in operation.request.headers %}  
                 <div class="row panel-body">  
                    <div class="col-md-3">  
                        <label>{{header.name}}</label>  
                        {%unless header.required %}  
                            <span class="text-muted">({% localized "Documentation|FormLabelSubtextOptional" %})</span>  
                        {% endunless %}  
                    </div>  
                    <div class="col-md-1">  
                        {{header.typeName}}  
                    </div>  
                    <div class="col-md-8">  
                        {{header.description}}  
                    </div>  
                 </div>  
            {% endfor %}  
        </div>  
    {% endif %}  
  
    {% if operation.request.description or operation.request.representations.size > 0 %}  
        <h4>{% localized "Documentation|SectionHeadingRequestBody" %}</h4>  
        <div class="panel">  
            {% if operation.request.description %}  
                <p>{{operation.request.description }}</p>     
            {% endif %}  
  
            {% if operation.request.representations.size > 0 %}  
                <div role="tabpanel">  
                    <ul class="nav nav-tabs" role="tablist">  
                        {% for representation in operation.request.representations %}  
                            <li role="presentation" {% if forloop.first %}class="active"{% endif %}>  
                                <a href="#requesttab{{forloop.index}}" role="tab" data-toggle="tab">  
                                    {{representation.contentType}}  
                                </a>  
                            </li>  
                        {% endfor %}  
                    </ul>  
                    <div class="tab-content tab-content-boxed">  
                        {% for representation in operation.request.representations %}  
                            <div id="requesttab{{forloop.index}}" role="tabpanel" class="tab-pane snippet{% if forloop.first %} active{% endif %}">  
  
                                {% if representation.sample or representation.schema %}   
                                <div role="tabpanel">  
                                    {% if representation.sample and representation.schema %}   
                                    <ul class="nav nav-tabs-borderless" role="tablist">  
                                        <li role="presentation" class="active">  
                                            <a href="#requesttab{{forloop.index}}sample" role="tab" data-toggle="tab">Sample</a>  
                                        </li>  
                                        <li role="presentation">  
                                            <a href="#requesttab{{forloop.index}}schema" role="tab" data-toggle="tab">Schema</a>  
                                        </li>  
                                    </ul>  
                                    {% endif %}  
  
                                    <div class="tab-content">  
                                        {% if representation.sample %}  
                                        <div id="requesttab{{forloop.index}}sample" role="tabpanel" class="tab-pane snippet active">  
                                            <pre><code class="{{representation.Brush}}">{{ representation.sample | escape }}</code></pre>  
                                        </div>  
                                        {% endif %}  
  
                                        {% if representation.schema %}  
                                        <div id="requesttab{{forloop.index}}schema" role="tabpanel" class="tab-pane snippet">  
                                            <pre><code class="{{representation.Brush}}">{{ representation.schema | escape }}</code></pre>  
                                        </div>  
                                        {% endif %}  
                                    </div>  
                                </div>  
                                {% endif %}  
                            </div>  
                        {% endfor %}  
                    </div>  
                </div>  
            {% endif %}  
  
            <div class="clearfix"></div>  
        </div>  
    {% endif %}  
{% endif %}  
  
{% if operation.responses.size > 0 %}  
    {% for response in operation.responses %}  
        {% if response.description or response.representations.size > 0 %}  
            <h4>{% localized "Documentation|SectionHeadingResponse" %} {{response.statusCode}}</h4>  
  
            <div class="panel">  
                {% if response.description %}  
                    <p>{{ response.description }}</p>  
                {% endif %}  
  
                {% if response.representations.size > 0 %}  
                    <div role="tabpanel">  
                        <ul class="nav nav-tabs" role="tablist">  
                            {% for representation in response.representations %}  
                                <li role="presentation" {% if forloop.first %}class="active"{% endif %}>  
                                    <a href="#response{{response.statusCode}}tab{{forloop.index}}" role="tab" data-toggle="tab">  
                                        {{representation.contentType}}  
                                    </a>  
                                </li>  
                            {% endfor %}  
                        </ul>  
                        <div class="tab-content tab-content-boxed">  
                            {% for representation in response.representations %}  
                                <div id="response{{response.statusCode}}tab{{forloop.index}}" role="tabpanel" class="tab-pane snippet{% if forloop.first %} active{% endif %}">  
  
                                    {% if representation.sample or representation.schema %}  
                                    <div role="tabpanel">  
  
                                        {% if representation.sample and representation.schema %}  
                                        <ul class="nav nav-tabs-borderless" role="tablist">  
                                            <li role="presentation" class="active">  
                                                <a href="#response{{response.statusCode}}tab{{forloop.index}}sample" role="tab" data-toggle="tab">  
                                                    Sample  
                                                </a>  
                                            </li>  
                                            <li role="presentation">  
                                                <a href="#response{{response.statusCode}}tab{{forloop.index}}schema" role="tab" data-toggle="tab">  
                                                    Schema  
                                                </a>  
                                            </li>  
                                        </ul>  
                                        {% endif %}  
  
                                        <div class="tab-content">  
                                            {% if representation.sample %}  
                                            <div id="response{{response.statusCode}}tab{{forloop.index}}sample" role="tabpanel" class="tab-pane snippet active">  
                                                <pre><code class="{{representation.Brush}}">{{ representation.sample | escape }}</code></pre>  
                                            </div>  
                                            {% endif %}  
  
                                            {% if representation.schema %}  
                                            <div id="response{{response.statusCode}}tab{{forloop.index}}schema" role="tabpanel" class="tab-pane snippet">  
                                                <pre><code class="{{representation.Brush}}">{{ representation.schema | escape }}</code></pre>  
                                            </div>  
                                            {% endif %}  
                                        </div>  
                                    </div>  
                                    {% endif %}  
                                </div>  
                            {% endfor %}  
                        </div>  
                    </div>  
                {% endif %}  
  
                <div class="clearfix"></div>  
            </div>  
  
        {% endif %}  
    {% endfor %}  
{% endif %}  
  
<h4>{% localized "Documentation|SectionHeadingCodeSamples" %}</h4>  
<div role="tabpanel">  
    <ul class="nav nav-tabs" role="tablist">  
        {% for sample in samples %}  
            <li role="presentation" {% if forloop.first %}class="active"{% endif %}>  
                <a href="#{{sample.brush}}" aria-controls="{{sample.brush}}" role="tab" data-toggle="tab">  
                    {{sample.title}}  
                </a>  
            </li>  
        {% endfor %}  
    </ul>  
    <div class="tab-content tab-content-boxed" title="{% localized "Documentation|TooltipTextDoubleClickToSelectAll=""" %}">  
        {% for sample in samples %}  
            <div role="tabpanel" class="tab-pane tab-content-boxed {% if forloop.first %} active{% endif %} snippet snippet-resizable" id="{{sample.brush}}" >  
                <pre><code class="{{sample.brush}}">{% partial sample.template for sample in samples %}</code></pre>  
            </div>  
        {% endfor %}  
    </div>  
    <div class="clearfix"></div>  
</div>  
```  
  
### <a name="controls"></a>Controlli  
 Il modello `Operation` non consente l'uso di [controlli di pagina](api-management-page-controls.md).  
  
### <a name="data-model"></a>Modello di dati  
  
|Proprietà|type|DESCRIZIONE|  
|--------------|----------|-----------------|  
|apiId|stringa|ID dell'API corrente.|  
|apiName|stringa|Nome dell'API.|  
|apiDescription|stringa|Descrizione dell'API.|  
|api|Entità [Riepilogo dell'API](api-management-template-data-model-reference.md#APISummary).|API corrente.|  
|operation|[Operazione](api-management-template-data-model-reference.md#Operation)|L'operazione attualmente visualizzata.|  
|sampleUrl|stringa|L'URL per l'operazione corrente.|  
|operationMenu|[Menu dell'operazione](api-management-template-data-model-reference.md#Menu)|Un menu di operazioni per questa API.|  
|consoleUrl|URI|L'URI per il pulsante **Prova**.|  
|esempi|Raccolta di entità [Esempio di codice](api-management-template-data-model-reference.md#Sample).|Gli esempi di codice per l'operazione corrente.|  
  
### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "apiId": "570275f1b16653124c8f9ba3",  
    "apiName": "Basic Calculator",  
    "apiDescription": "Arithmetics is just a call away!",  
    "api": {  
        "id": "570275f1b16653124c8f9ba3",  
        "name": "Basic Calculator",  
        "description": "Arithmetics is just a call away!"  
    },  
    "operation": {  
        "id": "570275f2b1665305c811cf49",  
        "name": "Add two integers",  
        "description": "Produces a sum of two numbers.",  
        "scheme": "https",  
        "uriTemplate": "calc/add?a={a}&b={b}",  
        "host": "sdcontoso5.azure-api.net",  
        "httpMethod": "GET",  
        "request": {  
            "description": null,  
            "headers": [  
                {  
                    "name": "Ocp-Apim-Subscription-Key",  
                    "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
                    "value": "{subscription key}",  
                    "typeName": "string",  
                    "options": null,  
                    "required": true,  
                    "readonly": false  
                }  
            ],  
            "parameters": [  
                {  
                    "name": "a",  
                    "description": "First operand. Default value is <code>51</code>.",  
                    "value": "51",  
                    "options": [  
                        "51"  
                    ],  
                    "required": true,  
                    "kind": 1,  
                    "typeName": null  
                },  
                {  
                    "name": "b",  
                    "description": "Second operand. Default value is <code>49</code>.",  
                    "value": "49",  
                    "options": [  
                        "49"  
                    ],  
                    "required": true,  
                    "kind": 1,  
                    "typeName": null  
                }  
            ],  
            "representations": []  
        },  
        "responses": []  
    },  
    "sampleUrl": "https://sdcontoso5.azure-api.net/calc/add?a={a}&b={b}",  
    "operationMenu": {  
        "ApiId": "570275f1b16653124c8f9ba3",  
        "CurrentOperationId": "570275f2b1665305c811cf49",  
        "Action": "Operation",  
        "MenuItems": [  
            {  
                "Id": "570275f2b1665305c811cf49",  
                "Title": "Add two integers",  
                "HttpMethod": "GET"  
            },  
            {  
                "Id": "570275f2b1665305c811cf4c",  
                "Title": "Divide two integers",  
                "HttpMethod": "GET"  
            },  
            {  
                "Id": "570275f2b1665305c811cf4b",  
                "Title": "Multiply two integers",  
                "HttpMethod": "GET"  
            },  
            {  
                "Id": "570275f2b1665305c811cf4a",  
                "Title": "Subtract two integers",  
                "HttpMethod": "GET"  
            }  
        ]  
    },  
    "consoleUrl": "/docs/services/570275f1b16653124c8f9ba3/operations/570275f2b1665305c811cf49/console",  
    "samples": [  
        {  
            "title": "Curl",  
            "snippet": null,  
            "brush": "plain",  
            "template": "DocumentationSamplesCurl",  
            "body": "{body}",  
            "method": "GET",  
            "scheme": "https",  
            "path": "/calc/add?a={a}&b={b}",  
            "query": "",  
            "host": "sdcontoso5.azure-api.net",  
            "headers": [  
                {  
                    "name": "Ocp-Apim-Subscription-Key",  
                    "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
                    "value": "{subscription key}",  
                    "typeName": "string",  
                    "options": null,  
                    "required": true,  
                    "readonly": false  
                }  
            ],  
            "parameters": []  
        },  
        {  
            "title": "C#",  
            "snippet": null,  
            "brush": "csharp",  
            "template": "DocumentationSamplesCsharp",  
            "body": "{body}",  
            "method": "GET",  
            "scheme": "https",  
            "path": "/calc/add?a={a}&b={b}",  
            "query": "",  
            "host": "sdcontoso5.azure-api.net",  
            "headers": [  
                {  
                    "name": "Ocp-Apim-Subscription-Key",  
                    "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
                    "value": "{subscription key}",  
                    "typeName": "string",  
                    "options": null,  
                    "required": true,  
                    "readonly": false  
                }  
            ],  
            "parameters": []  
        },  
        {  
            "title": "Java",  
            "snippet": null,  
            "brush": "java",  
            "template": "DocumentationSamplesJava",  
            "body": "{body}",  
            "method": "GET",  
            "scheme": "https",  
            "path": "/calc/add?a={a}&b={b}",  
            "query": "",  
            "host": "sdcontoso5.azure-api.net",  
            "headers": [  
                {  
                    "name": "Ocp-Apim-Subscription-Key",  
                    "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
                    "value": "{subscription key}",  
                    "typeName": "string",  
                    "options": null,  
                    "required": true,  
                    "readonly": false  
                }  
            ],  
            "parameters": []  
        },  
        {  
            "title": "JavaScript",  
            "snippet": null,  
            "brush": "xml",  
            "template": "DocumentationSamplesJs",  
            "body": "{body}",  
            "method": "GET",  
            "scheme": "https",  
            "path": "/calc/add?a={a}&b={b}",  
            "query": "",  
            "host": "sdcontoso5.azure-api.net",  
            "headers": [  
                {  
                    "name": "Ocp-Apim-Subscription-Key",  
                    "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
                    "value": "{subscription key}",  
                    "typeName": "string",  
                    "options": null,  
                    "required": true,  
                    "readonly": false  
                }  
            ],  
            "parameters": []  
        },  
        {  
            "title": "ObjC",  
            "snippet": null,  
            "brush": "objc",  
            "template": "DocumentationSamplesObjc",  
            "body": "{body}",  
            "method": "GET",  
            "scheme": "https",  
            "path": "/calc/add?a={a}&b={b}",  
            "query": "",  
            "host": "sdcontoso5.azure-api.net",  
            "headers": [  
                {  
                    "name": "Ocp-Apim-Subscription-Key",  
                    "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
                    "value": "{subscription key}",  
                    "typeName": "string",  
                    "options": null,  
                    "required": true,  
                    "readonly": false  
                }  
            ],  
            "parameters": []  
        },  
        {  
            "title": "PHP",  
            "snippet": null,  
            "brush": "php",  
            "template": "DocumentationSamplesPhp",  
            "body": "{body}",  
            "method": "GET",  
            "scheme": "https",  
            "path": "/calc/add?a={a}&b={b}",  
            "query": "",  
            "host": "sdcontoso5.azure-api.net",  
            "headers": [  
                {  
                    "name": "Ocp-Apim-Subscription-Key",  
                    "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
                    "value": "{subscription key}",  
                    "typeName": "string",  
                    "options": null,  
                    "required": true,  
                    "readonly": false  
                }  
            ],  
            "parameters": []  
        },  
        {  
            "title": "Python",  
            "snippet": null,  
            "brush": "python",  
            "template": "DocumentationSamplesPython",  
            "body": "{body}",  
            "method": "GET",  
            "scheme": "https",  
            "path": "/calc/add?a={a}&b={b}",  
            "query": "",  
            "host": "sdcontoso5.azure-api.net",  
            "headers": [  
                {  
                    "name": "Ocp-Apim-Subscription-Key",  
                    "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
                    "value": "{subscription key}",  
                    "typeName": "string",  
                    "options": null,  
                    "required": true,  
                    "readonly": false  
                }  
            ],  
            "parameters": []  
        },  
        {  
            "title": "Ruby",  
            "snippet": null,  
            "brush": "ruby",  
            "template": "DocumentationSamplesRuby",  
            "body": "{body}",  
            "method": "GET",  
            "scheme": "https",  
            "path": "/calc/add?a={a}&b={b}",  
            "query": "",  
            "host": "sdcontoso5.azure-api.net",  
            "headers": [  
                {  
                    "name": "Ocp-Apim-Subscription-Key",  
                    "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
                    "value": "{subscription key}",  
                    "typeName": "string",  
                    "options": null,  
                    "required": true,  
                    "readonly": false  
                }  
            ],  
            "parameters": []  
        }  
    ]  
}  
```  
  
##  <a name="CodeSamples"></a> Esempi di codice  
 I modelli seguenti consentono di personalizzare il corpo degli esempi di codice singoli nella pagina operazione.  
  
 ![Modelli per esempi di codice nel portale per sviluppatori](./media/api-management-api-templates/APIM-Developer-Portal-Templates-Code-samples.png "Modelli per esempi di codice nel portale per sviluppatori in Gestione API")  
  
-   [Curl](#Curl)  
  
-   [C#](#CSharp)  
  
-   [Java](#Stub)  
  
-   [JavaScript](#JavaScript)  
  
-   [Objective C](#ObjectiveC)  
  
-   [PHP](#PHP)  
  
-   [Python](#Python)  
  
-   [Ruby](#Ruby)  
  
###  <a name="Curl"></a> Curl  
 Il modello **DocumentationSamplesCurl** consente di personalizzare tale esempio di codice nella relativa sezione della pagina operazione.  
  
#### <a name="default-template"></a>Modello predefinito  
  
```xml  
@ECHO OFF  
  
curl -v -X {{method}} "{{scheme}}://{{host}}{{path}}{{query | escape }}"  
{% for header in headers -%}  
-H "{{ header.name }}: {{ header.value }}"  
{% endfor -%}  
{% if body -%}   
--data-ascii "{{ body | replace:'"','^"' }}"   
{% endif -%}  
  
```  
  
#### <a name="controls"></a>Controlli  
 I modelli degli esempi di codici non consentono l'uso di [controlli di pagina](api-management-page-controls.md).  
  
#### <a name="data-model"></a>Modello di dati  
 Entità [Esempio di codice](api-management-template-data-model-reference.md#Sample).  
  
#### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "title": "Curl",  
    "snippet": null,  
    "brush": "plain",  
    "template": "DocumentationSamplesCurl",  
    "body": "{body}",  
    "method": "GET",  
    "scheme": "https",  
    "path": "/calc/add?a={a}&b={b}",  
    "query": "",  
    "host": "sdcontoso5.azure-api.net",  
    "headers": [  
        {  
            "name": "Ocp-Apim-Subscription-Key",  
            "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
            "value": "{subscription key}",  
            "typeName": "string",  
            "options": null,  
            "required": true,  
            "readonly": false  
        }  
    ],  
    "parameters": []  
}  
```  
  
###  <a name="CSharp"></a> C#  
 Il modello **DocumentationSamplesCsharp** consente di personalizzare tale esempio di codice nella relativa sezione della pagina operazione.  
  
#### <a name="default-template"></a>Modello predefinito  
  
```xml  
using System;  
using System.Net.Http.Headers;  
using System.Text;  
using System.Net.Http;  
using System.Web;  
  
namespace CSHttpClientSample  
{  
    static class Program  
    {  
        static void Main()  
        {  
            MakeRequest();  
            Console.WriteLine("Hit ENTER to exit...");  
            Console.ReadLine();  
        }  
  
        static async void MakeRequest()  
        {  
            var client = new HttpClient();  
            var queryString = HttpUtility.ParseQueryString(string.Empty);  
  
{% if headers.size > 0 -%}  
            // Request headers  
{% for header in headers -%}  
{% case header.Name -%}  
{% when "Accept"%}  
            client.DefaultRequestHeaders.Accept.Add(MediaTypeWithQualityHeaderValue.Parse("{{header.value}}"));  
{% when "Accept-Charset" -%}  
            client.DefaultRequestHeaders.AcceptCharset.Add(StringWithQualityHeaderValue.Parse("{{header.value}}"));  
{% when "Accept-Encoding" -%}  
            client.DefaultRequestHeaders.AcceptEncoding.Add(StringWithQualityHeaderValue.Parse("{{header.value}}"));  
{% when "Accept-Language" -%}  
            client.DefaultRequestHeaders.AcceptLanguage.Add(StringWithQualityHeaderValue.Parse("{{header.value}}"));  
{% when "Cache-Control" -%}  
            client.DefaultRequestHeaders.CacheControl = CacheControlHeaderValue.Parse("{{header.value}}");  
{% when "Connection" -%}  
            client.DefaultRequestHeaders.Connection.Add("{{header.value}}");  
{% when "Date" -%}  
            client.DefaultRequestHeaders.Date = DateTimeOffset.Parse("{{header.value}}");  
{% when "Expect" -%}  
            client.DefaultRequestHeaders.Expect.Add(NameValueWithParametersHeaderValue.Parse("{{header.value}}"));  
{% when "If-Match" -%}  
            client.DefaultRequestHeaders.IfMatch.Add(EntityTagHeaderValue.Parse("{{header.value}}"));  
{% when "If-Modified-Since" -%}  
            client.DefaultRequestHeaders.IfModifiedSince = DateTimeOffset.Parse("{{header.value}}");  
{% when "If-None-Match" -%}  
            client.DefaultRequestHeaders.IfNoneMatch.Add(EntityTagHeaderValue.Parse("{{header.value}}"));  
{% when "If-Range" -%}  
            client.DefaultRequestHeaders.IfRange = RangeConditionHeaderValue.Parse("{{header.value}}");  
{% when "If-Unmodified-Since" -%}  
            client.DefaultRequestHeaders.IfUnmodifiedSince = DateTimeOffset.Parse("{{header.value}}");  
{% when "Max-Forwards" -%}  
            client.DefaultRequestHeaders.MaxForwards = int.Parse("{{header.value}}");  
{% when "Pragma" -%}  
            client.DefaultRequestHeaders.Pragma.Add(NameValueHeaderValue.Parse("{{header.value}}"));  
{% when "Range" -%}  
            client.DefaultRequestHeaders.Range = RangeHeaderValue.Parse("{{header.value}}");  
{% when "Referer" -%}  
            client.DefaultRequestHeaders.Referrer = new Uri("{{header.value}}");  
{% when "TE" -%}  
            client.DefaultRequestHeaders.TE.Add(TransferCodingWithQualityHeaderValue.Parse("{{header.value}}"));  
{% when "Transfer-Encoding" -%}  
            client.DefaultRequestHeaders.TransferEncoding.Add(TransferCodingHeaderValue.Parse("{{header.value}}"));  
{% when "Upgrade" -%}  
            client.DefaultRequestHeaders.Upgrade.Add(ProductHeaderValue.Parse("{{header.value}}"));  
{% when "User-Agent" -%}  
            client.DefaultRequestHeaders.UserAgent.Add(ProductInfoHeaderValue.Parse("{{header.value}}"));  
{% when "Via" -%}                
            client.DefaultRequestHeaders.Via.Add(ViaHeaderValue.Parse("{{header.value}}"));  
{% when "Warning" -%}  
            client.DefaultRequestHeaders.Warning.Add(WarningHeaderValue.Parse("{{header.value}}"));  
{% when "Content-Type" -%}  
{% else -%}  
            client.DefaultRequestHeaders.Add("{{header.Name}}", "{{header.value}}");  
{% endcase -%}  
{% endfor -%}  
{% endif -%}  
  
{% if parameters.size > 0 -%}  
            // Request parameters  
{% for parameter in parameters -%}  
            queryString["{{parameter.Name}}"] = "{{parameter.Value}}";  
{% endfor -%}  
{% endif -%}  
            var uri = "{{scheme}}://{{host}}{{path}}{% if path contains '?' %}&{% else %}?{% endif %}" + queryString;  
  
{% case method -%}  
  
{% when "POST" -%}  
            HttpResponseMessage response;  
  
            // Request body  
            byte[] byteData = Encoding.UTF8.GetBytes("{{ body | replace:'"','\"'}}");  
  
            using (var content = new ByteArrayContent(byteData))  
            {  
{% if body -%}  
               content.Headers.ContentType = new MediaTypeHeaderValue("< your content type, i.e. application/json >");  
{% endif -%}  
               response = await client.PostAsync(uri, content);  
            }  
  
{% when "GET" -%}  
            var response = await client.GetAsync(uri);  
{% when "DELETE" -%}  
            var response = await client.DeleteAsync(uri);  
{% when "PUT" -%}  
            HttpResponseMessage response;  
  
            // Request body  
            byte[] byteData = Encoding.UTF8.GetBytes("{{ body | replace:'"','\"'}}");  
  
            using (var content = new ByteArrayContent(byteData))  
            {  
{% if body -%}  
                content.Headers.ContentType = new MediaTypeHeaderValue("< your content type, i.e. application/json >");  
{% endif -%}  
                response = await client.PutAsync(uri, content);  
            }  
{% when "HEAD" -%}  
            var response = await client.SendAsync(new HttpRequestMessage(HttpMethod.Head, uri));  
{% when "OPTIONS" -%}  
            var response = await client.SendAsync(new HttpRequestMessage(HttpMethod.Options, uri));  
{% when "TRACE" -%}  
            var response = await client.SendAsync(new HttpRequestMessage(HttpMethod.Trace, uri));  
  
            if (response.Content != null)  
            {  
                var responseString = await response.Content.ReadAsStringAsync();  
                Console.WriteLine(responseString);      
            }  
{% endcase -%}  
        }  
    }  
}     
```  
  
#### <a name="controls"></a>Controlli  
 I modelli degli esempi di codici non consentono l'uso di [controlli di pagina](api-management-page-controls.md).  
  
#### <a name="data-model"></a>Modello di dati  
 Entità [Esempio di codice](api-management-template-data-model-reference.md#Sample).  
  
#### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "title": "C#",  
    "snippet": null,  
    "brush": "csharp",  
    "template": "DocumentationSamplesCsharp",  
    "body": "{body}",  
    "method": "GET",  
    "scheme": "https",  
    "path": "/calc/add?a={a}&b={b}",  
    "query": "",  
    "host": "sdcontoso5.azure-api.net",  
    "headers": [  
        {  
            "name": "Ocp-Apim-Subscription-Key",  
            "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
            "value": "{subscription key}",  
            "typeName": "string",  
            "options": null,  
            "required": true,  
            "readonly": false  
        }  
    ],  
    "parameters": []  
}  
```  
  
###  <a name="Stub"></a> Java  
 Il modello **DocumentationSamplesJava** consente di personalizzare tale esempio di codice nella relativa sezione della pagina operazione.  
  
#### <a name="default-template"></a>Modello predefinito  
  
```xml  
// // This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)  
import java.net.URI;  
import org.apache.http.HttpEntity;  
import org.apache.http.HttpResponse;  
import org.apache.http.client.HttpClient;  
import org.apache.http.client.methods.HttpGet;  
import org.apache.http.client.utils.URIBuilder;  
import org.apache.http.impl.client.HttpClients;  
import org.apache.http.util.EntityUtils;  
  
public class JavaSample   
{  
    public static void main(String[] args)   
    {  
        HttpClient httpclient = HttpClients.createDefault();  
  
        try  
        {  
            URIBuilder builder = new URIBuilder("{{scheme}}://{{host}}{{path}}");  
  
{% if parameters.size > 0 -%}  
{% for parameter in parameters -%}  
            builder.setParameter("{{parameter.name}}", "{{parameter.value}}");  
{% endfor -%}  
{% endif -%}  
  
            URI uri = builder.build();  
            Http{{ method | downcase | capitalize }} request = new Http{{ method | downcase | capitalize }}(uri);  
{% for header in headers -%}  
            request.setHeader("{{header.Name}}", "{{header.value}}");  
{% endfor %}  
  
{% if body -%}  
            // Request body  
            StringEntity reqEntity = new StringEntity("{{ body | replace:'"','\"' }}");  
            request.setEntity(reqEntity);  
{% endif -%}  
  
            HttpResponse response = httpclient.execute(request);  
            HttpEntity entity = response.getEntity();  
  
            if (entity != null)   
            {  
                System.out.println(EntityUtils.toString(entity));  
            }  
        }  
        catch (Exception e)  
        {  
            System.out.println(e.getMessage());  
        }  
    }  
}  
  
```  
  
#### <a name="controls"></a>Controlli  
 I modelli degli esempi di codici non consentono l'uso di [controlli di pagina](api-management-page-controls.md).  
  
#### <a name="data-model"></a>Modello di dati  
 Entità [Esempio di codice](api-management-template-data-model-reference.md#Sample).  
  
#### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "title": "Java",  
    "snippet": null,  
    "brush": "java",  
    "template": "DocumentationSamplesJava",  
    "body": "{body}",  
    "method": "GET",  
    "scheme": "https",  
    "path": "/calc/add?a={a}&b={b}",  
    "query": "",  
    "host": "sdcontoso5.azure-api.net",  
    "headers": [  
        {  
            "name": "Ocp-Apim-Subscription-Key",  
            "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
            "value": "{subscription key}",  
            "typeName": "string",  
            "options": null,  
            "required": true,  
            "readonly": false  
        }  
    ],  
    "parameters": []  
}  
```  
  
###  <a name="JavaScript"></a> JavaScript  
 Il modello **DocumentationSamplesJs** consente di personalizzare tale esempio di codice nella relativa sezione della pagina operazione.  
  
#### <a name="default-template"></a>Modello predefinito  
  
```xml  
<!DOCTYPE html>  
<html>  
<head>  
    <title>JSSample</title>  
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>  
</head>  
<body>  
  
<script type="text/javascript">  
    $(function() {  
        var params = {  
{% if parameters.size > 0 -%}  
            // Request parameters  
{% for parameter in parameters -%}  
            "{{parameter.name}}": "{{parameter.value}}",  
{% endfor -%}  
{% endif -%}  
        };  
  
        $.ajax({  
            url: "{{scheme}}://{{host}}{{path}}{% if path contains '?' %}&{% else %}?{% endif %}" + $.param(params),  
{% if headers.size > 0 -%}  
            beforeSend: function(xhrObj){  
                // Request headers  
{% for header in headers -%}  
                xhrObj.setRequestHeader("{{header.name}}","{{header.value}}");  
{% endfor -%}  
            },  
{% endif -%}  
            type: "{{method}}",  
{% if body -%}  
            // Request body  
            data: "{{ body | replace:'"','\"' }}",  
{% endif -%}  
        })  
        .done(function(data) {  
            alert("success");  
        })  
        .fail(function() {  
            alert("error");  
        });  
    });  
</script>  
</body>  
</html>  
  
```  
  
#### <a name="controls"></a>Controlli  
 I modelli degli esempi di codici non consentono l'uso di [controlli di pagina](api-management-page-controls.md).  
  
#### <a name="data-model"></a>Modello di dati  
 Entità [Esempio di codice](api-management-template-data-model-reference.md#Sample).  
  
#### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "title": "JavaScript",  
    "snippet": null,  
    "brush": "xml",  
    "template": "DocumentationSamplesJs",  
    "body": "{body}",  
    "method": "GET",  
    "scheme": "https",  
    "path": "/calc/add?a={a}&b={b}",  
    "query": "",  
    "host": "sdcontoso5.azure-api.net",  
    "headers": [  
        {  
            "name": "Ocp-Apim-Subscription-Key",  
            "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
            "value": "{subscription key}",  
            "typeName": "string",  
            "options": null,  
            "required": true,  
            "readonly": false  
        }  
    ],  
    "parameters": []  
}  
```  
  
###  <a name="ObjectiveC"></a> Objective C  
 Il modello **DocumentationSamplesObjc** consente di personalizzare tale esempio di codice nella relativa sezione della pagina operazione.  
  
#### <a name="default-template"></a>Modello predefinito  
  
```xml  
#import <Foundation/Foundation.h>  
  
int main(int argc, const char * argv[])  
{  
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];  
  
    NSString* path = @"{{scheme}}://{{host}}{{path}}";  
    NSArray* array = @[  
                         // Request parameters  
                         @"entities=true",  
{% if parameters.size > 0 -%}  
{% for parameter in parameters -%}  
                         @"{{parameter.name}}={{parameter.value}}",  
{% endfor -%}  
{% endif -%}  
                      ];  
  
    NSString* string = [array componentsJoinedByString:@"&"];  
    path = [path stringByAppendingFormat:@"?%@", string];  
  
    NSLog(@"%@", path);  
  
    NSMutableURLRequest* _request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:path]];  
    [_request setHTTPMethod:@"{{method}}"];  
{% if headers.size > 0 -%}  
    // Request headers  
{% for header in headers -%}  
    [_request setValue:@"{{header.value}}" forHTTPHeaderField:@"{{header.name}}"];  
{% endfor -%}  
{% endif -%}  
{% if body -%}  
    // Request body  
    [_request setHTTPBody:[@"{{ body | replace:'"','\"' }}" dataUsingEncoding:NSUTF8StringEncoding]];  
{% endif -%}  
  
    NSURLResponse *response = nil;  
    NSError *error = nil;  
    NSData* _connectionData = [NSURLConnection sendSynchronousRequest:_request returningResponse:&response error:&error];  
  
    if (nil != error)  
    {  
        NSLog(@"Error: %@", error);  
    }  
    else  
    {  
        NSError* error = nil;  
        NSMutableDictionary* json = nil;  
        NSString* dataString = [[NSString alloc] initWithData:_connectionData encoding:NSUTF8StringEncoding];  
        NSLog(@"%@", dataString);  
  
        if (nil != _connectionData)  
        {  
            json = [NSJSONSerialization JSONObjectWithData:_connectionData options:NSJSONReadingMutableContainers error:&error];  
        }  
  
        if (error || !json)  
        {  
            NSLog(@"Could not parse loaded json with error:%@", error);  
        }  
  
        NSLog(@"%@", json);  
        _connectionData = nil;  
    }  
  
    [pool drain];  
  
    return 0;  
}  
  
```  
  
#### <a name="controls"></a>Controlli  
 I modelli degli esempi di codici non consentono l'uso di [controlli di pagina](api-management-page-controls.md).  
  
#### <a name="data-model"></a>Modello di dati  
 Entità [Esempio di codice](api-management-template-data-model-reference.md#Sample).  
  
#### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "title": "ObjC",  
    "snippet": null,  
    "brush": "objc",  
    "template": "DocumentationSamplesObjc",  
    "body": "{body}",  
    "method": "GET",  
    "scheme": "https",  
    "path": "/calc/add?a={a}&b={b}",  
    "query": "",  
    "host": "sdcontoso5.azure-api.net",  
    "headers": [  
        {  
            "name": "Ocp-Apim-Subscription-Key",  
            "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
            "value": "{subscription key}",  
            "typeName": "string",  
            "options": null,  
            "required": true,  
            "readonly": false  
        }  
    ],  
    "parameters": []  
}  
```  
  
###  <a name="PHP"></a> PHP  
 Il modello **DocumentationSamplesPhp** consente di personalizzare tale esempio di codice nella relativa sezione della pagina operazione.  
  
#### <a name="default-template"></a>Modello predefinito  
  
```xml  
<?php  
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)  
require_once 'HTTP/Request2.php';  
  
$request = new Http_Request2('{{scheme}}://{{host}}{{path}}');  
$url = $request->getUrl();  
  
{% if headers.size > 0 -%}  
$headers = array(  
    // Request headers  
{% for header in headers -%}  
    '{{header.name}}' => '{{header.value}}',  
{% endfor -%}  
);  
  
$request->setHeader($headers);  
{% endif -%}  
  
{% if parameters.size > 0 -%}  
$parameters = array(  
    // Request parameters  
{% for parameter in parameters -%}  
    '{{parameter.name}}' => '{{parameter.value}}',  
{% endfor -%}  
);  
  
$url->setQueryVariables($parameters);  
{% endif -%}  
  
$request->setMethod(HTTP_Request2::METHOD_{{method}});  
  
{% if body -%}  
// Request body  
$request->setBody("{{ body | replace:'"','\"' }}");  
{% endif -%}  
  
try  
{  
    $response = $request->send();  
    echo $response->getBody();  
}  
catch (HttpException $ex)  
{  
    echo $ex;  
}  
  
?>  
```  
  
#### <a name="controls"></a>Controlli  
 I modelli degli esempi di codici non consentono l'uso di [controlli di pagina](api-management-page-controls.md).  
  
#### <a name="data-model"></a>Modello di dati  
 Entità [Esempio di codice](api-management-template-data-model-reference.md#Sample).  
  
#### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "title": "PHP",  
    "snippet": null,  
    "brush": "php",  
    "template": "DocumentationSamplesPhp",  
    "body": "{body}",  
    "method": "GET",  
    "scheme": "https",  
    "path": "/calc/add?a={a}&b={b}",  
    "query": "",  
    "host": "sdcontoso5.azure-api.net",  
    "headers": [  
        {  
            "name": "Ocp-Apim-Subscription-Key",  
            "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
            "value": "{subscription key}",  
            "typeName": "string",  
            "options": null,  
            "required": true,  
            "readonly": false  
        }  
    ],  
    "parameters": []  
}  
```  
  
###  <a name="Python"></a> Python  
 Il modello **DocumentationSamplesPython** consente di personalizzare tale esempio di codice nella relativa sezione della pagina operazione.  
  
#### <a name="default-template"></a>Modello predefinito  
  
```xml  
########### Python 2.7 #############  
import httplib, urllib, base64  
  
headers = {  
{% if headers.size > 0 -%}  
    # Request headers  
{% for header in headers -%}  
    '{{header.name}}': '{{header.value}}',  
{% endfor -%}  
{% endif -%}  
}  
  
params = urllib.urlencode({  
{% if parameters.size > 0 -%}  
    # Request parameters  
{% for parameter in parameters -%}  
    '{{parameter.name}}': '{{parameter.value}}',  
{% endfor -%}  
{% endif -%}  
})  
  
try:  
{% case scheme -%}  
{% when "http" -%}  
    conn = httplib.HTTPConnection('{{host}}')  
{% when "https" -%}  
    conn = httplib.HTTPSConnection('{{host}}')  
{% endcase -%}  
    conn.request("{{method}}", "{{path}}{% if path contains '?' %}&{% else %}?{% endif %}%s" % params{% if body %}, "{{ body | replace:'"','\"' }}"{% endif %}, headers)  
    response = conn.getresponse()  
    data = response.read()  
    print(data)  
    conn.close()  
except Exception as e:  
    print("[Errno {0}] {1}".format(e.errno, e.strerror))  
  
####################################  
  
########### Python 3.2 #############  
import http.client, urllib.request, urllib.parse, urllib.error, base64  
  
headers = {  
{% if headers.size > 0 -%}  
    # Request headers  
{% for header in headers -%}  
    '{{header.name}}': '{{header.value}}',  
{% endfor -%}  
{% endif -%}  
}  
  
params = urllib.parse.urlencode({  
{% if parameters.size > 0 -%}  
    # Request parameters  
{% for parameter in parameters -%}  
    '{{parameter.name}}': '{{parameter.value}}',  
{% endfor -%}  
{% endif -%}  
})  
  
try:  
{% case scheme -%}  
{% when "http" -%}  
    conn = http.client.HTTPConnection('{{host}}')  
{% when "https" -%}  
    conn = http.client.HTTPSConnection('{{host}}')  
{% endcase -%}  
    conn.request("{{method}}", "{{path}}{% if path contains '?' %}&{% else %}?{% endif %}%s" % params{% if body %}, "{{ body | replace:'"','\"' }}"{% endif %}, headers)  
    response = conn.getresponse()  
    data = response.read()  
    print(data)  
    conn.close()  
except Exception as e:  
    print("[Errno {0}] {1}".format(e.errno, e.strerror))  
  
####################################  
```  
  
#### <a name="controls"></a>Controlli  
 I modelli degli esempi di codici non consentono l'uso di [controlli di pagina](api-management-page-controls.md).  
  
#### <a name="data-model"></a>Modello di dati  
 Entità [Esempio di codice](api-management-template-data-model-reference.md#Sample).  
  
#### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "title": "Python",  
    "snippet": null,  
    "brush": "python",  
    "template": "DocumentationSamplesPython",  
    "body": "{body}",  
    "method": "GET",  
    "scheme": "https",  
    "path": "/calc/add?a={a}&b={b}",  
    "query": "",  
    "host": "sdcontoso5.azure-api.net",  
    "headers": [  
        {  
            "name": "Ocp-Apim-Subscription-Key",  
            "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
            "value": "{subscription key}",  
            "typeName": "string",  
            "options": null,  
            "required": true,  
            "readonly": false  
        }  
    ],  
    "parameters": []  
}  
```  
  
###  <a name="Ruby"></a> Ruby  
 Il modello **DocumentationSamplesRuby** consente di personalizzare tale esempio di codice nella relativa sezione della pagina operazione.  
  
#### <a name="default-template"></a>Modello predefinito  
  
```xml  
require 'net/http'  
  
uri = URI('{{scheme}}://{{host}}{{path}}')  
uri.query = URI.encode_www_form({  
{% if parameters.size > 0 -%}  
    # Request parameters  
{% for parameter in parameters -%}  
    '{{parameter.name}}' => '{{parameter.value}}'{% unless forloop.last %},{% endunless %}  
{% endfor -%}  
{% endif -%}  
})  
  
request = Net::HTTP::{{ method | downcase | capitalize }}.new(uri.request_uri)  
{% for header in headers -%}  
# Request headers  
request['{{header.name}}'] = '{{header.value}}'  
{% endfor -%}  
{% if body -%}  
# Request body  
request.body = "{{ body | replace:'"','\"' }}"  
{% endif -%}  
  
response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|  
    http.request(request)  
end  
  
puts response.body  
  
```  
  
#### <a name="controls"></a>Controlli  
 I modelli degli esempi di codici non consentono l'uso di [controlli di pagina](api-management-page-controls.md).  
  
#### <a name="data-model"></a>Modello di dati  
 Entità [Esempio di codice](api-management-template-data-model-reference.md#Sample).  
  
#### <a name="sample-template-data"></a>Dati del modello di esempio  
  
```json  
{  
    "title": "Ruby",  
    "snippet": null,  
    "brush": "ruby",  
    "template": "DocumentationSamplesRuby",  
    "body": "{body}",  
    "method": "GET",  
    "scheme": "https",  
    "path": "/calc/add?a={a}&b={b}",  
    "query": "",  
    "host": "sdcontoso5.azure-api.net",  
    "headers": [  
        {  
            "name": "Ocp-Apim-Subscription-Key",  
            "description": "Subscription key which provides access to this API. Found in your <a href='/developer'>Profile</a>.",  
            "value": "{subscription key}",  
            "typeName": "string",  
            "options": null,  
            "required": true,  
            "readonly": false  
        }  
    ],  
    "parameters": []  
}  
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API di Azure con i modelli](api-management-developer-portal-templates.md).