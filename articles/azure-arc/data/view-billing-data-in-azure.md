---
title: Caricare i dati di fatturazione in Azure e visualizzarli nel portale di Azure
description: Caricare i dati di fatturazione in Azure e visualizzarli nel portale di Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5335442c7ffcdca950ba0e9c5f3b6bc9e4be9f63
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108151"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Caricare i dati di fatturazione in Azure e visualizzarli nel portale di Azure

> [!IMPORTANT] 
>  Non è previsto alcun costo per l'uso di Azure Arc Enabled Data Services durante il periodo di anteprima. Sebbene il sistema di fatturazione funzioni end-to-end, il contatore della fatturazione è impostato su $0.  Se si segue questo scenario, vengono visualizzate le voci della fatturazione per un servizio attualmente denominato **Hybrid Data Services** e per le risorse di un tipo denominato **Microsoft. AzureData/ `<resource type>` **. Sarà possibile visualizzare un record per ogni servizio dati-Azure Arc creato, ma ogni record verrà fatturato per $0.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>Modalità di connettività: implicazioni per i dati di fatturazione

In futuro, saranno disponibili due modalità in cui è possibile eseguire i servizi dati abilitati per Azure Arc:

- **Connessa indirettamente** : non esiste alcuna connessione diretta ad Azure. I dati vengono inviati ad Azure solo tramite un processo di esportazione/caricamento. Tutte le distribuzioni di Azure Arc Data Services funzionano in questa modalità oggi in anteprima.
- **Connessione diretta: in** questa modalità sarà presente una dipendenza dal servizio Kubernetes abilitato per Azure Arc per fornire una connessione diretta tra Azure e il cluster Kubernetes in cui sono in esecuzione i servizi dati abilitati per Azure Arc. Questo consentirà di abilitare un numero maggiore di funzionalità e consentirà anche di usare il portale di Azure e l'interfaccia della riga di comando di Azure per gestire i servizi dati abilitati per Azure Arc come la gestione dei servizi dati in Azure PaaS.  Questa modalità di connettività non è ancora disponibile in anteprima, ma sarà presto disponibile.

Per altre informazioni sulla differenza tra le modalità di [connettività](./connectivity.md), vedere.

In modalità connessa indirettamente i dati di fatturazione vengono periodicamente esportati dal controller dati di Azure Arc a un file protetto e quindi caricati in Azure ed elaborati.  Nell'imminente modalità di connessione diretta i dati di fatturazione verranno inviati automaticamente ad Azure circa 1 ora per offrire una visualizzazione quasi in tempo reale ai costi dei servizi. Il processo di esportazione e caricamento dei dati nella modalità connessa indirettamente può inoltre essere automatizzato tramite script oppure è possibile creare un servizio che lo eseguirà automaticamente.

## <a name="upload-billing-data-to-azure"></a>Caricare i dati di fatturazione in Azure

Per caricare i dati di fatturazione in Azure, è necessario che si verifichi prima quanto segue:

