---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b7c097306ba46d4f0024aecc55994508e2d8a090
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011721"
---
Per proteggere i dati presenti nelle condivisioni file di Azure da perdita o danneggiamento, tutte le condivisioni file di Azure archiviano più copie dei file quando vengono scritti. A seconda dei requisiti del carico di lavoro, è possibile selezionare altri livelli di ridondanza. File di Azure supporta attualmente le opzioni di ridondanza dei dati seguenti:

- **Con ridondanza locale**: con l'archiviazione con ridondanza locale ogni file viene archiviato tre volte all'interno di un cluster di archiviazione di Azure. Questa operazione consente di proteggere da perdite di dati causate da errori hardware, ad esempio nel caso di un'unità disco danneggiata.
- **Con ridondanza della zona**: con l'archiviazione con ridondanza della zona ogni file viene archiviato tre volte in tre diversi cluster di archiviazione di Azure. Analogamente a quanto avviene con l'archiviazione con ridondanza locale, anche con la ridondanza della zona vengono archiviate tre copie di ogni file, ma tali copie sono fisicamente isolate in tre cluster di archiviazione distinti in *zone di disponibilità* diverse di Azure. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Una scrittura nello spazio di archiviazione non viene accettata finché non viene scritta nei cluster di archiviazione in tutte e tre le zone di disponibilità. 
- **Con ridondanza geografica**: l'archiviazione con ridondanza geografica è simile all'archiviazione con ridondanza locale, in cui un file viene archiviato tre volte all'interno di un cluster di archiviazione di Azure nell'area primaria. Tutte le scritture vengono replicate in modo asincrono in un'area secondaria definita da Microsoft. Con l'archiviazione con ridondanza geografica vengono conservate sei copie dei dati distribuite tra due aree di Azure. In caso di emergenza grave, ad esempio la perdita definitiva di un'area di Azure a causa di un disastro naturale o di un altro evento analogo, Microsoft eseguirà un failover in modo che l'area secondaria diventi primaria e gestisca tutte le operazioni. Dal momento che la replica tra le aree primaria e secondaria è asincrona, in caso di emergenza grave, i dati che sono stati ancora replicati nell'area secondaria andranno persi. È anche possibile eseguire un failover manuale di un account di archiviazione con ridondanza geografica.
- **Con ridondanza geografica della zona**: l'archiviazione con ridondanza geografica della zona è simile all'archiviazione con ridondanza della zona, in cui un file viene archiviato tre volte in tre distinti cluster di archiviazione nell'area primaria. Tutte le scritture vengono replicate in modo asincrono in un'area secondaria definita da Microsoft. Il funzionamento del processo di failover per l'archiviazione con ridondanza della zona geografica è analogo a quello dell'archiviazione con ridondanza geografica.

Le condivisioni file di Azure Standard supportano tutti e quattro i tipi di ridondanza, mentre le condivisioni file di Azure Premium supportano solo l'archiviazione con ridondanza locale e quella con ridondanza della zona.

Gli account di archiviazione per utilizzo generico versione 2 (GPv2) offrono due opzioni di ridondanza aggiuntivi non supportati da File di Azure, ovvero archiviazione con ridondanza geografica e accesso in lettura e archiviazione con ridondanza geografica della zona e accesso in lettura. È possibile effettuare il provisioning di condivisioni file di Azure in account di archiviazione con queste opzioni impostate, ma File di Azure non supporta la lettura dall'area secondaria. Le condivisioni file di Azure distribuite in account di archiviazione con ridondanza geografica o geografica della zona e accesso in lettura verranno fatturate rispettivamente come archiviazione con ridondanza geografica e archiviazione con ridondanza geografica della zona.