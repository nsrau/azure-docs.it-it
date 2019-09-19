---
title: Mapping del contatore di servizi per l'account gratuito di Azure
description: Comprendere il mapping del misuratore di servizi per i servizi inclusi nell'account gratuito.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8022c065d73aafc53d3dcb77e79c3e6320e0ce39
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490396"
---
# <a name="understand-free-service-to-meter-mapping"></a>Comprendere il mapping del misuratore di servizi gratuiti

Tutti i servizi di Azure generano i dati di utilizzo con i misuratori che vengono usati dal sistema di fatturazione per addebitare i servizi all'utente. Per comprendere meglio l'utilizzo dei servizi gratuiti, verrà ora esaminato il mapping del contatore di servizi per i servizi. Per informazioni su come creare i servizi gratuiti, vedere [Creare servizi gratuiti con l'account gratuito di Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-eligible-services"></a>Mapping del contatore di servizi per i servizi idonei

|    Service   | Nome misuratore nel portale di Azure | Nome misuratore nel file di dati di utilizzo/API | ID misuratore |
| ------------ | -------------------------- | -------------------------| -------- |
| VM B1S Linux | Ore di calcolo - VM Standard_B1 | Ore di calcolo - Gratuito | 8260cba2-4437-47d1-a31e-2561cd370f50
| VM B1S Windows | Ore di calcolo - VM Standard_B1 (Windows) | Ore di calcolo - Gratuito | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| VM B1S - Indirizzi IP pubblici  | Ore indirizzo IP - Indirizzi IP pubblici | Ore indirizzo IP - Gratuito | ae56b367-2708-4454-a3d9-2be7b2364ea1
| Cosmos DB | Archiviazione (GB) - Cosmos DB | Archiviazione (GB) - Gratuito | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| Cosmos DB | 100 unità richiesta (ore) - Cosmos DB | 100 unità richiesta (ore) - Gratuito | 5d638a6f-e221-41cf-ae3f-0f81d368cef6
| Archiviazione file | I/O Standard - File (GB) - Ridondanza locale | I/O Standard - File (GB) - Gratuito | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| Archiviazione file | I/O Standard - Unità operazioni lettura file (in decine di migliaia) | I/O Standard - Unità operazioni lettura file (in decine di migliaia) - Gratuito | 6207404d-3389-4d20-9087-cc078ddc3fd9
| Archiviazione file | I/O Standard - Unità operazioni scrittura file (in decine di migliaia) | I/O Standard - Unità operazioni scrittura file (in decine di migliaia) - Gratuito | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| Archiviazione file | I/O Standard - Unità operazioni protocollo file (in decine di migliaia) | I/O Standard - Unità operazioni protocollo file (in decine di migliaia) - Gratuito | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| Archiviazione file | I/O Standard - Unità operazioni elenco file (in decine di migliaia) | I/O Standard - Unità operazioni elenco file (in decine di migliaia) - Gratuito | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Archiviazione BLOB in blocchi per accesso frequente | I/O Standard - Operazioni di lettura BLOB in blocchi per accesso frequente (in decine di migliaia) | I/O Standard - Operazioni di lettura BLOB in blocchi per accesso frequente (in decine di migliaia) - Gratuito |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Archiviazione BLOB in blocchi per accesso frequente | I/O Standard - BLOB in blocchi per accesso frequente (GB) - Ridondanza locale | I/O Standard - BLOB in blocchi per accesso frequente (GB) - Gratuito | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Archiviazione BLOB in blocchi per accesso frequente | I/O Standard - Operazioni di scrittura BLOB in blocchi per accesso frequente (in decine di migliaia) | I/O Standard - Operazioni di scrittura BLOB in blocchi per accesso frequente (in decine di migliaia) - Gratuito | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Archiviazione BLOB in blocchi per accesso frequente  | I/O Standard - Operazioni di scrittura/elenco BLOB in blocchi per accesso frequente (in decine di migliaia) | I/O Standard - Operazioni di scrittura/elenco BLOB in blocchi per accesso frequente (in decine di migliaia) - Gratuito | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Disco gestito <sup>1</sup>  | Disco gestito/Snapshot Standard (GB) - Ridondanza locale | Disco gestito/Snapshot Standard (GB) - Gratuito | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Disco gestito <sup>1</sup>  | Operazioni disco gestito Standard (in decine di migliaia) | Operazioni disco gestito Standard (in decine di migliaia) - Gratuito | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Disco gestito <sup>1</sup>  | Archiviazione Premium - BLOB di pagine/P6 (unità) - Ridondanza locale | Archiviazione Premium - BLOB di pagine/P6 (unità) - Gratuito | 2b98c168-27ca-4cc1-b509-e887dec87657
| Database SQL | Giorni di database S0 Standard - Database SQL | Giorni di database S0 Standard - Gratuito | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Condiviso - Larghezza di banda <sup>2</sup> | Trasferimento dati in uscita (GB) | Trasferimento dati in uscita (GB) - Gratuito | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

<sup>1</sup> Se si crea una macchina virtuale Windows e si sceglie il disco gestito, il contatore del disco gestito verrà utilizzato come parte della macchina virtuale.

<sup>2</sup> I contatori condivisi possono essere utilizzati con più servizi. Ad esempio, sia le macchine virtuali che l'archiviazione generano i dati di utilizzo con il misuratore Trasferimento dati in uscita (GB).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Aggiornare la sottoscrizione](billing-upgrade-azure-subscription.md)
