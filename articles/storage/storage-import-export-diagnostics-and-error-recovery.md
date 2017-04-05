---
title: Diagnostica e ripristino dagli errori per i processi di Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come abilitare la registrazione dettagliata per i processi del servizio Importazione/Esportazione di Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 0068aae9d6780aa41a070db0eb191d0d5a165d21
ms.lasthandoff: 03/30/2017


---

# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnostica e ripristino dagli errori per i processi di Importazione/Esportazione di Azure
Per ogni unità elaborata, il servizio Importazione/Esportazione di Azure crea un log degli errori nell'account di archiviazione associato. È anche possibile abilitare la registrazione dettagliata impostando la proprietà `LogLevel` su `Verbose` quando si chiamano le operazioni [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) o [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update).

 Per impostazione predefinita, i log vengono scritti in un contenitore denominato `waimportexport`. È possibile specificare un nome diverso impostando la proprietà `DiagnosticsPath` quando si chiamano le operazioni `Put Job` or `Update Job Properties`. I log vengono archiviati come BLOB in blocchi con la convenzione di denominazione seguente: `waies/jobname_driveid_timestamp_logtype.xml`.

 È possibile recuperare l'URI dei log per un processo chiamando l'operazione [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). L'URI per il log dettagliato viene restituito nella proprietà `VerboseLogUri` per ogni unità, mentre l'URI per il log degli errori viene restituito nella proprietà `ErrorLogUri`.

È possibile usare i dati di registrazione per identificare i problemi seguenti.

## <a name="drive-errors"></a>Errori delle unità

Gli elementi seguenti sono classificati come errori delle unità:

-   Errori di accesso o di lettura del file manifesto

-   Chiavi BitLocker non corrette

-   Errori di lettura/scrittura delle unità

## <a name="blob-errors"></a>Errori del BLOB

Gli elementi seguenti sono classificati come errori del BLOB:

-   BLOB o nomi non corretti o in conflitto

-   File mancanti

-   BLOB non trovato

-   File troncati (i file sul disco sono più piccoli di quanto specificato nel manifesto)

-   Contenuto dei file danneggiato (per i processi di importazione, rilevato con un checksum MD5 non corrispondente)

-   File di metadati e proprietà BLOB danneggiati (rilevati con un checksum MD5 non corrispondente)

-   Schema non corretto per i file di metadati e/o proprietà BLOB

Può capitare che alcune parti di un processo di importazione o esportazione non vengano completate correttamente, anche se l'intero processo viene completato. In questo caso, è possibile caricare o scaricare in rete le parti mancanti dei dati oppure è possibile creare un nuovo processo per trasferire i dati. Per informazioni su come ripristinare i dati in rete, vedere [Azure Import/Export Tool Reference](storage-import-export-tool-how-to-v1.md) (Informazioni di riferimento sullo strumento Importazione/Esportazione di Azure).

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)

