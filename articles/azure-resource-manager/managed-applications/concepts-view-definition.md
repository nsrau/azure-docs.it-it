---
title: Panoramica della definizione della vista
description: Descrive il concetto di creazione della definizione della visualizzazione per le applicazioni gestite di Azure.Describes the concept of creating view definition for Azure Managed Applications.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 7a3d2234a140d1fb2eede50e3fe2eef5575da648
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391701"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Visualizzare l'elemento di definizione nelle applicazioni gestite di AzureView definition artifact in Azure Managed Applications

La definizione della visualizzazione è un elemento facoltativo nelle applicazioni gestite di Azure.View definition is an optional artifact in Azure Managed Applications. Consente di personalizzare la pagina di panoramica e aggiungere altre visualizzazioni, ad esempio Metriche e Risorse personalizzate.

In questo articolo viene fornita una panoramica dell'elemento di definizione della visualizzazione e delle relative funzionalità.

## <a name="view-definition-artifact"></a>Artefatto di definizione delle visualizzazioni

L'elemento di definizione della visualizzazione deve essere denominato **viewDefinition.json** e inserito allo stesso livello di **createUiDefinition.json** e **mainTemplate.json** nel pacchetto .zip che crea una definizione di applicazione gestita. Per informazioni su come creare il pacchetto .zip e pubblicare una definizione di applicazione gestita, vedere Pubblicare una definizione di [applicazione gestita di AzureTo](publish-service-catalog-app.md) learn how to create the .zip package and publish a managed application definition, see Publish an Azure Managed Application definition

## <a name="view-definition-schema"></a>Schema di definizione della vista

