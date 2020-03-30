---
title: Rehosting del frame principale nelle macchine virtuali di AzureMainframe rehosting on Azure virtual machines
description: Riospitare i carichi di lavoro mainframe, ad esempio i sistemi basati su IBM, usando macchine virtuali (VM) in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289799"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Rehosting del frame principale nelle macchine virtuali di AzureMainframe rehosting on Azure virtual machines

La migrazione dei carichi di lavoro dagli ambienti mainframe al cloud consente di modernizzare l'infrastruttura e spesso di risparmiare sui costi. Molti carichi di lavoro possono essere trasferiti in Azure con semplici modifiche del codice, ad esempio aggiornando i nomi dei database.

In generale, il termine *mainframe* significa un grande sistema informatico. In particolare, la stragrande maggioranza attualmente in uso sono i server IBM System o i sistemi IBM compatibili con i plug-system che eseguono MVS, DOS, VSE, OS/390 o z/OS.

Una macchina virtuale di Azure (VM) viene usata per isolare e gestire le risorse per un'applicazione specifica in una singola istanza. I mainframe come IBM z/OS utilizzano le partizioni logiche (LPARS) a questo scopo. Un mainframe potrebbe utilizzare un LPAR per un'area CICS con programmi COBOL associati e un LPAR separato per il database IBM Db2. Una tipica [applicazione a più livelli in Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) distribuisce le macchine virtuali di Azure in una rete virtuale che può essere segmentata in subnet per ogni livello.

Le macchine virtuali di Azure possono eseguire ambienti di emulazione mainframe e compilatori che supportano scenari di passaggio e spostamento. Sviluppo e test sono spesso tra i primi carichi di lavoro di cui eseguire la migrazione da un mainframe a un ambiente di sviluppo/test di Azure.Development and testing are often among the first workloads to migrate from a mainframe to an Azure dev/test environment. I componenti server comuni che è possibile emulare includono sistemi di ingestione di batch e di inserimento dati (OLTP), batch e dati, come illustrato nella figura seguente.

![Gli ambienti di emulazione in Azure consentono di eseguire sistemi basati su z/OS.](media/01-overview.png)

