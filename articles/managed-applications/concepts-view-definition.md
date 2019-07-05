---
title: Panoramica della definizione della vista nelle applicazioni gestite di Azure | Microsoft Docs
description: Descrive il concetto di creazione definizione della vista per le applicazioni gestite di Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478745"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Elemento di definizione visualizzazione nelle applicazioni gestite di Azure

Definizione della vista è un elemento facoltativo nelle applicazioni gestite di Azure. Consente di personalizzare la pagina di panoramica e aggiungere altre visualizzazioni, ad esempio le risorse di metriche e personalizzato.

Questo articolo offre una panoramica di elemento di definizione visualizzazione e le relative funzionalità.

## <a name="view-definition-artifact"></a>Artefatto di definizione delle visualizzazioni

L'artefatto di definizione della vista deve chiamarsi **viewDefinition.json** e inserito in allo stesso livello **createuidefinition. JSON** e **maintemplate. JSON** nel file ZIP pacchetto che crea una definizione di applicazione gestita. Per informazioni su come creare il pacchetto con estensione zip e pubblicare una definizione di applicazione gestita, vedere [pubblica una definizione di applicazione gestita di Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Schema di definizione visualizzazione

Il **viewDefinition.json** file ha solo un livello superiore `views` proprietà, ovvero una matrice di viste. Ogni visualizzazione è visualizzato nell'interfaccia utente dell'applicazione gestita come una voce di menu separata nella tabella dei contenuti. Ogni visualizzazione dispone di un `kind` proprietà che imposta il tipo della visualizzazione. Si deve essere impostato su uno dei valori seguenti: [Overview](#overview), [Metrics](#metrics), [CustomResources](#custom-resources). Per altre informazioni, vedere corrente [schema JSON per viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Esempio JSON per la definizione della vista:

```json
{
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { },
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        }
    ]
}

```

## <a name="overview"></a>Panoramica

`"kind": "Overview"`

Quando si specifica questa visualizzazione nel **viewDefinition.json**, viene eseguito l'override la pagina di panoramica predefinita nell'applicazione gestita.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Proprietà|Obbligatorio|Descrizione|
|---------|---------|---------|
|intestazione|No|L'intestazione della pagina Panoramica.|
|description|No|La descrizione dell'applicazione gestita.|
|Comandi|No|Matrice di pulsanti della barra degli strumenti aggiuntivi della pagina Panoramica, vedere [comandi](#commands).|

## <a name="metrics"></a>metrics

`"kind": "Metrics"`

La visualizzazione metrica consente di raccogliere e aggregare i dati dalle risorse di applicazione gestita nel [metriche di monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Proprietà|Obbligatorio|Descrizione|
|---------|---------|---------|
|displayName|No|Il titolo visualizzato della visualizzazione.|
|version|No|La versione della piattaforma usata per il rendering della visualizzazione.|
|Grafici|Yes|Matrice di grafici della pagina metriche.|

### <a name="chart"></a>Grafico

|Proprietà|Obbligatorio|Descrizione|
|---------|---------|---------|
|displayName|Yes|Il titolo visualizzato del grafico.|
|chartType|No|La visualizzazione da utilizzare per il grafico. Per impostazione predefinita, Usa un grafico a linee. Tipi di grafico supportati: `Bar, Line, Area, Scatter`.|
|metrics|Yes|Matrice di metriche da tracciare sul grafico. Per altre informazioni sulle metriche supportate nel portale di Azure, vedere [metriche supportate con monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrica

|Proprietà|Obbligatorio|Descrizione|
|---------|---------|---------|
|name|Yes|Nome della metrica.|
|aggregationType|Yes|Il tipo di aggregazione da utilizzare per questa metrica. Tipi di aggregazione supportati: `none, sum, min, max, avg, unique, percentile, count`|
|namespace|No|Informazioni aggiuntive da usare per determinare il provider di metriche corrette.|
|resourceTagFilter|No|Matrice di tag alla risorsa (saranno separati con `or` word) per le metriche di cui verrebbe visualizzato. Applicati a un filtro del tipo di risorsa.|
|resourceType|Yes|Il tipo di risorsa per cui verrebbe visualizzate le metriche.|

## <a name="custom-resources"></a>Risorse personalizzate

`"kind": "CustomResources"`

È possibile definire più visualizzazioni di questo tipo. Ogni vista rappresenta un **univoci** personalizzato il tipo di risorsa dal provider personalizzato definito nel **maintemplate. JSON**. Per un'introduzione ai provider personalizzati, vedere [Azure Custom Providers Preview overview (Panoramica dell'anteprima di provider personalizzati di Azure)](custom-providers-overview.md).

In questa visualizzazione che è possibile eseguire GET, PUT, DELETE e registra le operazioni per il tipo di risorsa personalizzata. Operazioni successive potrebbero essere globale azioni personalizzate o azioni personalizzate in un contesto del tipo di risorsa personalizzata.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Proprietà|Obbligatorio|Descrizione|
|---------|---------|---------|
|displayName|Yes|Il titolo visualizzato della visualizzazione. Il titolo deve essere **univoci** per ogni visualizzazione CustomResources le **viewDefinition.json**.|
|version|No|La versione della piattaforma usata per il rendering della visualizzazione.|
|resourceType|Yes|Il tipo di risorsa personalizzata. Deve essere un **univoco** tipo di risorsa personalizzata del provider personalizzato.|
|createUIDefinition|No|Definizione dell'interfaccia utente di creare lo schema per creare il comando di risorsa personalizzata. Per un'introduzione alla creazione di definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md)|
|Comandi|No|Vedere la matrice dei pulsanti della barra degli strumenti aggiuntivi della visualizzazione, CustomResources [comandi](#commands).|
|columns|No|Matrice di colonne della risorsa personalizzata. Se non è definito il `name` colonna verrà visualizzata per impostazione predefinita. La colonna deve avere `"key"` e `"displayName"`. Per la chiave, specificare la chiave della proprietà da visualizzare in una vista. Se annidati, usare punto come delimitatore, ad esempio `"key": "name"` o `"key": "properties.property1"`. Per il nome visualizzato, specificare il nome visualizzato della proprietà da visualizzare in una vista. È anche possibile fornire un `"optional"` proprietà. Quando impostato su true, la colonna è nascosta per impostazione predefinita in una vista.|

## <a name="commands"></a>Comandi:

I comandi è una matrice di pulsanti della barra degli strumenti aggiuntivi che vengono visualizzati nella pagina. Ogni comando rappresenta un'azione POST dal Provider personalizzato di Azure definita **maintemplate. JSON**. Per un'introduzione ai provider personalizzati, vedere [Cenni preliminari sul provider personalizzati di Azure](custom-providers-overview.md).

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Proprietà|Obbligatorio|Descrizione|
|---------|---------|---------|
|displayName|Yes|Il nome visualizzato del pulsante di comando.|
|path|Yes|Il nome dell'azione provider personalizzato. L'azione deve essere definito in **maintemplate. JSON**.|
|icon|No|L'icona del pulsante di comando. Elenco di icone supportate è definito [dello Schema JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|No|Creare lo schema di definizione dell'interfaccia utente per comando. Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](overview.md).
- Per un'introduzione ai provider personalizzati, vedere [Cenni preliminari sul provider personalizzati di Azure](custom-providers-overview.md).
