---
title: Informazioni sulle quote e sulle limitazioni dell&quot;hub IoT di Azure | Documentazione Microsoft
description: 'Guida per gli sviluppatori: descrizione delle quote che si applicano all&quot;hub IoT e del comportamento di limitazione previsto.'
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e9171844e2fbac7a4e734be9144b30d1bbd0397f
ms.lasthandoff: 03/30/2017


---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Riferimento - Quote e limitazioni dell'hub IoT

## <a name="quotas-and-throttling"></a>Quote e limitazioni
Ogni sottoscrizione di Azure può avere al massimo 10 hub IoT e al massimo un hub gratuito.

Ogni hub IoT viene sottoposto a provisioning con un determinato numero di unità in uno SKU specifico. Per altre informazioni, vedere [Prezzi dell'hub IoT di Azure][lnk-pricing]. Lo SKU e il numero di unità determinano la quota giornaliera massima dei messaggi che è possibile inviare.

Lo SKU determina anche le limitazioni che l'hub IoT applica alle operazioni.

## <a name="operation-throttles"></a>Limitazioni per le operazioni
Le limitazioni per le operazioni sono limitazioni di frequenza applicate con intervalli di minuti e consentono di evitare abusi. L'hub IoT prova a evitare la restituzione di errori, se possibile, ma inizia a restituire eccezioni se la limitazione viene violata troppo a lungo.

Ecco di seguito l'elenco di limitazioni applicate. I valori fanno riferimento a un singolo hub.

| Limitazione | Hub gratuiti e S1 | Hub S2 | Hub S3 | 
| -------- | ------- | ------- | ------- |
| Operazioni del registro delle identità (creazione, recupero, elenco, aggiornamento, eliminazione) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83,33/sec/unità (5000/min/unità) |
| Connessioni del dispositivo | Al massimo 100/sec o 12/sec/unità <br/> Ad esempio, due unità S1 sono 2\*12 = 24/sec, ma si hanno almeno 100/sec tra le unità. Con nove unità S1 si otterrà 108/sec (9\*12) tra le unità. | 120/sec/unità | 6000/sec/unità |
| Inoltri dal dispositivo al cloud | Al massimo 100/sec o 12/sec/unità <br/> Ad esempio, due unità S1 sono 2\*12 = 24/sec, ma si hanno almeno 100/sec tra le unità. Con nove unità S1 si otterrà 108/sec (9\*12) tra le unità. | 120/sec/unità | 6000/sec/unità |
| Inoltri dal cloud al dispositivo | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83,33/sec/unità (5000/min/unità) |
| Ricezioni dal cloud al dispositivo <br/> (solo quando il dispositivo usa HTTP)| 16,67/sec/unità (1000/min/unità) | 16,67/sec/unità (1000/min/unità) | 833,33/sec/unità (50000/min/unità) |
| Caricamento di file | 1,67 notifice caricamento file/sec/unità (100/min/unità) | 1,67 notifice caricamento file/sec/unità (100/min/unità) | 83,33 notifice caricamento file/sec/unità (5000/min/unità) |
| Metodi diretti | 10/sec/unità | 30/sec/unità | 1500/sec/unità | 
| Letture del dispositivo gemello | 10/sec | Al massimo 10/sec o 1/sec/unità | 50/sec/unità |
| Aggiornamenti dei dispositivi gemelli | 10/sec | Al massimo 10/sec o 1/sec/unità | 50/sec/unità |
| Operazioni dei processi <br/> (creazione, aggiornamento, elenco, eliminazione) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83,33/sec/unità (5000/min/unità) |
| Velocità effettiva delle operazioni dei processi per dispositivo | 10/sec | Al massimo 10/sec o 1/sec/unità | 50/sec/unità |

È importante chiarire che la limitazione delle *connessioni del dispositivo* determina la frequenza con cui possono essere stabilite nuove connessioni del dispositivo con un hub IoT e non il numero massimo di dispositivi connessi contemporaneamente. La limitazione dipende dal numero di unità di cui viene eseguito il provisioning per l'hub IoT.

Ad esempio, se si acquista una singola unità S1, si ottiene un limite di 100 connessioni al secondo. Di conseguenza, per connettere 100.000 dispositivi sono necessari almeno 1000 secondi (circa 16 minuti). Tuttavia, è consentito un numero di dispositivi connessi simultaneamente pari al numero di dispositivi registrati nel registro delle identità.

Per un'analisi approfondita del comportamento della limitazione dell'hub IoT, vedere il post del blog [IoT Hub throttling and you][lnk-throttle-blog] (Limitazione dell'hub IoT).

> [!NOTE]
> È possibile incrementare le quote o le limitazioni in qualsiasi momento aumentando il numero di unità sottoposte a provisioning in un hub IoT.
> 
> [!IMPORTANT]
> Le operazioni del registro delle identità sono destinate all'uso in fase di esecuzione negli scenari di gestione e provisioning dei dispositivi. L'operazione di lettura o aggiornamento di un numero elevato di identità dei dispositivi è supportata tramite i [processi di importazione ed esportazione][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Altri limiti

L'hub IoT applica altri limiti sulle diverse funzionalità.

| Operazione | Limite |
| --------- | ----- |
| URI per il caricamento di file | 10000 URI di firma di accesso condiviso possono essere generati contemporaneamente per un account di archiviazione. <br/> 10 URI di firma di accesso condiviso/dispositivo possono essere generati contemporaneamente. |
| Processi | La cronologia dei processi viene mantenuta per un massimo di 30 giorni. <br/> Il numero massimo di processi simultanei è 1 (per il livello Gratuito e S1), 5 (per S2), 10 (per S3). |
| Altri endpoint | Agli hub SKU a pagamento possono essere associati 10 endpoint aggiuntivi. Agli hub SKU gratuiti può essere associato solo un endpoint aggiuntivo. |
| Regole di routing dei messaggi | Agli hub SKU a pagamento possono essere associate 100 regole di routing. Agli hub SKU gratuiti possono essere associate cinque regole di routing. |

> [!NOTE]
> Attualmente, il numero massimo di dispositivi che è possibile connettere a un singolo hub IoT è 500.000. Per aumentare questo limite, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/en-us/support/options/).

## <a name="next-steps"></a>Passaggi successivi
Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-devguide-endpoints]
* [Linguaggio di query dell'hub IoT per dispositivi gemelli e processi][lnk-devguide-query]
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

