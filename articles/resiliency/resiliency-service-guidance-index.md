<properties
   pageTitle="Indicazioni sulla resilienza dei servizi | Microsoft Azure"
   description="Collegamenti ad articoli sul ripristino di emergenza e indicazioni proattive sulla resilienza e sulla disponibilità per i servizi di Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

# Indicazioni sulla resilienza dei servizi di Microsoft Azure
Microsoft Azure è progettato per offrire all'utente le risorse necessarie quando necessario. Come parte delle procedure consigliate per una progettazione e un funzionamento corretti, è necessario sapere come progettare l'uso dei servizi di Azure per ottenere una disponibilità elevata, oltre a come procedere se l'applicazione è interessata da un'interruzione del servizio. Per semplificare il processo, questo documento include collegamenti a indicazioni sul ripristino di emergenza, oltre a indicazioni sulla progettazione per diversi servizi di Azure.

##Indicazioni sul ripristino di emergenza
I collegamenti seguenti relativi a indicazioni sul ripristino di emergenza possono offrire le informazioni necessarie per ripristinare rapidamente l'applicazione online in caso di interruzione di un servizio di Azure. Questi collegamenti sono stati creati per contribuire a rispondere alla domanda "Cosa si può fare quando si è interessati dall'interruzione di un servizio di Azure?".

##Indicazioni per la progettazione
I collegamenti relativi alle indicazioni per la progettazione seguenti sono indicazioni per la progettazione e l'architettura, create per capire come usare il servizio di Azure in modo da massimizzare i tempi di attività dell'applicazione. Questi collegamenti sono stati creati per contribuire a rispondere alla domanda "Come ci si può assicurare che un bug, un errore hardware, un'interruzione del servizio o un altro errore non influisca sulla disponibilità complessiva della mia applicazione?". Se non è presente alcuna indicazione specifica per il servizio ricercato, è possibile che nell'articolo [High availability for applications built on Microsoft Azure](./resiliency-high-availability-azure-applications.md) (Disponibilità elevata per applicazioni basate su Microsoft Azure) siano contenute informazioni aggiuntive utili per la progettazione.

##Indicazioni sul servizio
| Service | Indicazioni sul ripristino di emergenza | Indicazioni per la progettazione |
|:---------|:--------------------------:|:------------------:|
| [Servizi cloud](https://azure.microsoft.com/services/cloud-services/ "Servizi cloud di Azure") | [link](../cloud-services/cloud-services-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per i Servizi cloud di Azure") | Non disponibile |
| [Insieme di credenziali di chiave](https://azure.microsoft.com/services/key-vault/ "Insieme di credenziali chiave Azure") | [link](../key-vault/key-vault-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per l'insieme di credenziali delle chiavi di Azure") | Non disponibile |
| [Archiviazione](https://azure.microsoft.com/services/storage/ "Archiviazione di Azure") | [link](../storage/storage-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per l'Archiviazione di Azure") | Non disponibile |
| [Database SQL](https://azure.microsoft.com/services/sql-database/ "Database SQL di Azure") | [link](../sql-database/sql-database-disaster-recovery.md "Indicazioni sul ripristino di emergenza per il database SQL di Azure") | [link](../sql-database/sql-database-business-continuity-design.md "Indicazioni sulla progettazione di database SQL di Azure") |
| [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/ "Macchine virtuali di Azure") | [link](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per Macchine virtuali di Azure") | Non disponibile |
| [Rete virtuale](https://azure.microsoft.com/services/virtual-network/ "Rete virtuale di Azure") | [link](../virtual-network/virtual-network-disaster-recovery-guidance.md "Indicazioni sul ripristino di emergenza per Rete virtuale di Azure") | Non disponibile |

##Passaggi successivi
Se si cercano indicazioni incentrate in modo più generico su sistemi e soluzioni, vedere [Disaster recovery and high availability for applications built on Microsoft Azure](https://aka.ms/drtechguide) (Ripristino di emergenza e disponibilità elevata per applicazioni basate su Microsoft Azure).

<!---HONumber=AcomDC_0824_2016-->