Alcuni carichi di lavoro mainframe possono essere migrati in Azure con relativa facilità, mentre altri possono essere ospitati nuovamente in Azure usando una soluzione partner. Per indicazioni dettagliate sulla scelta di una soluzione partner, il Centro migrazione [mainframe](https://azure.microsoft.com/migration/mainframe/) di Azure può essere utile.

## <a name="mainframe-migration"></a>Migrazione dei mainframe

Riospitare, ricostruire, sostituire o ritirarsi? Soluzioni IaaS o PaaS? Per determinare la strategia di migrazione corretta per l'applicazione mainframe, vedere la guida alla migrazione di mainframe in Azure Architecture Center.To determine the right migration strategy for your mainframe application, see the [Mainframe migration](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) guide in the Azure Architecture Center.

## <a name="micro-focus-rehosting-platform"></a>Piattaforma di rehosting Micro Focus

Micro Focus Enterprise Server è una delle più grandi piattaforme di rehosting mainframe disponibili. È possibile usarlo per eseguire i carichi di lavoro z/OS su una piattaforma x86 meno costosa in Azure.You can use it run your z/OS workloads on a less expensive x86 platform on Azure.

Attività iniziali

- [Installare Enterprise Server e Enterprise Developer in Azure](./microfocus/set-up-micro-focus-azure.md)
- [Configurare CICS BankDemo per Enterprise Developer in AzureSet up CICS BankDemo for Enterprise Developer on Azure](./microfocus/demo.md)
- [Eseguire Enterprise Server in un contenitore Docker in AzureRun Enterprise Server in a Docker Container on Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame in Azure

TmaxSoft OpenFrame è una popolare soluzione di rehosting mainframe utilizzata in scenari lift-and-shift. Un ambiente OpenFrame in Azure è adatto per carichi di lavoro di sviluppo, demo, test o produzione.

Attività iniziali

- [Introduzione a TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Scarica l'ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM, Development and Test Environment (IBM zD&T) configura un ambiente non di produzione in Azure che è possibile usare per lo sviluppo, i test e le demo delle applicazioni basate su z/OS.

L'ambiente di emulazione in Azure può ospitare diversi tipi di istanze di , tramite distribuzioni controllate (ADAD) degli sviluppatori di applicazioni. È possibile eseguire zD&T Personal Edition, zD&T Parallel Sysplex e zD&T Enterprise Edition in Azure e Azure Stack.You can run zD&T Personal Edition, zD&T Parallel Sysplex, and zD&T Enterprise Edition on Azure and Azure Stack.

Attività iniziali

- [Configurare IBM zD&T 12.0 in AzureSet up IBM zD&T 12.0 on Azure](./ibm/install-ibm-z-environment.md)
- [Configurare ADCD su zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale in Azure

L'ambiente IBM DB2 pureScale fornisce un cluster di database per Azure.The IBM DB2 pureScale environment provides a database cluster for Azure. Non è identico all'ambiente originale, ma offre disponibilità e scalabilità simili a quelle di IBM DB2 per z/OS in esecuzione in un'installazione parallela di Sysplex.

Per iniziare, vedere [IBM DB2 pureScale in Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerazioni

Quando si esegue la migrazione dei carichi di lavoro mainframe all'infrastruttura di Azure come servizio (IaaS), è possibile scegliere tra diversi tipi di risorse di elaborazione scalabili su richiesta, incluse le macchine virtuali di Azure.When you migrate mainframe workloads to Azure infrastructure as a service (IaaS), you can choose from several types of on-demand, scalable computing resources, including Azure VMs. Azure offre una gamma di macchine virtuali Linux e [Windows.Azure](/azure/virtual-machines/windows/overview) offers a range of [Linux](/azure/virtual-machines/linux/overview) and Windows VMs.

### <a name="compute"></a>Calcolo

La potenza di calcolo di Azure viene confrontata favorevolmente con la capacità di un mainframe. Se stai pensando di spostare un carico di lavoro mainframe in Azure, confronta la metrica mainframe di un milione di istruzioni al secondo (MIPS) con le CPU virtuali. 

Informazioni su come spostare il [calcolo del mainframe](./concepts/mainframe-compute-azure.md)in Azure.

### <a name="high-availability-and-failover"></a>Disponibilità elevata e failover

Azure offre contratti di servizio basati sull'impegno. La disponibilità di più nove è l'impostazione predefinita e i contratti di servizio possono essere ottimizzati con la replica locale o basata su aree geografiche dei servizi. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

Con Azure IaaS, ad esempio una macchina virtuale, funzioni di sistema specifiche forniscono supporto per il failover, ad esempio istanze di clustering di failover e set di disponibilità. Quando si usa la piattaforma Azure come risorse di servizio (PaaS), la piattaforma gestisce automaticamente il failover. Gli esempi includono [Il database SQL di Azure](/azure/sql-database/sql-database-technical-overview) e il database cosmo di [Azure.](/azure/cosmos-db/introduction)

### <a name="scalability"></a>Scalabilità

I mainframe in genere aumentano, mentre gli ambienti cloud aumentano. Azure offre una gamma di dimensioni [Linux](/azure/virtual-machines/linux/sizes) e [Windows](/azure/virtual-machines/windows/sizes) per soddisfare le tue esigenze. Il cloud viene inoltre ridimensionato in base alle specifiche dell'utente. Potenza di calcolo, storage e servizi [scalabili](/azure/architecture/best-practices/auto-scaling) su richiesta in base a un modello di fatturazione basato sull'utilizzo.

### <a name="storage"></a>Archiviazione

Nel cloud, hai una gamma di opzioni di archiviazione flessibili e scalabili e paghi solo per ciò di cui hai bisogno. [Archiviazione di Azure](/azure/storage/common/storage-introduction) offre un archivio a scalabilità elevata per oggetti dati, un servizio di file system per il cloud, un archivio di messaggistica affidabile e un archivio NoSQL. Per le macchine virtuali, i dischi gestiti e non gestiti offrono risorse di archiviazione su disco persistenti e sicure.

Informazioni su come [spostare l'archiviazione mainframe](./concepts/mainframe-storage-azure.md)in Azure.

### <a name="backup-and-recovery"></a>Backup e ripristino

Mantenere il proprio sito di ripristino di emergenza può essere una proposta costosa. Azure offre opzioni facili da implementare ed economiche per [il backup,](/azure/backup/backup-introduction-to-azure-backup)il [ripristino](/azure/site-recovery/site-recovery-overview)e la [ridondanza](/azure/storage/common/storage-redundancy) a livello locale o regionale oppure tramite la ridondanza geografica.

## <a name="azure-government-for-mainframe-migrations"></a>Azure per enti pubblici per le migrazioni mainframeAzure Government for mainframe migrations

Molte entità del settore pubblico vorrebbero spostare le loro applicazioni mainframe su una piattaforma più moderna e flessibile. Microsoft Azure per enti pubblici è un'istanza fisicamente separata della piattaforma Microsoft Azure globale, inclusa nel pacchetto per sistemi federali, statali e locali. Fornisce servizi di sicurezza, protezione e conformità di livello mondiale specifici per le agenzie governative degli Stati Uniti e i loro partner.

Azure Government ha ottenuto un'autorità provvisoria per operare (P-ATO) per FedRAMP High Impact per i sistemi che necessitano di questo tipo di ambiente.

Per iniziare, scaricare [Microsoft Azure Government cloud for mainframe applications](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Passaggi successivi

Chiedi ai nostri [partner](partner-workloads.md) di aiutarti a migrare o ospitare nuovamente le tue applicazioni mainframe. 

Vedere anche la pagina relativa alla

- [White paper sugli argomenti mainframe](mainframe-white-papers.md)
- [Migrazione del mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Risoluzione dei problemi](/azure/virtual-machines/troubleshooting/)
- [Demistificazione della migrazione da mainframe ad AzureDemistifying mainframe to Azure migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