Il file **viewDefinition.json** ha `views` una sola proprietà di primo livello, ovvero una matrice di visualizzazioni. Ogni visualizzazione viene visualizzata nell'interfaccia utente dell'applicazione gestita come voce di menu separata nel sommario. Ogni visualizzazione `kind` dispone di una proprietà che imposta il tipo della visualizzazione. Deve essere impostato su uno dei valori seguenti: [Overview](#overview), [Metrics](#metrics), [CustomResources](#custom-resources), [Associations](#associations). Per ulteriori informazioni, vedere schema JSON corrente [per viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

JSON di esempio per la definizione della vista:Sample JSON for view definition:

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
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Panoramica

`"kind": "Overview"`

Quando si fornisce questa visualizzazione in **viewDefinition.json**, viene ignorata la pagina Panoramica predefinita nell'applicazione gestita.

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

|Proprietà|Obbligatoria|Descrizione|
|---------|---------|---------|
|intestazione|No|Intestazione della pagina di panoramica.|
|description|No|Descrizione dell'applicazione gestita.|
|comandi|No|La matrice di pulsanti aggiuntivi della pagina di panoramica, vedere [comandi](#commands).|

![Panoramica](./media/view-definition/overview.png)

## <a name="metrics"></a>Metriche

`"kind": "Metrics"`

La visualizzazione delle metriche consente di raccogliere e aggregare dati dalle risorse dell'applicazione gestita in Metriche di Monitoraggio di [Azure.](../../azure-monitor/platform/data-platform-metrics.md)

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

|Proprietà|Obbligatoria|Descrizione|
|---------|---------|---------|
|displayName|No|Titolo visualizzato della vista.|
|version|No|Versione della piattaforma utilizzata per eseguire il rendering della visualizzazione.|
|grafici|Sì|Matrice di grafici della pagina delle metriche.|

### <a name="chart"></a>Grafico

|Proprietà|Obbligatoria|Descrizione|
|---------|---------|---------|
|displayName|Sì|Titolo visualizzato del grafico.|
|tipo di grafico|No|Visualizzazione da utilizzare per questo grafico. Per impostazione predefinita, utilizza un grafico a linee. Tipi di `Bar, Line, Area, Scatter`grafico supportati: .|
|Metriche|Sì|Matrice di metriche da tracciare su questo grafico. Per altre informazioni sulle metriche supportate nel portale di Azure, vedere [Metriche supportate con Monitoraggio di AzureTo](../../azure-monitor/platform/metrics-supported.md) learn more about metrics supported in Azure portal, see Supported metrics with Azure Monitor|

### <a name="metric"></a>Metrica

|Proprietà|Obbligatoria|Descrizione|
|---------|---------|---------|
|name|Sì|Nome della metrica.|
|aggregationType|Sì|Tipo di aggregazione da utilizzare per questa metrica. Tipi di aggregazione supportati:Supported aggregation types:`none, sum, min, max, avg, unique, percentile, count`|
|namespace|No|Informazioni aggiuntive da utilizzare per determinare il provider di metriche corretto.|
|resourceTagFilter (filtro resourceTag)|No|Matrice di tag di risorsa `or` (sarà separata da word) per la quale verrebbero visualizzate le metriche. Si applica sopra il filtro del tipo di risorsa.|
|resourceType|Sì|Tipo di risorsa per cui verrebbero visualizzate le metriche.|

![Metriche](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Risorse personalizzate

`"kind": "CustomResources"`

È possibile definire più viste di questo tipo. Ogni visualizzazione rappresenta un tipo di risorsa personalizzata **univoca** dal provider personalizzato definito in **mainTemplate.json**. Per un'introduzione ai provider personalizzati, vedere [Azure Custom Providers Preview overview (Panoramica dell'anteprima di provider personalizzati di Azure)](../custom-providers/overview.md).

In questa visualizzazione è possibile eseguire operazioni GET, PUT, DELETE e POST per il tipo di risorsa personalizzata. Le operazioni POST possono essere azioni personalizzate globali o azioni personalizzate in un contesto del tipo di risorsa personalizzata.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
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

|Proprietà|Obbligatoria|Descrizione|
|---------|---------|---------|
|displayName|Sì|Titolo visualizzato della vista. Il titolo deve essere **univoco** per ogni visualizzazione CustomResources in **viewDefinition.json**.|
|version|No|Versione della piattaforma utilizzata per eseguire il rendering della visualizzazione.|
|resourceType|Sì|Tipo di risorsa personalizzata. Deve essere un tipo di risorsa personalizzata **univoca** del provider personalizzato.|
|icon|No|Icona della vista. L'elenco delle icone di esempio è definito in [Schema JSON.](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)|
|createUIDefinizione|No|Creare lo schema di definizione dell'interfaccia utente per creare un comando di risorsa personalizzato. Per un'introduzione alla creazione di definizioni dell'interfaccia utente, vedere Introduzione a [CreateUiDefinitionFor](create-uidefinition-overview.md) an introduction to creating UI definitions, see Getting started with CreateUiDefinition|
|comandi|No|Matrice di pulsanti aggiuntivi della barra degli strumenti Risorse, vedere [Comandi](#commands).|
|colonne|No|Matrice di colonne della risorsa personalizzata. Se non `name` è stata definita, la colonna verrà visualizzata per impostazione predefinita. La colonna `"key"` deve `"displayName"`avere e . Per key, fornire la chiave della proprietà da visualizzare in una visualizzazione. Se nidificato, utilizzare dot come delimitatore, ad esempio, `"key": "name"` o `"key": "properties.property1"`. Per il nome visualizzato, specificare il nome visualizzato della proprietà da visualizzare in una visualizzazione. È inoltre possibile `"optional"` fornire una proprietà. Se impostato su true, la colonna è nascosta in una vista per impostazione predefinita.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Comandi:

I comandi sono una serie di pulsanti aggiuntivi della barra degli strumenti visualizzati nella pagina. Ogni comando rappresenta un'azione POST dal provider personalizzato di Azure definito in **mainTemplate.json**. Per un'introduzione ai provider personalizzati, vedere [Panoramica dei provider personalizzati](../custom-providers/overview.md)di Azure.For an introduction to custom providers, see Azure Custom Providers overview .

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

|Proprietà|Obbligatoria|Descrizione|
|---------|---------|---------|
|displayName|Sì|Nome visualizzato del pulsante di comando.|
|path|Sì|Nome dell'azione del provider personalizzato. L'azione deve essere definita in **mainTemplate.json**.|
|icon|No|Icona del pulsante di comando. L'elenco delle icone di esempio è definito in [Schema JSON.](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)|
|createUIDefinizione|No|Creare lo schema di definizione dell'interfaccia utente per il comando. Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Associazioni

`"kind": "Associations"`

È possibile definire più viste di questo tipo. Questa visualizzazione consente di collegare le risorse esistenti all'applicazione gestita tramite il provider personalizzato definito in **mainTemplate.json**. Per un'introduzione ai provider personalizzati, vedere [Azure Custom Providers Preview overview (Panoramica dell'anteprima di provider personalizzati di Azure)](../custom-providers/overview.md).

In questa visualizzazione è possibile estendere `targetResourceType`le risorse di Azure esistenti in base al file . Quando una risorsa viene selezionata, verrà creata una richiesta di onboarding al provider personalizzato **pubblico,** che può applicare un effetto collaterale alla risorsa. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Proprietà|Obbligatoria|Descrizione|
|---------|---------|---------|
|displayName|Sì|Titolo visualizzato della vista. Il titolo deve essere **univoco** per ogni visualizzazione Associazioni in **viewDefinition.json**.|
|version|No|Versione della piattaforma utilizzata per eseguire il rendering della visualizzazione.|
|targetResourceType|Sì|Tipo di risorsa di destinazione. Questo è il tipo di risorsa che verrà visualizzato per l'onboarding delle risorse.|
|createUIDefinizione|No|Creare lo schema di definizione dell'interfaccia utente per il comando di creazione della risorsa di associazione. Per un'introduzione alla creazione di definizioni dell'interfaccia utente, vedere Introduzione a [CreateUiDefinitionFor](create-uidefinition-overview.md) an introduction to creating UI definitions, see Getting started with CreateUiDefinition|

## <a name="looking-for-help"></a>Assistenza

In caso di domande sulle applicazioni gestite di Azure, provare a inviarle a [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di pubblicarle. Aggiungere il tag `azure-managedapps` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](overview.md).
- Per un'introduzione ai provider personalizzati, vedere [Panoramica dei provider personalizzati](../custom-providers/overview.md)di Azure.For an introduction to custom providers, see Azure Custom Providers overview .
- Per la creazione di un'applicazione gestita di Azure con provider personalizzati di Azure, vedere [Esercitazione: Creare un'applicazione gestita con azioni del provider e tipi di risorse personalizzatiFor](tutorial-create-managed-app-with-custom-provider.md) creating an Azure Managed Application with Azure Custom Providers, see Tutorial: Create managed application with custom provider actions and resource types
