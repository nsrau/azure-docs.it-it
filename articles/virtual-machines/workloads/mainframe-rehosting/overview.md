---
title: Riallocazione del mainframe in macchine virtuali di Azure
description: Riospitare i carichi di lavoro mainframe come i sistemi basati su IBM Z usando macchine virtuali (VM) in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 58755a0ac49b9549813ddb507dfbd986d83dfce5
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305859"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Riallocazione del mainframe in macchine virtuali di Azure

La migrazione dei carichi di lavoro da ambienti mainframe al cloud consente di modernizzare l'infrastruttura e spesso risparmiare sui costi. Molti carichi di lavoro possono essere trasferiti in Azure con semplici modifiche del codice, ad esempio aggiornando i nomi dei database.

In generale, il termine *mainframe* indica un sistema di grandi dimensioni. In particolare, la maggior parte della maggioranza attualmente in uso sono i server IBM System Z o i sistemi compatibili con IBM plug-in che eseguono MVS, DOS, VSE, OS/390 o Z/OS.

Una macchina virtuale (VM) di Azure viene usata per isolare e gestire le risorse per un'applicazione specifica in una singola istanza. A questo scopo, i mainframe come IBM z/OS usano partizioni logiche (LPAR). Un mainframe può usare una LPAR per un'area CICS con programmi COBOL associati e un LPAR separato per il database IBM DB2. Una tipica [applicazione a più livelli in Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) distribuisce le VM di Azure in una rete virtuale che può essere segmentata in subnet per ogni livello.

Le macchine virtuali di Azure possono eseguire ambienti di emulazione mainframe e compilatori che supportano scenari di Lift-and-Shift. Lo sviluppo e il test sono spesso tra i primi carichi di lavoro per eseguire la migrazione da un mainframe a un ambiente di sviluppo/test di Azure. I componenti server comuni che è possibile emulare includono i sistemi OLTP (Online Transaction Process), batch e di inserimento dei dati, come illustrato nella figura seguente.

![Gli ambienti di emulazione in Azure consentono di eseguire sistemi basati su z/OS.](media/01-overview.png)