1. Creare un servizio dati abilitato per Azure Arc se non ne è già disponibile uno. Ad esempio, creare uno dei seguenti elementi:
   - [Creare un'istanza gestita di SQL di Azure in Azure Arc](create-sql-managed-instance.md)
   - [Creare un gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc](create-postgresql-hyperscale-server-group.md)
1. Se non è già stato fatto, [caricare l'inventario delle risorse, i dati di utilizzo, le metriche e i log in monitoraggio di Azure](upload-metrics-and-logs-to-azure-monitor.md) .
1. Attendere almeno 2 ore dalla creazione del servizio dati in modo che il processo di raccolta dei dati di telemetria di fatturazione possa raccogliere alcuni dati di fatturazione.

Eseguire il comando seguente per esportare i dati di fatturazione:

```console
azdata arc dc export -t usage -p usage.json
```

Per il momento, il file non è crittografato in modo che sia possibile visualizzarne il contenuto. È possibile aprire in un editor di testo e visualizzare l'aspetto del contenuto.

Si noterà che sono presenti due set di dati: `resources` e `data` . `resources`Sono il controller dati, i gruppi di server con iperscalabilità PostgreSQL e le istanze gestite di SQL. I `resources` record nei dati acquisiscono gli eventi pertinenti nella cronologia di una risorsa, quando è stato creato, quando è stato aggiornato e quando è stato eliminato. I `data` record acquisiscono il numero di core disponibili per l'uso da parte di una determinata istanza per ogni ora.

Esempio di `resource` voce:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

Esempio di `data` voce:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

Eseguire il comando seguente per caricare il usage.jsnel file in Azure:

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Visualizzare i dati di fatturazione in portale di Azure

Per visualizzare i dati di fatturazione nel portale di Azure, attenersi alla procedura seguente:

1. Aprire il portale di Azure usando l'URL speciale:  [https://aka.ms/arcdata](https://aka.ms/arcdata) .
1. Nella casella di ricerca nella parte superiore della schermata digitare **Gestione costi** , quindi fare clic sul servizio gestione costi.
1. Fare clic sulla scheda **analisi costi** a sinistra.
1. Fare clic sul pulsante **costo per risorsa** nella parte superiore della visualizzazione.
1. Verificare che l'ambito sia impostato sulla sottoscrizione in cui sono state create le risorse del servizio dati.
1. Selezionare **costo per risorsa** nell'elenco a discesa della vista accanto al selettore dell'ambito nella parte superiore della visualizzazione.
1. Verificare che il filtro della data sia impostato su **questo mese** o su un altro intervallo di tempo che abbia senso in base all'intervallo di tempo durante il quale sono state create le risorse del servizio dati.
1. Fare clic su **Aggiungi filtro** per aggiungere un filtro per **tipo di risorsa**  =  `microsoft.azuredata/<data service type>` se si desidera filtrare in base a un solo tipo di servizio dati abilitato per Azure Arc.
1. Verrà ora visualizzato un elenco di tutte le risorse che sono state create e caricate in Azure. Poiché il contatore per la fatturazione è $0, si noterà che il costo è sempre $0.

## <a name="download-billing-data"></a>Scarica i dati di fatturazione

È possibile scaricare i dati di riepilogo della fatturazione direttamente dalla portale di Azure.

1. Nella stessa **analisi dei costi-> visualizzazione per tipo di risorsa** raggiunta seguendo le istruzioni riportate in precedenza, fare clic sul pulsante Download nella parte superiore.
1. Scegliere il tipo di file di download, Excel o CSV, e fare clic sul pulsante **Scarica dati** .
1. Aprire il file in un editor appropriato in base al tipo di file selezionato.

## <a name="export-billing-data"></a>Esportare i dati di fatturazione

È anche possibile esportare periodicamente i dati **dettagliati** sull'utilizzo e sulla fatturazione in un contenitore di archiviazione di Azure creando un processo di esportazione della fatturazione. Questa opzione è utile se si desidera visualizzare i dettagli della fatturazione, ad esempio il numero di ore per cui una determinata istanza è stata fatturata nel periodo di fatturazione.

Per configurare un processo di esportazione fatturazione, attenersi alla procedura seguente:

1. Fare clic su Exports (Esporta) a sinistra.
1. Fare clic su Aggiungi.
1. Immettere un nome e una frequenza di esportazione, quindi fare clic su Avanti.
1. Scegliere di creare un nuovo account di archiviazione o crearne uno nuovo e compilare il modulo per specificare l'account di archiviazione, il contenitore e il percorso della directory in cui esportare i file di dati di fatturazione, quindi fare clic su Avanti.
1. Fare clic su Crea.

I file di esportazione dei dati di fatturazione saranno disponibili in circa 4 ore e verranno esportati nella pianificazione specificata durante la creazione del processo di esportazione della fatturazione.

Per visualizzare i file di dati di fatturazione esportati, attenersi alla procedura seguente:

È possibile convalidare i file di dati di fatturazione nel portale di Azure. 

> [!IMPORTANT]
> Dopo aver creato il processo di esportazione della fatturazione, attendere 4 ore prima di procedere con i passaggi seguenti.

1. Nella casella di ricerca nella parte superiore del portale digitare **account di archiviazione** e fare clic su **account di archiviazione**.
3. Fare clic sull'account di archiviazione specificato durante la creazione del processo di esportazione fatturazione precedente.
4. Fare clic su contenitori sulla sinistra.
5. Fare clic sul contenitore specificato durante la creazione del processo di esportazione fatturazione precedente.
6. Fare clic sulla cartella specificata durante la creazione del processo di esportazione fatturazione precedente.
7. Eseguire il drill-down nelle cartelle e nei file generati e fare clic su uno dei file CSV generati.
8. Fare clic sul pulsante download che consente di salvare il file nella cartella download locale.
9. Aprire il file usando un visualizzatore di file con estensione CSV, ad esempio Excel.
10. Filtrare i risultati per visualizzare solo le righe con il **tipo di risorsa**  =  `Microsoft.AzureData/<data service resource type` .
11. Viene visualizzato il numero di ore in cui l'istanza è stata usata nel periodo di 24 ore corrente nella colonna UsageQuantity.