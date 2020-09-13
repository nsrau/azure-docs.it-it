---
title: Cenni preliminari sui criteri di conservazione dei dati-Azure SQL Edge (anteprima)
description: Informazioni sui criteri di conservazione dei dati in Azure SQL Edge (anteprima)
keywords: SQL Edge, conservazione dei dati
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 3649d4f77e5b57ab14accacd87fbaa867ba2742f
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89550659"
---
# <a name="data-retention-policy-overview"></a>Cenni preliminari sui criteri di conservazione dei dati

La raccolta e l'archiviazione di dati da dispositivi Internet connessi è importante per ottenere informazioni operative e aziendali. Tuttavia, dato il volume di dati provenienti da questi dispositivi, diventa importante per le organizzazioni pianificare con attenzione la quantità di dati che si desidera mantenere e la granularità. Sebbene sia consigliabile mantenere tutti i dati a ogni granularità, non è sempre pratico. Inoltre, il volume di dati che è possibile conservare è vincolato dalla quantità di spazio di archiviazione disponibile nei dispositivi Internet o Internet. 

Azure SQL Edge (anteprima) CTP 2.3 aggiunge una nuova funzionalità che consente agli amministratori di database di definire i criteri di conservazione dei dati in un database Edge SQL e nelle tabelle sottostanti. Una volta definiti i criteri di conservazione dei dati, viene eseguita un'attività di sistema in background per eliminare tutti i dati obsoleti dalle tabelle utente. 

> [!Note]
> I dati eliminati dalla tabella non sono recuperabili. L'unico modo possibile per recuperare i dati eliminati consiste nel ripristinare il database da un backup precedente.

Argomenti di avvio rapido:

- [Abilitare e disabilitare i criteri di conservazione dei dati](data-retention-enable-disable.md)
- [Gestire i dati cronologici con i criteri di conservazione](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>Funzionamento della conservazione dei dati

Per configurare la conservazione dei dati, è possibile utilizzare istruzioni DDL. Per ulteriori informazioni, [abilitare e disabilitare i criteri di conservazione dei dati](data-retention-enable-disable.md). Per l'eliminazione automatica dei record obsoleti, è innanzitutto necessario abilitare la conservazione dei dati sia per il database che per le tabelle che si desidera eliminare all'interno del database. 

Una volta configurata la conservazione dei dati per una tabella, viene eseguita un'attività in background per identificare i record obsoleti in una tabella ed eliminare tali record. Se per qualche motivo la pulizia automatica delle attività non è in esecuzione o non è in grado di gestire le eliminazioni, è possibile eseguire un'operazione di pulizia manuale su queste tabelle. Per altre informazioni sulle pulizie automatiche e manuali, vedere [pulizia automatica e manuale](data-retention-cleanup.md).

## <a name="limitations-and-restrictions"></a>Limitazioni e restrizioni

- La conservazione dei dati, se abilitata, viene disabilitata automaticamente quando il database viene ripristinato da un backup completo o viene ricollegato. 
- Non è possibile abilitare la conservazione dei dati per una tabella di cronologia temporale

## <a name="next-steps"></a>Passaggi successivi

- [Machine Learning e intelligenza artificiale con ONNX in SQL Edge](onnx-overview.md).
- [Creazione di una soluzione IoT end-to-end con SQL Edge usando IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming dei dati in Azure SQL Edge](stream-data.md)