È possibile eseguire la migrazione di alcuni carichi di lavoro mainframe in Azure con relativa facilità, mentre altri possono essere riallocati in Azure usando una soluzione partner. Per istruzioni dettagliate sulla scelta di una soluzione partner, il [centro migrazione del mainframe di Azure](https://azure.microsoft.com/migration/mainframe/) può essere di aiuto.

## <a name="mainframe-migration"></a>Migrazione dei mainframe

Riallocare, ricompilare, sostituire o ritirare? IaaS o PaaS? Per determinare la strategia di migrazione appropriata per l'applicazione mainframe, vedere la guida alla [migrazione del mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) nell'centro architetture di Azure.

## <a name="micro-focus-rehosting-platform"></a>Piattaforma di riallocazione dello stato attivo micro

Micro Focus Enterprise Server è una delle più grandi piattaforme di riallocazione mainframe disponibili. È possibile usarlo per eseguire i carichi di lavoro z/OS su una piattaforma x86 meno costosa in Azure.

Attività iniziali

- [Installare Enterprise Server e Enterprise Developer in Azure](./microfocus/set-up-micro-focus-azure.md)
- [Configurare CICS BankDemo per Enterprise Developer in Azure](./microfocus/demo.md)
- [Eseguire Enterprise Server in un contenitore Docker in Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame in Azure

TmaxSoft OpenFrame è una diffusa soluzione di riallocazione mainframe utilizzata negli scenari di Lift-and-Shift. Un ambiente OpenFrame in Azure è adatto per lo sviluppo, le demo, i test o i carichi di lavoro di produzione.

Attività iniziali

- [Introduzione a TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Scarica l'eBook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD & T 12,0

Ambiente di sviluppo e test IBM Z (IBM zD & T) configura un ambiente non di produzione in Azure che è possibile usare per lo sviluppo, il test e la demo di applicazioni basate su Z/OS.

L'ambiente di emulazione in Azure può ospitare tipi diversi di istanze Z tramite distribuzioni controllate di sviluppatori di applicazioni (ADCDs). È possibile eseguire zD & T Personal Edition, zD & T Parallel Sysplex e zD & T Enterprise Edition in Azure e Azure Stack.

Attività iniziali

- [Configurare IBM zD & T 12,0 in Azure](./ibm/install-ibm-z-environment.md)
- [Configurare ADCD su zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale in Azure

L'ambiente IBM DB2 pureScale fornisce un cluster di database per Azure. Non è identico all'ambiente originale, ma offre una disponibilità e una scalabilità simili a IBM DB2 per z/OS in esecuzione in un'installazione parallela di Sysplex.

Per iniziare, vedere [IBM DB2 pureScale in Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerazioni

Quando si esegue la migrazione di carichi di lavoro mainframe nell'infrastruttura distribuita come servizio (IaaS) di Azure, è possibile scegliere tra diversi tipi di risorse di calcolo scalabili e su richiesta, incluse le macchine virtuali di Azure. Azure offre una gamma di macchine virtuali [Linux](/azure/virtual-machines/linux/overview) e [Windows](/azure/virtual-machines/windows/overview) .

### <a name="compute"></a>Calcolo

La potenza di calcolo di Azure è paragonabile alla capacità di un mainframe. Se si sta pensando di trasferire un carico di lavoro mainframe in Azure, confrontare la metrica del mainframe di 1 milione istruzioni al secondo (MIPS) per le CPU virtuali. 

Informazioni su come [spostare il calcolo mainframe in Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Disponibilità elevata e failover

Azure offre contratti di servizio (SLA) basati sull'impegno. La disponibilità di più nove è l'impostazione predefinita e i contratti di servizio possono essere ottimizzati con la replica locale o geografica dei servizi. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

Con Azure IaaS, ad esempio una macchina virtuale, funzioni di sistema specifiche forniscono il supporto per il failover, ad esempio le istanze del clustering di failover e i set di disponibilità. Quando si usano risorse della piattaforma distribuita come servizio (PaaS) di Azure, la piattaforma gestisce automaticamente il failover. Gli esempi includono il [database SQL di Azure](/azure/sql-database/sql-database-technical-overview) e [Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Scalabilità

I mainframe supportano in genere la scalabilità verticale, mentre gli ambienti cloud supportano lo scale-out. Azure offre una gamma di dimensioni di [Linux](/azure/virtual-machines/linux/sizes) e [Windows](/azure/virtual-machines/windows/sizes) per soddisfare le tue esigenze. Il cloud viene inoltre ridimensionato in base alle specifiche utente esatte. Calcolo della potenza, dell'archiviazione e dei servizi [scalabile](/azure/architecture/best-practices/auto-scaling) su richiesta con un modello di fatturazione basato sull'utilizzo.

### <a name="storage"></a>Archiviazione

Nel cloud è possibile disporre di una gamma di opzioni di archiviazione flessibili e scalabili e si paga solo per gli elementi necessari. [Archiviazione di Azure](/azure/storage/common/storage-introduction) offre un archivio a scalabilità elevata per oggetti dati, un servizio di file system per il cloud, un archivio di messaggistica affidabile e un archivio NoSQL. Per le macchine virtuali, i dischi gestiti e non gestiti offrono risorse di archiviazione su disco persistenti e sicure.

Informazioni su come [spostare l'archiviazione mainframe in Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Backup e ripristino

La gestione del sito di ripristino di emergenza può essere una proposta costosa. Azure offre opzioni facili da implementare e convenienti per il [backup](/azure/backup/backup-introduction-to-azure-backup), il [ripristino](/azure/site-recovery/site-recovery-overview)e la [ridondanza](/azure/storage/common/storage-redundancy) a livello locale o regionale, oppure tramite la ridondanza geografica.

## <a name="azure-government-for-mainframe-migrations"></a>Azure per enti pubblici per migrazioni mainframe

Molte entità di settore pubblico vogliono spostare le proprie applicazioni mainframe in una piattaforma più moderna e flessibile. Microsoft Azure per enti pubblici è un'istanza separata fisicamente della piattaforma di Microsoft Azure globale, in pacchetto per sistemi governativi federali, statali e locali. Fornisce servizi di sicurezza, protezione e conformità di qualità elevata in modo specifico per Stati Uniti agenzie governative e i loro partner.

Azure per enti pubblici ha ottenuto un'autorità di provisioning per operare (P-ATO) per FedRAMP a un impatto elevato per i sistemi che richiedono questo tipo di ambiente.

Per iniziare, scaricare [Microsoft Azure per enti pubblici cloud per le applicazioni mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Passaggi successivi

Chiedi ai nostri [partner](partner-workloads.md) di aiutarti a eseguire la migrazione o riallocare le tue applicazioni mainframe. Per istruzioni dettagliate sulla scelta di una soluzione per i partner, vedere il sito Web [Platform modernation Alliance](https://www.platformmodernization.org/pages/mainframe.aspx) .

Vedere anche la pagina relativa alla

- [White paper sugli argomenti del mainframe](mainframe-white-papers.md)
- [Migrazione del mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Risoluzione dei problemi](/azure/virtual-machines/troubleshooting/)
- [Demistificazione della migrazione da mainframe ad Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
