---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87457bb103f49be4ca3e7bf9f463c5bf63f3a119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597819"
---
Per proteggere i dati nelle condivisioni file di Azure da perdite o danneggiamenti di dati, tutte le condivisioni file di Azure archiviano più copie di ogni file durante la scrittura. A seconda dei requisiti del carico di lavoro, è possibile selezionare ulteriori gradi di ridondanza. File di Azure supporta attualmente le opzioni di ridondanza dei dati seguenti:Azure Files currently supports the following data redundancy options:

- **Ridondamento locale:** l'archiviazione con ridondanza locale, spesso definita LRS, significa che ogni file viene archiviato tre volte all'interno di un cluster di archiviazione di Azure.Locally redundant : Locally redundant storage, often referred to LRS, means that every file is stored three times within an Azure storage cluster. Ciò protegge dalla perdita di dati a causa di errori hardware, ad esempio un'unità disco difettosa.
- **Ridondante zona**: L'archiviazione ridondante di zona, spesso indicata come , significa che ogni file viene archiviato tre volte in tre cluster di archiviazione di Azure distinti. I tre cluster di archiviazione di Azure distinti ogni archivio il file tre volte, proprio come con l'archiviazione con ridondanza locale, e sono fisicamente isolati in diverse zone di disponibilità di Azure.The three distinct Azure storage clusters each store the file three times, just like with locally redundant storage, and are physically isolated into different Azure *availability zones.* Le zone di disponibilità sono posizioni fisiche univoche all'interno di un'area di Azure.Availability zones are unique physical locations within an Azure region. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Una scrittura nell'archiviazione non viene accettata finché non viene scritta nei cluster di archiviazione in tutte e tre le zone di disponibilità. 
- **Ridondante geografico:** l'archiviazione con ridondanza geografica, spesso definita GRS, è simile all'archiviazione con ridondanza locale, in quanto un file viene archiviato tre volte all'interno di un cluster di archiviazione di Azure nell'area primaria. Tutte le scritture vengono quindi replicate in modo asincrono in un'area secondaria definita da Microsoft.All writes are then asynchronously replicated to a Microsoft-defined secondary region. L'archiviazione con ridondanza geografica fornisce 6 copie dei dati distribuiti tra due aree di Azure.Geo-redundant storage provides 6 copies of your data spread between two Azure regions. In caso di emergenza grave, ad esempio la perdita permanente di un'area di Azure a causa di un'emergenza naturale o di un altro evento simile, Microsoft eseguirà un failover in modo che il database secondario diventi effettivo primario, servendo tutte le operazioni. Poiché la replica tra le aree primaria e secondaria è asincrona, in caso di emergenza grave, i dati non ancora replicati nell'area secondaria andranno persi. È anche possibile eseguire un failover manuale di un account di archiviazione con ridondanza geografica.
- **Ridondante zona geografica**: L'archiviazione ridondante della zona geografica, spesso indicata come RRS, è come l'archiviazione ridondante di zona, in quanto un file viene archiviato nove volte in 3 cluster di archiviazione distinti nell'area primaria. Tutte le scritture vengono quindi replicate in modo asincrono in un'area secondaria definita da Microsoft.All writes are then asynchronously replicated to a Microsoft-defined secondary region. Il processo di failover per l'archiviazione con ridondanza di zona geografica funziona allo stesso modo di archiviazione con ridondanza geografica.

Le condivisioni file standard di Azure supportano tutti e quattro i tipi di ridondanza, mentre le condivisioni file premium di Azure supportano solo l'archiviazione ridondante locale e l'archiviazione con ridondanza di zona.

Gli account di archiviazione generici versione 2 (GPv2) offrono due opzioni di ridondanza aggiuntive non supportate da File di Azure: archiviazione con ridondanza geografica accessibile in lettura, spesso definita RA-GRS, e archiviazione con ridondanza geografica accessibile, spesso denominato RA-G-RS. È possibile eseguire il provisioning delle condivisioni file di Azure negli account di archiviazione con queste opzioni impostate, tuttavia File di Azure non supporta la lettura dall'area secondaria. Le condivisioni file di Azure distribuite in account di archiviazione ridondanti geo o zona geografica accessibili in lettura verranno fatturati rispettivamente come storage con ridondanza geografica o con ridondanza geografica.