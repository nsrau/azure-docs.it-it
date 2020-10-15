---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 007c19a10db5e000770c8c80189453d4a80edec2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204500"
---
Nella tabella seguente sono elencati i limiti associati ai diversi livelli di servizio, ovvero S1, S2, S3 e F1. Per informazioni sul costo delle singole *unità* in ogni livello, vedere [Prezzi dell'hub IoT di Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

| Risorsa | S1 Standard | S2 Standard | S3 Standard | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Messaggi al giorno |400.000 |6\.000.000 |300.000.000 |8\.000 |
| Unità massime |200 |200 |10 |1 |

> [!NOTE]
> Se si prevede l'uso di più di 200 unità con un hub di livello S1, S2 o con 10 unità con livello S3, contattare il supporto tecnico Microsoft.
> 
> 

Nella tabella seguente sono elencate le limitazioni che si applicano alle risorse dell'hub IoT.

| Risorsa | Limite |
| --- | --- |
| Numero massimo hub IoT a pagamento per ogni sottoscrizione di Azure |50 |
| Numero massimo hub IoT gratuiti per ogni sottoscrizione di Azure |1 |
| Numero massimo di caratteri in un ID dispositivo | 128 |
| Numero massimo di identità del dispositivo<br/>  restituito in una singola chiamata |1\.000 |
| Conservazione massima nell'hub IoT per i messaggi dal dispositivo al cloud |7 giorni |
| Dimensione massima del messaggio del dispositivo al cloud |256 KB |
| Dimensione massima del batch del dispositivo al cloud |AMQP e HTTP: 256 kB per l'intero batch <br/>MQTT: 256 kB per ogni messaggio |
| Messaggi massimi nel batch del dispositivo al cloud |500 |
| Dimensione massima del messaggio del cloud al dispositivo |64 KB |
| TTL massima per i messaggi del cloud al dispositivo |2 giorni |
| Numero massimo di recapiti per i messaggi  <br/> del cloud al dispositivo |100 |
| Profondità massima della coda da cloud a dispositivo per dispositivo |50 |
| Numero massimo di recapiti per i messaggi di feedback  <br/>  in risposta a un messaggio del cloud al dispositivo |100 |
| TTL massimo per i messaggi di feedback  <br/>  in risposta a un messaggio del cloud al dispositivo |2 giorni |
| [Dimensione massima del dispositivo gemello](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB per la sezione dei tag e 32 KB per ognuna delle sezioni delle proprietà desiderate e segnalate |
| Lunghezza massima della chiave della stringa del dispositivo gemello | 1 KB |
| Lunghezza massima del valore della stringa del dispositivo gemello | 4 KB |
| [Profondità massima dell'oggetto nel dispositivo gemello](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Dimensione massima del payload del metodo diretto | 128 KB |
| Periodo di conservazione massimo della cronologia del processo | 30 giorni |
| Numero massimo di processi simultanei | 10 (per S3), 5 per (S2), 1 ( per S1) |
| Numero massimo di endpoint aggiuntivi | 10 (per S1, S2 e S3) |
| Numero massimo di regole di routing dei messaggi | 100 (per S1, S2 e S3) |
| Numero massimo di flussi dispositivo connessi contemporaneamente | 50 (per S1, S2, S3 e solo F1) |
| Trasferimento dati massimo di flussi dispositivo | 300 MB al giorno (per S1, S2, S3 e solo F1) |

> [!NOTE]
> Se sono necessari più di 50 hub IoT a pagamento in una sottoscrizione di Azure, contattare il supporto tecnico Microsoft.

> [!NOTE]
> Il numero totale di dispositivi e moduli che è attualmente possibile registrare in un singolo hub IoT è pari a 1.000.000. Per aumentare questo limite, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

L'hub IoT limita le richieste quando vengono superate le quote seguenti.

| Limitazione | Valore per ogni hub |
| --- | --- |
| Operazioni del registro delle identità <br/> (creazione, recupero, elenco, aggiornamento ed eliminazione) <br/>  importazione/esportazione singola o in blocco |83,33/sec/unità (5.000/min/unità) (per S3). <br/> 1.67/sec/unità (100/min/unità) (per S1 e S2). |
| Connessioni del dispositivo |6\.000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo di 100/sec. |
| Inoltri dal dispositivo al cloud |6\.000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo di 100/sec. |
| Inoltri dal cloud al dispositivo | 83,33/sec/unità (5.000/min/unità) (per S3), 1,67/sec/unità (100/min/unità) (per S1 e S2). |
| Ricezioni dal cloud al dispositivo |833,33/sec/unità (50.000/min/unità) (per S3), 16,67/sec/unità (1.000/min/unità) (per S1 e S2). |
| Operazioni di caricamento file |83,33 avvii di caricamento file/sec/unità (5.000/min/unità) (per S3), 1,67 avvii di caricamento file/sec/unità (100/min/unità) (per S1 e S2). <br/> Per un account di archiviazione di Azure possono essere generati contemporaneamente 10.000 URI di firma di accesso condiviso.<br/>  10 URI di firma di accesso condiviso/dispositivo possono essere generati contemporaneamente. |
| Metodi diretti | 24 MB/sec/unità (per S3), 480 KB/sec/unità (per S2), 160 KB/sec/unità (per S1).<br/> In base a una dimensione del contatore delle limitazioni di 8 KB. |
| Letture del dispositivo gemello | 500/sec/unità (per S3), un massimo di 100/sec o di 10/sec/unità (per S2), 100/sec (per S1) |
| Aggiornamenti dei dispositivi gemelli | 250/sec/unità (per S3), un massimo di 50/sec o di 5/sec/unità (per S2), 50/sec (per S1) |
| Operazioni dei processi <br/> (creazione, aggiornamento, elenco ed eliminazione) | 83,33/sec/unità (5.000/min/unità) (per S3), 1,67/sec/unità (100/min/unità) (per S2), 1,67/sec/unità (100/min/unità) (per S1). |
| Velocità effettiva delle operazioni dei processi per dispositivo | 50/sec/unità (per S3), un massimo di 10/sec o di 1/sec/unità (per S2), 10/sec (per S1). |
| Velocità di avvio del flusso dispositivo | 5 nuovi flussi/sec (solo per S1, S2, S3 e F1). |
