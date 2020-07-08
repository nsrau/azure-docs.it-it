---
title: includere il file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597819"
---
Per proteggere i dati nelle condivisioni file di Azure in caso di perdita o danneggiamento dei dati, tutte le condivisioni file di Azure archiviano più copie di ogni file man mano che vengono scritte. A seconda dei requisiti del carico di lavoro, è possibile selezionare altri gradi di ridondanza. File di Azure supporta attualmente le seguenti opzioni di ridondanza dei dati:

- Con **ridondanza locale**: l'archiviazione con ridondanza locale, spesso denominata con ridondanza locale, indica che ogni file è archiviato per tre volte all'interno di un cluster di archiviazione di Azure. Questa operazione protegge dalla perdita di dati a causa di errori hardware, ad esempio un'unità disco non valida.
- Con **ridondanza**della zona: l'archiviazione con ridondanza della zona, spesso denominata ZRS, indica che ogni file viene archiviato tre volte in tre diversi cluster di archiviazione di Azure. I tre cluster distinti di archiviazione di Azure archiviano il file tre volte, come per l'archiviazione con ridondanza locale, e sono fisicamente isolati in *zone di disponibilità*di Azure diverse. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Una scrittura nello spazio di archiviazione non viene accettata finché non viene scritta nei cluster di archiviazione in tutte e tre le zone di disponibilità. 
- Con **ridondanza geografica**: l'archiviazione con ridondanza geografica, spesso denominata GRS, è analoga all'archiviazione con ridondanza locale, in quanto un file viene archiviato tre volte all'interno di un cluster di archiviazione di Azure nell'area primaria. Tutte le scritture vengono quindi replicate in modo asincrono in un'area secondaria definita da Microsoft. L'archiviazione con ridondanza geografica offre 6 copie dei dati distribuiti tra due aree di Azure. In caso di emergenza grave, ad esempio la perdita permanente di un'area di Azure a causa di un'emergenza naturale o di un altro evento analogo, Microsoft eseguirà un failover in modo che il database secondario diventi attivo come primario, servendo tutte le operazioni. Poiché la replica tra le aree primaria e secondaria è asincrona, in caso di emergenza grave, i dati non ancora replicati nell'area secondaria andranno perduti. È anche possibile eseguire un failover manuale di un account di archiviazione con ridondanza geografica.
- **Ridondanza della zona geografica**: l'archiviazione con ridondanza della zona geografica, spesso denominata GZRS, è analoga all'archiviazione con ridondanza della zona, perché un file viene archiviato nove volte in 3 cluster di archiviazione distinti nell'area primaria. Tutte le scritture vengono quindi replicate in modo asincrono in un'area secondaria definita da Microsoft. Il processo di failover per l'archiviazione con ridondanza della zona geografica funziona come per l'archiviazione con ridondanza geografica.

Le condivisioni file di Azure standard supportano tutti e quattro i tipi di ridondanza, mentre le condivisioni file di Azure Premium supportano solo l'archiviazione con ridondanza locale e la zona

Gli account di archiviazione per utilizzo generico versione 2 (GPv2) forniscono due opzioni di ridondanza aggiuntive che non sono supportate da File di Azure: archiviazione con ridondanza geografica e accesso in lettura, spesso definita RA-GRS, e archiviazione con ridondanza geografica e accesso in lettura, spesso denominata RA-GZRS. È possibile eseguire il provisioning di condivisioni file di Azure negli account di archiviazione con queste opzioni impostate, tuttavia File di Azure non supporta la lettura dall'area secondaria. Le condivisioni file di Azure distribuite in account di archiviazione con ridondanza geografica o geografica con accesso in lettura saranno fatturate rispettivamente come archiviazione con ridondanza geografica o con ridondanza della zona geografica.