---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/27/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e02de4ee9c36f9c3f0c27bc02ee1c37ce3e4057c
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78208219"
---
La tabella seguente descrive i limiti predefiniti per gli account di archiviazione BLOB V1, V2 e per utilizzo generico di Azure. Il limite di *ingresso* si riferisce a tutti i dati inviati a un account di archiviazione. Il limite di *uscita* si riferisce a tutti i dati ricevuti da un account di archiviazione.

| Resource | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per ogni area per ogni sottoscrizione, inclusi gli account sia Standard che Premium | 250 |
| Capacità massima dell'account di archiviazione | 5 PiB <sup>1</sup>|
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima di richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Ingresso massimo<sup>1</sup> per account di archiviazione (aree Stati Uniti, Europa) | 25 Gbps |
| Ingresso massimo<sup>1</sup> per account di archiviazione (aree diverse da Stati Uniti ed Europa) | 5 Gbps se RA-GRS/GRS è abilitata, 10 Gbps per con ridondanza locale/ZRS<sup>2</sup> |
| Numero massimo in uscita per gli account di archiviazione BLOB e V2 per utilizzo generico (tutte le aree) | 50 Gbps |
| Numero massimo in uscita per gli account di archiviazione per utilizzo generico V1 (aree degli Stati Uniti) | 20 Gbps se RA-GRS/GRS è abilitata, 30 Gbps per con ridondanza locale/ZRS<sup>2</sup> |
| Numero massimo in uscita per gli account di archiviazione per utilizzo generico V1 (aree non statunitensi) | 10 Gbps se RA-GRS/GRS è abilitato, 15 Gbps per con ridondanza locale/ZRS<sup>2</sup> |
| Numero massimo di regole di rete virtuale per account di archiviazione | 200 |
| Numero massimo di regole di indirizzi IP per account di archiviazione | 200 |

<sup>1</sup> Gli account standard di archiviazione di Azure supportano limiti di capacità superiori e limiti più elevati per il traffico in ingresso per richiesta. Per richiedere un incremento dei limiti di archiviazione per il traffico in ingresso, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> se per l'account di archiviazione è abilitato l'accesso in lettura con archiviazione con ridondanza geografica (RA-GRS) o archiviazione con ridondanza della zona geografica (RA-GZRS), le destinazioni in uscita per la posizione secondaria sono identiche a quelle della posizione primaria. Le opzioni di [replica di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) includono:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> Microsoft consiglia di utilizzare un account di archiviazione per utilizzo generico V2 per la maggior parte degli scenari. È possibile aggiornare facilmente un account di archiviazione BLOB di Azure o di uso generico V1 a un account per utilizzo generico V2 senza tempi di inattività e senza la necessità di copiare i dati. Per altre informazioni, vedere [eseguire l'aggiornamento a un account di archiviazione per utilizzo generico V2](../articles/storage/common/storage-account-upgrade.md).

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione. Quindi partizionare gli oggetti dati tra tali account di archiviazione. Per informazioni sui prezzi dei volumi, vedere [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Tutti gli account di archiviazione vengono eseguiti su una topologia di rete flat e supportano gli obiettivi di scalabilità e prestazioni descritti in questo articolo, indipendentemente dal momento in cui sono stati creati. Per ulteriori informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
