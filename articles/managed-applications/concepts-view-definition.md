---
title: Panoramica della definizione della vista nelle applicazioni gestite di Azure | Microsoft Docs
description: Viene descritto il concetto di creazione della definizione della vista per le applicazioni gestite di Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: ff96bddef1b34f5a8bf743ccaaccba2da01534dc
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335111"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Visualizzazione dell'elemento di definizione nelle applicazioni gestite di Azure

La definizione della vista è un elemento facoltativo nelle applicazioni gestite di Azure. Consente di personalizzare la pagina panoramica e aggiungere altre visualizzazioni, ad esempio metriche e risorse personalizzate.

Questo articolo fornisce una panoramica dell'elemento della definizione della vista e delle relative funzionalità.

## <a name="view-definition-artifact"></a>Artefatto di definizione delle visualizzazioni

L'elemento della definizione della vista deve essere denominato **viewDefinition. JSON** e inserito allo stesso livello di **createUiDefinition. JSON** e **mainTemplate. JSON** nel pacchetto zip che consente di creare una definizione di applicazione gestita. Per informazioni su come creare il pacchetto zip e pubblicare una definizione di applicazione gestita, vedere [pubblicare una definizione di applicazione gestita di Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Visualizza Schema definizione

Il file **viewDefinition. JSON** dispone solo di una proprietà `views` di primo livello, che è una matrice di visualizzazioni. Ogni visualizzazione viene visualizzata nell'interfaccia utente dell'applicazione gestita come voce di menu separata nel sommario. Ogni visualizzazione dispone di `kind` una proprietà che imposta il tipo della visualizzazione. Deve essere impostato su uno dei valori seguenti: [Panoramica](#overview), [metriche](#metrics), [CustomResources](#custom-resources). Per altre informazioni, vedere [schema JSON corrente per viewDefinition. JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Esempio di JSON per la definizione della vista:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
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
                        "createUIDefinition": { }
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

Quando si fornisce questa visualizzazione in **viewDefinition. JSON**, viene eseguito l'override della pagina di panoramica predefinita nell'applicazione gestita.

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
|intestazione|No|Intestazione della pagina Overview.|
|description|No|Descrizione dell'applicazione gestita.|
|comandi|No|La matrice di pulsanti aggiuntivi della barra degli strumenti della pagina Panoramica, vedere [comandi](#commands).|

![Panoramica](./media/view-definition/overview.png)

## <a name="metrics"></a>metrics

`"kind": "Metrics"`

La visualizzazione metriche consente di raccogliere e aggregare dati dalle risorse dell'applicazione gestita nelle [metriche di monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).

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

|Proprietà|Obbligatorio|DESCRIZIONE|
|---------|---------|---------|
|displayName|No|Titolo visualizzato della visualizzazione.|
|version|No|Versione della piattaforma utilizzata per il rendering della visualizzazione.|
|grafici|Yes|Matrice di grafici della pagina metrica.|

### <a name="chart"></a>Grafico

|Proprietà|Obbligatorio|Descrizione|
|---------|---------|---------|
|displayName|Sì|Titolo visualizzato del grafico.|
|chartType|No|Visualizzazione da utilizzare per il grafico. Per impostazione predefinita, viene utilizzato un grafico a linee. Tipi di grafico supportati `Bar, Line, Area, Scatter`:.|
|metrics|Sì|Matrice di metriche da tracciare in questo grafico. Per altre informazioni sulle metriche supportate in portale di Azure, vedere [metriche supportate con monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrica

|Proprietà|Obbligatorio|Descrizione|
|---------|---------|---------|
|name|Sì|Nome della metrica.|
|aggregationType|Sì|Tipo di aggregazione da utilizzare per questa metrica. Tipi di aggregazione supportati:`none, sum, min, max, avg, unique, percentile, count`|
|namespace|No|Informazioni aggiuntive da usare per determinare il provider di metriche corretto.|
|resourceTagFilter|No|La matrice dei tag delle risorse (sarà separata `or` da Word) per la quale verranno visualizzate le metriche. Si applica al filtro del tipo di risorsa.|
|resourceType|Sì|Tipo di risorsa per cui visualizzare le metriche.|

![metrics](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Risorse personalizzate

`"kind": "CustomResources"`

È possibile definire più visualizzazioni di questo tipo. Ogni visualizzazione rappresenta un tipo di risorsa personalizzato **univoco** del provider personalizzato definito in **mainTemplate. JSON**. Per un'introduzione ai provider personalizzati, vedere [Azure Custom Providers Preview overview (Panoramica dell'anteprima di provider personalizzati di Azure)](custom-providers-overview.md).

In questa visualizzazione è possibile eseguire operazioni GET, PUT, DELETE e POST per il tipo di risorsa personalizzato. Le operazioni POST potrebbero essere azioni personalizzate globali o azioni personalizzate in un contesto del tipo di risorsa personalizzato.

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
|displayName|Sì|Titolo visualizzato della visualizzazione. Il titolo deve essere **univoco** per ogni visualizzazione CustomResources in **viewDefinition. JSON**.|
|version|No|Versione della piattaforma utilizzata per il rendering della visualizzazione.|
|resourceType|Sì|Tipo di risorsa personalizzata. Deve essere un tipo di risorsa personalizzata **univoco** del provider personalizzato.|
|createUIDefinition|No|Creare lo schema di definizione dell'interfaccia utente per il comando Crea risorsa personalizzata. Per un'introduzione alla creazione di definizioni dell'interfaccia utente, vedere Introduzione a [CreateUiDefinition](create-uidefinition-overview.md)|
|comandi|No|La matrice di pulsanti aggiuntivi della barra degli strumenti della visualizzazione CustomResources, vedere [comandi](#commands).|
|columns|No|Matrice di colonne della risorsa personalizzata. Se non è definito `name` , la colonna verrà visualizzata per impostazione predefinita. La colonna deve avere `"key"` e `"displayName"`. Per chiave, fornire la chiave della proprietà da visualizzare in una visualizzazione. Se annidato, utilizzare il punto come delimitatore, ad `"key": "name"` esempio `"key": "properties.property1"`o. Per nome visualizzato specificare il nome visualizzato della proprietà da visualizzare in una vista. È anche possibile specificare una `"optional"` proprietà. Se è impostato su true, per impostazione predefinita la colonna è nascosta in una visualizzazione.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Comandi:

Commands è una matrice di pulsanti aggiuntivi della barra degli strumenti che vengono visualizzati nella pagina. Ogni comando rappresenta un'azione POST del provider personalizzato di Azure definito in **mainTemplate. JSON**. Per un'introduzione ai provider personalizzati, vedere [Panoramica dei provider personalizzati di Azure](custom-providers-overview.md).

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
|displayName|Yes|Nome visualizzato del pulsante di comando.|
|path|Sì|Nome dell'azione del provider personalizzato. L'azione deve essere definita in **mainTemplate. JSON**.|
|icon|No|Icona del pulsante di comando. L'elenco delle icone supportate è definito nello [schema JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|No|Creare lo schema di definizione dell'interfaccia utente per il comando. Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="looking-for-help"></a>Ricerca della Guida

Per domande sulle applicazioni gestite di Azure, provare a chiedere [stack overflow](http://stackoverflow.com/questions/tagged/azure-managedapps). Una domanda simile potrebbe essere già stata posta e rispondere, quindi controllare prima di pubblicare. Aggiungere il tag `azure-managedapps` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](overview.md).
- Per un'introduzione ai provider personalizzati, vedere [Panoramica dei provider personalizzati di Azure](custom-providers-overview.md).
- Per la creazione di un'applicazione gestita di Azure con i provider [personalizzati di Azure, vedere Esercitazione: Creare un'applicazione gestita con azioni del provider personalizzate e tipi di risorse](tutorial-create-managed-app-with-custom-provider.md)
