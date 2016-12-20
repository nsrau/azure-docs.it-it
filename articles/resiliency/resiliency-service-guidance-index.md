---
title: Indicazioni sulla resilienza dei servizi | Microsoft Azure
description: "Collegamenti ad articoli sul ripristino di emergenza e indicazioni proattive sulla resilienza e sulla disponibilità per i servizi di Microsoft Azure."
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: 919a197d-ba47-4e49-a64c-118e27d5a7df
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: aafb03c86884d1af3e2cae1134c9e6d7ac42397b
ms.openlocfilehash: 4186211af944c0c0109799a94e4163cfceeee4c4


---
# <a name="microsoft-azure-service-resiliency-guidance"></a>Indicazioni sulla resilienza dei servizi di Microsoft Azure
Microsoft Azure è progettato per offrire all'utente le risorse necessarie quando necessario. Come parte delle procedure consigliate per una progettazione e un funzionamento corretti, è necessario sapere come progettare l'uso dei servizi di Azure per ottenere una disponibilità elevata, oltre a come procedere se l'applicazione è interessata da un'interruzione del servizio. Per semplificare il processo, questo documento include collegamenti a indicazioni sul ripristino di emergenza, oltre a indicazioni sulla progettazione per diversi servizi di Azure.

## <a name="disaster-recovery-guidance"></a>Indicazioni sul ripristino di emergenza
I collegamenti seguenti relativi a indicazioni sul ripristino di emergenza possono offrire le informazioni necessarie per ripristinare rapidamente l'applicazione online in caso di interruzione di un servizio di Azure. Questi collegamenti sono stati creati per contribuire a rispondere alla domanda "Cosa si può fare quando si è interessati dall'interruzione di un servizio di Azure?".

## <a name="design-guidance"></a>Indicazioni per la progettazione
I collegamenti relativi alle indicazioni per la progettazione seguenti sono indicazioni per la progettazione e l'architettura, create per capire come usare il servizio di Azure in modo da massimizzare i tempi di attività dell'applicazione. Questi collegamenti sono stati creati per contribuire a rispondere alla domanda "Come ci si può assicurare che un bug, un errore hardware, un'interruzione del servizio o un altro errore non influisca sulla disponibilità complessiva della mia applicazione?". Se non è presente alcuna indicazione specifica per il servizio ricercato, è possibile che nell'articolo [High availability for applications built on Microsoft Azure](resiliency-high-availability-azure-applications.md) (Disponibilità elevata per applicazioni basate su Microsoft Azure) siano contenute informazioni aggiuntive utili per la progettazione.

## <a name="service-guidance"></a>Indicazioni sul servizio
| Service | Indicazioni sul ripristino di emergenza | Indicazioni per la progettazione |
|:--- |:---:|:---:|
| [Servizi cloud](https://azure.microsoft.com/services/cloud-services/ "Azure Servizi cloud") |[link](../cloud-services/cloud-services-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per i Servizi cloud di Azure") |Non disponibile |
| [Insieme di credenziali di chiave](https://azure.microsoft.com/services/key-vault/ "Azure Insieme di credenziali di chiave") |[link](../key-vault/key-vault-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per l'insieme di credenziali delle chiavi di Azure") |Non disponibile |
| [Archiviazione](https://azure.microsoft.com/services/storage/ "Azure Archiviazione") |[link](../storage/storage-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per l'Archiviazione di Azure") |Non disponibile |
| [Database SQL](https://azure.microsoft.com/services/sql-database/ "Azure Database SQL") |[link](../sql-database/sql-database-disaster-recovery.md "Indicazioni sul ripristino di emergenza per il database SQL di Azure") |[link](../sql-database/sql-database-performance-guidance.md "Indicazioni sulla progettazione di database SQL di Azure") |
| [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/ "Azure Macchine virtuali") |[link](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per Macchine virtuali di Azure") |Non disponibile |
| [Rete virtuale](https://azure.microsoft.com/services/virtual-network/ "Azure Rete virtuale") |[link](../virtual-network/virtual-network-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per Rete virtuale di Azure") |Non disponibile |

## <a name="next-steps"></a>Passaggi successivi
Se si cercano indicazioni incentrate in modo più generico su sistemi e soluzioni, vedere [Disaster recovery and high availability for applications built on Microsoft Azure](https://aka.ms/drtechguide)(Ripristino di emergenza e disponibilità elevata per applicazioni basate su Microsoft Azure).



<!--HONumber=Nov16_HO3-->


