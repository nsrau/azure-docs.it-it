---
title: Parametri delle risorse delle cartelle di lavoro di Azure MonitorAzure Monitor workbooks resource parameters
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658103"
---
# <a name="workbook-resource-parameters"></a>Parametri delle risorse della cartella di lavoroWorkbook resource parameters

I parametri delle risorse consentono la selezione delle risorse nelle cartelle di lavoro. Ciò è utile per impostare l'ambito da cui ottenere i dati da. Un esempio è consentire agli utenti di selezionare il set di macchine virtuali, che i grafici verranno utilizzati in un secondo momento durante la presentazione dei dati.

I valori delle selezioni risorse possono provenire dal contesto della cartella di lavoro, dall'elenco statico o dalle query di Azure Resource Graph.Values from resource pickers can come from the workbook context, static list or from Azure Resource Graph queries.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Creazione di un parametro di risorsa (risorse cartella di lavoro)Creating a resource parameter (workbook resources)
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante blu _Aggiungi parametro._
4. Nel nuovo riquadro dei parametri visualizzato immettere:
    1. Nome parametro:`Applications`
    2. Tipo di parametro:`Resource picker`
    3. Obbligatorio:`checked`
    4. Consenti selezioni multiple:`checked`
5. Ottenere dati da:`Workbook Resources`
6. Includere solo i tipi di risorse:Include only resource types:`Application Insights`
7. Scegliere 'Salva' dalla barra degli strumenti per creare il parametro.

![Immagine che mostra la creazione di un parametro di risorsa utilizzando le risorse della cartella di lavoro](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Creazione di un parametro di risorsa (Azure Resource Graph)Creating a resource parameter (Azure Resource Graph)
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante blu _Aggiungi parametro._
4. Nel nuovo riquadro dei parametri visualizzato immettere:
    1. Nome parametro:`Applications`
    2. Tipo di parametro:`Resource picker`
    3. Obbligatorio:`checked`
    4. Consenti selezioni multiple:`checked`
5. Ottenere dati da:`Query`
    1. Tipo di query:`Azure Resource Graph`
    2. Sottoscrizioni:`Use default subscriptions`
    3. Nel controllo query aggiungere questo frammento di codice
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Scegliere 'Salva' dalla barra degli strumenti per creare il parametro.

![Immagine che mostra la creazione di un parametro di risorsa con Azure Resource Graph](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Resource Graph non è ancora disponibile in tutti i cloud. Assicurarsi che sia supportato nel cloud di destinazione se si sceglie questo approccio.

[Documentazione di Azure Resource GraphAzure Resource Graph documentation](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Creazione di un parametro di risorsa (elenco JSON)Creating a resource parameter (JSON list)
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante blu _Aggiungi parametro._
4. Nel nuovo riquadro dei parametri visualizzato immettere:
    1. Nome parametro:`Applications`
    2. Tipo di parametro:`Resource picker`
    3. Obbligatorio:`checked`
    4. Consenti selezioni multiple:`checked`
5. Ottenere dati da:`JSON`
    1. Nel controllo contenuto aggiungere questo frammento jsonIn the content control, add this json snippet
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Premi il pulsante blu _Aggiorna._
6. Facoltativamente, `Include only resource types` impostare l'oggetto su _Application Insights_
7. Scegliere 'Salva' dalla barra degli strumenti per creare il parametro.

## <a name="referencing-a-resource-parameter"></a>Riferimento a un parametro di risorsa
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa di Application Insights.Add a query control to the workbook and select an Application Insights resource.
2. Usare l'elenco a discesa _Application Insights_ per associare il parametro al controllo. In questo modo l'ambito della query viene impostato sulle risorse restituite dal parametro in fase di esecuzione.
4. Nel controllo KQL, aggiungere questo frammento di codice
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Eseguire una query per visualizzare i risultati. 

![Immagine che mostra un parametro di risorsa a cui viene fatto riferimento in un controllo query](./media/workbooks-resources/resource-reference.png)

> Questo approccio può essere usato per associare le risorse ad altri controlli come le metriche.

## <a name="resource-parameter-options"></a>Opzioni dei parametri della risorsa
| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `{Applications}` | L'ID risorsa selezionata | _/subscriptions/<id>>/resourceGroups/<gruppo di risorse>/providers/<>/acmeauthentication di tipo di risorsa_ |
| `{Applications:label}` | L'etichetta della risorsa selezionata | `acmefrontend` |
| `{Applications:value}` | Il valore della risorsa selezionata | _'/sottoscrizioni/<id>/resourceGroups/<gruppo di risorse>/providers/<tipo di risorsa>/acmeauthentication''/subscriptions/sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication'_ |
| `{Applications:name}` | Il nome della risorsa selezionata | `acmefrontend` |
| `{Applications:resourceGroup}` | Il gruppo di risorse della risorsa selezionata | `acmegroup` |
| `{Applications:resourceType}` | Il tipo della risorsa selezionata | _microsoft.insights/components_ |
| `{Applications:subscription}` | La sottoscrizione della risorsa selezionata |  |
| `{Applications:grid}` | Griglia che mostra le proprietà della risorsa. Utile per eseguire il rendering in un blocco di testo durante il debug  |  |

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a](workbooks-visualizations.md) conoscere le cartelle di lavoro su molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
