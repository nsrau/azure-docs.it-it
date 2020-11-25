---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef9efe389894af7c792e980922ca422e9d05929b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026767"
---
La tabella seguente descrive i limiti predefiniti per Azure per gli account di archiviazione di Azure per utilizzo generico V1, V2, archiviazione BLOB e archiviazione BLOB in blocchi. Il limite *in ingresso* indica tutti i dati inviati a un account di archiviazione. Il limite *in uscita* indica tutti i dati ricevuti da un account di archiviazione.

> [!NOTE]
> È possibile richiedere una maggiore capacità e limiti più alti per i dati in ingresso. Per richiedere un aumento, contattare il [supporto di Azure](https://azure.microsoft.com/support/faq/).

| Risorsa | Limite |
| --- | --- |
| Numero di account di archiviazione per ogni area per ogni sottoscrizione, inclusi gli account di archiviazione Standard e Premium.| 250 |
| Capacità massima dell'account di archiviazione | 5 PiB <sup>1</sup>|
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima delle richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (per le aree Stati Uniti ed Europa) | 10 Gbps |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (per le aree diverse da Stati Uniti ed Europa) | 5 Gbps se è abilitata l'archiviazione RA-GRS/GRS, 10 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v2 e BLOB (per tutte le aree) | 50 Gbps |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v1 (per le aree degli Stati Uniti) | 20 Gbps se è abilitata l'archiviazione RA-GRS/GRS, 30 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v1 (per le aree diverse dagli Stati Uniti) | 10 Gbps se è abilitata l'archiviazione RA-GRS/GRS, 15 Gbps per LRS/ZRS<sup>2</sup> |
| Numero massimo di regole di rete virtuale per account di archiviazione | 200 |
| Numero massimo di regole di indirizzo IP per account di archiviazione | 200 |

<sup>1</sup> Gli account standard di Archiviazione di Azure supportano limiti di capacità superiori e limiti più elevati per il traffico in ingresso in base alle richieste. Per richiedere un incremento dei limiti di archiviazione, contattare il [supporto di Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Se per l'account di archiviazione è abilitata l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS), le destinazioni in uscita per la posizione secondaria sono identiche a quelle della posizione primaria. Per altre informazioni, vedere [Replica di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md).

> [!NOTE]
> Per la maggior parte degli scenari è consigliabile usare un account di archiviazione per utilizzo generico v2. È possibile eseguire facilmente l'aggiornamento di un account di archiviazione per utilizzo generico v1 o un account di archiviazione BLOB di Azure a un account per utilizzo generico v2, senza tempi di inattività e senza la necessità copiare i dati. Per altre informazioni, [Eseguire l'aggiornamento alla versione 2 di un account di archiviazione per uso generico](../articles/storage/common/storage-account-upgrade.md).

Tutti gli account di archiviazione vengono eseguiti in una topologia di rete flat indipendentemente da quando sono stati creati. Per altre informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets).