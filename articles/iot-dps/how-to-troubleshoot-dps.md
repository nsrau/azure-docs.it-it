---
title: Diagnosticare e risolvere i problemi di disconnessione con DPS dell'hub IoT di AzureDiagnose and troubleshoot disconnects with Azure IoT Hub DPS
description: Informazioni su come diagnosticare e risolvere gli errori comuni con la connettività dei dispositivi per il servizio Diprovisioning dei dispositivi Hub (DPS) di Azure IoTLearn learn to diagnose and troubleshoot common errors with device connectivity for Azure IoT Hub Device Provisioning Service (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646473"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Risoluzione dei problemi con il servizio di provisioning dei dispositivi dell'hub IoT di AzureTroubleshooting with Azure IoT Hub Device Provisioning Service

Problemi di connettività per i dispositivi IoT possono essere difficili da risolvere perché esistono molti possibili punti di errore, ad esempio errori di attestazione, errori di registrazione e così via. Questo articolo fornisce indicazioni su come rilevare e risolvere i problemi di connettività dei dispositivi tramite [Monitoraggio di Azure.](https://docs.microsoft.com/azure/azure-monitor/overview)

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Uso di Monitoraggio di Azure per visualizzare le metriche e configurare gli avvisiUsing Azure Monitor to view metrics and set up alerts

La procedura seguente descrive come visualizzare e configurare gli avvisi nella metrica del servizio di provisioning dei dispositivi dell'hub IoT.The following procedure describes how to view and set up alert on IoT Hub Device Provisioning Service metric. 

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Passare al servizio di provisioning dei dispositivi dell'hub IoT.Browse to your IoT Hub Device Provisioning Service.

3. Selezionare **Metriche**.

4. Selezionare la metrica desiderata. 
   <br />Attualmente ci sono tre metriche per DPS:

    | Nome misurazione | Descrizione |
    |-------|------------|
    | Tentativi di attestazione | Numero di dispositivi che hanno tentato di eseguire l'autenticazione con il servizio Device Provisioning|
    | Tentativi di registrazione | Numero di dispositivi che hanno tentato di registrarsi nell'hub IoT dopo la corretta autenticazione|
    | Dispositivo assegnato | Numero di dispositivi assegnati correttamente all'hub IoT|

5. Selezionare il metodo di aggregazione desiderato per creare una visualizzazione visiva della metrica. 

6. Per impostare un avviso di una metrica, selezionare **Nuove regole** di avviso nella parte superiore destra del pannello della metrica, in modo analogo, è possibile passare a Pannello **di avviso** e selezionare Nuove regole **di avviso**.

7. Selezionare **Aggiungi condizione**, quindi selezionare la metrica e la soglia desiderate seguendo i prompt riportati di seguito.

Per altre informazioni, vedere [Che cosa sono gli avvisi classici in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Utilizzo di Log Analytic per visualizzare e risolvere gli errori

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Passare all'hub IoT.

3. Selezionare **Impostazioni di diagnostica**.

4. Selezionare **Attiva diagnostica**.

5. Abilitare i registri desiderati da raccogliere.

    | Nome registro | Descrizione |
    |-------|------------|
    | DeviceOperations | Registri correlati agli eventi di connessione del dispositivoLogs related to device connection events |
    | ServiceOperations | Registri eventi correlati all'utilizzo dell'SDK del servizio (ad esempio creazione o aggiornamento dei gruppi di registrazione)Event logs related to using service SDK (e.g. Creating or updating enrollment groups)|

6. Attivare **Invia a Log Analytics** [(vedere prezzi).](https://azure.microsoft.com/pricing/details/log-analytics/) 

7. Passare alla scheda **Logs** nel portale di Azure in Risorsa del servizio di provisioning dei dispositivi.

8. Fare clic su **Esegui** per visualizzare gli eventi recenti.

9. Se sono presenti risultati, `ResultType` `ResultSignature`cercare `ResultDescription` `OperationName`, , e (messaggio di errore) per ottenere maggiori dettagli sull'errore.


## <a name="common-error-codes"></a>Codici di errore comuni
Usare questa tabella per comprendere e risolvere gli errori comuni.

| Codice di errore| Descrizione | Codice di stato HTTP |
|-------|------------|------------|
| 400 | Il corpo della richiesta non è valido; ad esempio, non può essere analizzato o l'oggetto non può essere convalidato.| 400 Formato non valido |
| 401 | Il token di autorizzazione non può essere convalidato. ad esempio, è scaduto o non si applica all'URI della richiesta. Questo codice di errore viene restituito anche ai dispositivi come parte del flusso di attestazione TPM. | 401 - Non autorizzato|
| 404 | L'istanza del servizio Device Provisioning o una risorsa (ad esempio una registrazione) non esiste. |404 - Non trovato |
| 412 | L'ETag nella richiesta non corrisponde all'ETag della risorsa esistente, come richiesto da RFC7232. | 412 Precondizione non riuscita |
| 429 | Le operazioni vengono limitate dal servizio. Per limiti di servizio specifici, vedere [Limiti del servizio di provisioning dei dispositivi Hub IoT](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 Troppe richieste |
| 500 | An internal error occurred. | 500 - Errore interno del server|
