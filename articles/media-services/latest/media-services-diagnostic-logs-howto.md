---
title: Monitorare i log di diagnostica di Servizi multimediali tramite Monitoraggio di Azure Documenti Microsoft
description: Questo articolo illustra come instradare e visualizzare i log di diagnostica tramite Monitoraggio di Azure.This article demonstrates how to route and view diagnostic logs via Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 4d4587c701a054828fc34785e2ae680fef47625d
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382920"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Monitorare i log di diagnostica di Servizi multimedialiMonitor Media Services diagnostic logs

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle applicazioni. Per una descrizione dettagliata di questa funzionalità e per informazioni sul motivo per cui si vuole usare le metriche e i log di diagnostica di Servizi multimediali di Azure, vedere [Monitorare le metriche e i log](media-services-metrics-diagnostic-logs.md)di diagnostica di Servizi multimediali.

Questo articolo illustra come instradare i dati all'account di archiviazione e quindi visualizzare i dati.

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account di Servizi multimediali di Azure](create-account-cli-how-to.md).
- Esaminare [Monitorare le metriche e i log di diagnostica](media-services-metrics-diagnostic-logs.md)di Servizi multimediali.

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Instradare i dati all'account di archiviazione usando il portale

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passare all'account di Servizi multimediali in e fare clic su **Impostazioni di diagnostica** in **Monitor**. Di seguito viene visualizzato un elenco di tutte le risorse nella sottoscrizione che genera dati di monitoraggio tramite Monitoraggio di Azure.

    ![Sezione sulle impostazioni di diagnostica](media/media-services-diagnostic-logs/logs01.png)

1. Fare clic su **Aggiungi impostazione di diagnostica**.

   Un'impostazione di diagnostica della risorsa è una definizione di *quali* dati di monitoraggio devono essere indirizzati da una particolare risorsa e *dove* tali dati di monitoraggio devono essere inseriti.

1. Nella sezione che viene visualizzata assegnare un **nome** all'impostazione e selezionare la casella **Archivia in un account di archiviazione**.

    Selezionare l'account di archiviazione a cui si desidera inviare i log e premere **OK**.
1. Selezionare tutte le caselle in **Log** e **Metrica**. A seconda del tipo di risorsa, si può avere solo una di queste opzioni. Queste caselle di controllo consentono di controllare le categorie di log e dati di metrica disponibili per il tipo di risorsa che vengono inviate alla destinazione selezionata, in questo caso, un account di archiviazione.

   ![Sezione sulle impostazioni di diagnostica](media/media-services-diagnostic-logs/logs02.png)
1. Impostare il dispositivo di scorrimento **Retention (days)** (Conservazione (giorni)) su 30. Questo dispositivo di scorrimento consente di impostare un numero di giorni per conservare i dati di monitoraggio nell'account di archiviazione. Monitoraggio di Azure elimina automaticamente i dati precedenti al numero di giorni specificato. Se il valore di conservazione è zero giorni, i dati vengono conservati all'infinito.
1. Fare clic su **Salva**.

Il monitoraggio dei dati nella risorsa viene quindi trasmesso all'account di archiviazione.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Instradare i dati all'account di archiviazione usando l'interfaccia della riga di comando di AzureRoute data to the storage account using the Azure CLI

Per abilitare l'archiviazione dei log di diagnostica `az monitor diagnostic-settings` in un account di archiviazione, è necessario eseguire il comando dell'interfaccia della riga di comando di Azure seguente:To enable storage of diagnostic logs in a Storage Account, you would run the following Azure CLI command:

```azurecli-interactive
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Ad esempio:

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Visualizzare i dati nell'account di archiviazione tramite il portaleView data in the storage account using the portal

Se è stata seguita la procedura precedente, il processo di trasmissione dei dati all'account di archiviazione è iniziato.

Potrebbe essere necessario attendere fino a cinque minuti prima che l'evento venga visualizzato nell'account di archiviazione.

1. Nel portale passare alla sezione **Account di archiviazione** cercandola sulla barra di navigazione a sinistra.
1. Identificare l'account di archiviazione creato nella sezione precedente e fare clic su di esso.
1. Fare clic su **BLOB**, quindi sul contenitore con etichetta **insights-logs-keydeliveryrequests**. Questo è il contenitore che contiene i log. I dati di monitoraggio vengono suddivisi in contenitori in base all'ID risorsa, quindi in base alla data e all'ora.
1. Passare al file PT1H.json facendo clic sui contenitori per l'ID di risorsa, data e ora. Fare clic sul file PT1H.json e quindi su **Download**.

 È ora possibile visualizzare l'evento JSON che è stato archiviato nell'account di archiviazione.

### <a name="examples-of-pt1hjson"></a>Esempi di PT1H.json

#### <a name="clear-key-delivery-log"></a>Cancella registro di recapito delle chiavi

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Log di recapito delle chiavi crittografate Widevine

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="see-also"></a>Vedere anche

* [Metriche di Monitoraggio di AzureAzure Monitor Metrics](../../azure-monitor/platform/data-platform.md)
* [Log di diagnostica di Monitoraggio di AzureAzure Monitor Diagnostic logs](../../azure-monitor/platform/platform-logs-overview.md)
* [Come raccogliere e usare i dati di log dalle risorse di AzureHow to collect and consume log data from your Azure resources](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="next-steps"></a>Passaggi successivi

[Monitorare le metriche](media-services-metrics-howto.md)
