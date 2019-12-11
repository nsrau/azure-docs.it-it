---
title: Diagnosticare e risolvere i problemi relativi alle disconnessioni con l'hub Internet Azure
description: Informazioni su come diagnosticare e risolvere gli errori comuni con la connettività dei dispositivi per il servizio Device provisioning in hub Azure
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: ffe20ff80e26bc5564b9379ea21ca99e2890b519
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974820"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Risoluzione dei problemi con il servizio Device provisioning in hub Azure

I problemi di connettività per i dispositivi Internet possono essere difficili da risolvere perché esistono molti possibili punti di errore, ad esempio errori di attestazione, errori di registrazione e così via. Questo articolo fornisce indicazioni su come rilevare e risolvere i problemi di connettività del dispositivo tramite [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Uso di monitoraggio di Azure per visualizzare le metriche e configurare gli avvisi

La procedura seguente descrive come visualizzare e configurare l'avviso sulla metrica del servizio Device provisioning in hub Internet. 

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare al servizio Device provisioning in hub Internet.

3. Selezionare **Metriche**.

4. Selezionare la metrica desiderata. 
   <br />Attualmente sono disponibili tre metriche per DPS:

    | Nome della metrica | Description |
    |-------|------------|
    | Tentativi di attestazione | Numero di dispositivi che hanno tentato di eseguire l'autenticazione con il servizio Device provisioning|
    | Tentativi di registrazione | Numero di dispositivi che hanno tentato di eseguire la registrazione nell'hub Internet al termine dell'autenticazione|
    | Dispositivo assegnato | Numero di dispositivi assegnati correttamente all'hub Internet|

5. Selezionare il metodo di aggregazione desiderato per creare una visualizzazione visiva della metrica. 

6. Per impostare un avviso di una metrica, selezionare **nuove regole di avviso** nella parte superiore destra del pannello metrica. in modo analogo, è possibile passare al pannello **avviso** e selezionare **nuove regole di avviso**.

7. Selezionare **Aggiungi condizione**, quindi selezionare la metrica e la soglia desiderate seguendo le istruzioni.

Per altre informazioni, vedere [che cosa sono gli avvisi classici in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Uso di log Analytics per visualizzare e risolvere gli errori

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare all'hub IoT.

3. Selezionare **Impostazioni di diagnostica**.

4. Selezionare **Attiva diagnostica**.

5. Abilitare la raccolta dei log desiderati.

    | Nome registro | Description |
    |-------|------------|
    | DeviceOperations | Log relativi agli eventi di connessione del dispositivo |
    | ServiceOperations | Log eventi correlati all'uso di Service SDK, ad esempio la creazione o l'aggiornamento di gruppi di registrazioni|

6. Attivare **Invia a log Analytics** ([vedere i prezzi](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Passare alla scheda **logs** nel portale di Azure nella risorsa del servizio Device provisioning.

8. Fare clic su **Esegui** per visualizzare gli eventi recenti.

9. Se sono presenti risultati, cercare `OperationName`, `ResultType`, `ResultSignature`e `ResultDescription` (messaggio di errore) per ottenere maggiori dettagli sull'errore.


## <a name="common-error-codes"></a>Codici di errore comuni
Usare questa tabella per comprendere e risolvere gli errori comuni.

| Codice di errore| Description | Codice di stato HTTP |
|-------|------------|------------|
| 400 | Il corpo della richiesta non è valido. ad esempio, non può essere analizzato o l'oggetto non può essere convalidato.| 400 formato non valido |
| 401 | Il token di autorizzazione non può essere convalidato. ad esempio, è scaduta o non si applica all'URI della richiesta. Questo codice di errore viene anche restituito ai dispositivi come parte del flusso di attestazione TPM. | 401 - Non autorizzato|
| 404 | L'istanza del servizio Device provisioning o una risorsa, ad esempio una registrazione, non esiste. |404 - Pagina non trovata |
| 412 | Il valore ETag nella richiesta non corrisponde al valore ETag della risorsa esistente, in base al valore di RFC7232. | 412 Precondizione non riuscita |
| 429 | Le operazioni sono limitate dal servizio. Per i limiti di servizio specifici, vedere [limiti del servizio Device provisioning in hub](https://docs.microsoft.com/azure/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 numero eccessivo di richieste |
| 500 | An internal error occurred. | 500 - Errore interno del server|
