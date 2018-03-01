---
title: Informazioni sulle quote e sulle limitazioni dell'hub IoT di Azure | Documentazione Microsoft
description: 'Guida per gli sviluppatori: descrizione delle quote che si applicano all''hub IoT e del comportamento di limitazione previsto.'
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
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 7aa963e7f7463dbc06ba98e4876ea5d98f6921c8
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Riferimento - Quote e limitazioni dell'hub IoT

## <a name="quotas-and-throttling"></a>Quote e limitazioni
Ogni sottoscrizione di Azure può avere al massimo 10 hub IoT e al massimo un hub gratuito.

Il provisioning di ogni hub IoT viene eseguito con un determinato numero di unità in uno SKU specifico. Per altre informazioni, vedere [Prezzi di Hub IoT][lnk-pricing]. Lo SKU e il numero di unità determinano la quota giornaliera massima dei messaggi che è possibile inviare.

Lo SKU determina anche le limitazioni che l'hub IoT applica alle operazioni.

## <a name="operation-throttles"></a>Limitazioni per le operazioni
Le limitazioni per le operazioni sono limitazioni di frequenza, applicate a intervalli di minuti, con lo scopo di evitare abusi. L'hub IoT cerca di evitare di restituire errori, se possibile, ma inizia a restituire eccezioni se la limitazione viene violata troppo a lungo.

È possibile incrementare i limiti delle quote o delle limitazioni in qualsiasi momento aumentando il numero di unità sottoposte a provisioning in un hub IoT.

La tabella seguente mostra le limitazioni applicate. I valori fanno riferimento a un singolo hub.

| Limitazione | Hub gratuiti e S1 | Hub S2 | Hub S3 | 
| -------- | ------- | ------- | ------- |
| Operazioni del registro delle identità (creazione, recupero, elenco, aggiornamento, eliminazione) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83,33/sec/unità (5000/min/unità) |
| Connessioni del dispositivo | Più di 100/sec o 12/sec/unità <br/> Ad esempio, due unità S1 sono 2\*12 = 24/sec, ma si hanno almeno 100/sec tra le unità. Con nove unità S1 si otterrà 108/sec (9\*12) tra le unità. | 120/sec/unità | 6000/sec/unità |
| Inoltri dal dispositivo al cloud | Più di 100/sec o 12/sec/unità <br/> Ad esempio, due unità S1 sono 2\*12 = 24/sec, ma si hanno almeno 100/sec tra le unità. Con nove unità S1 si otterrà 108/sec (9\*12) tra le unità. | 120/sec/unità | 6000/sec/unità |
| Inoltri dal cloud al dispositivo | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83,33/sec/unità (5000/min/unità) |
| Ricezioni dal cloud al dispositivo <br/> (solo quando il dispositivo usa HTTPS)| 16,67/sec/unità (1000/min/unità) | 16,67/sec/unità (1000/min/unità) | 833,33/sec/unità (50000/min/unità) |
| Caricamento di file | 1,67 notifice caricamento file/sec/unità (100/min/unità) | 1,67 notifice caricamento file/sec/unità (100/min/unità) | 83,33 notifice caricamento file/sec/unità (5000/min/unità) |
| Metodi diretti | 160 KB/sec/unità<sup>1</sup> | 480 KB/sec/unità<sup>1</sup> | 24 MB/sec/unità<sup>1</sup> | 
| Letture del dispositivo gemello | 10/sec | Più di 10/sec o 1/sec/unità | 50/sec/unità |
| Aggiornamenti dei dispositivi gemelli | 10/sec | Più di 10/sec o 1/sec/unità | 50/sec/unità |
| Operazioni dei processi <br/> (creazione, aggiornamento, elenco, eliminazione) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83,33/sec/unità (5000/min/unità) |
| Velocità effettiva delle operazioni dei processi per dispositivo | 10/sec | Più di 10/sec o 1/sec/unità | 50/sec/unità |

<sup>1</sup>La dimensione del contatore delle limitazioni è 8 KB

La limitazione delle *connessioni del dispositivo* determina la frequenza con cui possono essere stabilite nuove connessioni del dispositivo con un hub IoT. La limitazione delle *connessioni del dispositivo* non determina il numero massimo di dispositivi connessi contemporaneamente. La limitazione dipende dal numero di unità di cui viene eseguito il provisioning per l'hub IoT.

Ad esempio, se si acquista una singola unità S1, si ottiene un limite di 100 connessioni al secondo. Di conseguenza, per connettere 100.000 dispositivi sono necessari almeno 1000 secondi (circa 16 minuti). Tuttavia, è consentito un numero di dispositivi connessi simultaneamente pari al numero di dispositivi registrati nel registro delle identità.

Per un'analisi approfondita del comportamento della limitazione dell'hub IoT, vedere il post del blog [IoT Hub throttling and you][lnk-throttle-blog] (Limitazione dell'hub IoT).

> [!IMPORTANT]
> Le operazioni del registro delle identità sono destinate all'uso in fase di esecuzione negli scenari di gestione e provisioning dei dispositivi. L'operazione di lettura o aggiornamento di un numero elevato di identità dei dispositivi è supportata tramite i [processi di importazione ed esportazione][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Altri limiti

L'hub IoT applica altri limiti operativi:

| Operazione | Limite |
| --------- | ----- |
| URI per il caricamento di file | 10000 URI di firma di accesso condiviso possono essere generati contemporaneamente per un account di archiviazione. <br/> 10 URI di firma di accesso condiviso/dispositivo possono essere generati contemporaneamente. |
| Processi | La cronologia dei processi viene mantenuta per un massimo di 30 giorni. <br/> Il numero massimo di processi simultanei è 1 (per il livello Gratuito) e S1, 5 (per S2), 10 (per S3). |
| Altri endpoint | Agli hub SKU a pagamento possono essere associati 10 endpoint aggiuntivi. Agli hub SKU gratuiti può essere associato solo un endpoint aggiuntivo. |
| Regole di routing dei messaggi | Agli hub SKU a pagamento possono essere associate 100 regole di routing. Agli hub SKU gratuiti possono essere associate cinque regole di routing. |
| Messaggistica da dispositivo a cloud | Dimensioni massime dei messaggi 256 KB |
| Messaggistica da cloud a dispositivo | Dimensioni massime dei messaggi 64 KB |
| Messaggistica da cloud a dispositivo | Il numero massimo di messaggi in sospeso è 50 |
| Metodo diretto | La dimensione massima del payload del metodo diretto è 128 KB |

> [!NOTE]
> Attualmente, il numero massimo di dispositivi che è possibile connettere a un singolo hub IoT è 500.000. Per aumentare questo limite, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Latenza
L'hub IoT punta a fornire bassa latenza per tutte le operazioni. Tuttavia, a causa delle condizioni della rete e di altri fattori imprevedibili, non può garantire una latenza massima. Quando si progetta la soluzione, è necessario:

* Evitare di fare ipotesi sulla latenza massima di qualsiasi operazione dell'hub IoT.
* Eseguire il provisioning dell'hub IoT nell'area di Azure più vicina ai dispositivi.
* Considerare l'eventualità di usare Azure IoT Edge per eseguire operazioni sensibili alla latenza sul dispositivo o su un gateway vicino al dispositivo.

Più unità dell'hub IoT influiscono sulla limitazione come descritto in precedenza, ma non forniscono alcuna prestazione di latenza aggiuntiva o garanzia.
In caso di incremento imprevisto della latenza dell'operazione, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passaggi successivi
Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-devguide-endpoints]
* [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing messaggi][lnk-devguide-query]
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
