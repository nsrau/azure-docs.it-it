---
title: Parametri delle risorse delle cartelle di lavoro di monitoraggio di Azure
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: adc9855d30a3b286cfbc3489ec639ab8b460e314
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872809"
---
# <a name="workbook-resource-parameters"></a>Parametri delle risorse della cartella di lavoro

I parametri delle risorse consentono la selezione di risorse nelle cartelle di lavoro di. Questa operazione è utile per impostare l'ambito da cui ottenere i dati. Un esempio consente agli utenti di selezionare il set di macchine virtuali che i grafici in un secondo momento useranno per presentare i dati.

I valori dei selezionatori di risorse possono provenire dal contesto della cartella di lavoro, dall'elenco statico o dalle query del grafico di risorse di Azure.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Creazione di un parametro di risorsa (risorse della cartella di lavoro)
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante _Aggiungi parametro_ blu.
4. Nel riquadro nuovo parametro visualizzato immettere:
    1. Nome parametro: `Applications`
    2. Tipo di parametro: `Resource picker`
    3. Obbligatorio: `checked`
    4. Consenti selezioni multiple: `checked`
5. Recuperare i dati da: `Workbook Resources`
6. Includi solo tipi di risorse: `Application Insights`
7. Scegliere ' Salva ' dalla barra degli strumenti per creare il parametro.

![Immagine che mostra la creazione di un parametro di risorsa usando le risorse della cartella di lavoro](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Creazione di un parametro di risorsa (grafico risorse di Azure)
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante _Aggiungi parametro_ blu.
4. Nel riquadro nuovo parametro visualizzato immettere:
    1. Nome parametro: `Applications`
    2. Tipo di parametro: `Resource picker`
    3. Obbligatorio: `checked`
    4. Consenti selezioni multiple: `checked`
5. Recuperare i dati da: `Query`
    1. Tipo di query: `Azure Resource Graph`
    2. Sottoscrizioni: `Use default subscriptions`
    3. Nel controllo query aggiungere questo frammento di codice
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Scegliere ' Salva ' dalla barra degli strumenti per creare il parametro.

![Immagine che mostra la creazione di un parametro di risorsa usando il grafo delle risorse di Azure](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Il grafico risorse di Azure non è ancora disponibile in tutti i cloud. Se si sceglie questo approccio, assicurarsi che sia supportato nel cloud di destinazione.

[Documentazione di Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Creazione di un parametro di risorsa (elenco JSON)
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante _Aggiungi parametro_ blu.
4. Nel riquadro nuovo parametro visualizzato immettere:
    1. Nome parametro: `Applications`
    2. Tipo di parametro: `Resource picker`
    3. Obbligatorio: `checked`
    4. Consenti selezioni multiple: `checked`
5. Recuperare i dati da: `JSON`
    1. Nel controllo contenuto aggiungere questo frammento di codice JSON
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Premere il pulsante di _aggiornamento_ blu.
6. Facoltativamente, impostare il `Include only resource types` su _Application Insights_
7. Scegliere ' Salva ' dalla barra degli strumenti per creare il parametro.

## <a name="referencing-a-resource-parameter"></a>Riferimento a un parametro di risorsa
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa Application Insights.
2. Usare l'elenco a discesa _Application Insights_ per associare il parametro al controllo. Questa operazione consente di impostare l'ambito della query sulle risorse restituite dal parametro in fase di esecuzione.
4. Nel controllo KQL aggiungere questo frammento di codice
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Eseguire la query per visualizzare i risultati. 

![Immagine che mostra un parametro di risorsa a cui si fa riferimento in un controllo query](./media/workbooks-resources/resource-reference.png)

> Questo approccio può essere usato per associare le risorse ad altri controlli come le metriche.

## <a name="resource-parameter-options"></a>Opzioni del parametro Resource
| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `{Applications}` | ID risorsa selezionato | _/Subscriptions/< ID secondario >/resourceGroups/< Resource-Group >/providers/< Resource-Type >/acmeauthentication_ |
| `{Applications:label}` | Etichetta della risorsa selezionata | `acmefrontend` |
| `{Applications:value}` | Valore della risorsa selezionata | _'/subscriptions/< ID secondario >/resourceGroups/< Resource-Group >/providers/< Resource-Type >/acmeauthentication '_ |
| `{Applications:name}` | Nome della risorsa selezionata | `acmefrontend` |
| `{Applications:resourceGroup}` | Gruppo di risorse della risorsa selezionata | `acmegroup` |
| `{Applications:resourceType}` | Il tipo della risorsa selezionata | _Microsoft. Insights/Components_ |
| `{Applications:subscription}` | Sottoscrizione della risorsa selezionata |  |
| `{Applications:grid}` | Griglia che mostra le proprietà della risorsa. Utile per eseguire il rendering in un blocco di testo durante il debug  |  |

## <a name="next-steps"></a>Passaggi successivi

* Per [iniziare ad](workbooks-visualizations.md) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.