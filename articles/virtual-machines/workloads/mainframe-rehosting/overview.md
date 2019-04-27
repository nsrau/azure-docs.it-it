---
title: Mainframe rehosting in macchine virtuali di Azure
description: Rehosting di carichi di lavoro di mainframe, ad esempio i sistemi basati su IBM Z usando macchine virtuali (VM) in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487460"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe rehosting in macchine virtuali di Azure

Migrazione dei carichi di lavoro da mainframe o ambienti di cloud ti permette modernizza l'infrastruttura e risparmiare sui costi. Molti carichi di lavoro possono essere trasferiti in Azure con semplici modifiche del codice, ad esempio aggiornando i nomi dei database.

In generale, il termine *mainframe* significa che un sistema di computer di grandi dimensioni. In particolare, la maggior parte attualmente in uso sono i server IBM Z di sistema o IBM plug-compatible sistemi che eseguono MVS, DOS, VSE, OS/390 o z/OS.

Una macchina virtuale di Azure (VM) viene utilizzata per isolare e gestire le risorse per un'applicazione specifica in una singola istanza. Ad esempio IBM z/OS mainframe usano partizioni logiche (LPARS) per questo scopo. Un mainframe potrebbe usare una LPAR per un'area CICS con programmi COBOL associati e un LPAR separato per il database IBM Db2. Una tipica [applicazione a più livelli in Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) distribuisce macchine virtuali di Azure in una rete virtuale che può essere segmentata in subnet per ogni livello.

Macchine virtuali di Azure possono eseguire ambienti di emulazione mainframe e i compilatori che supportano scenari lift-and-shift. Sviluppo e test sono spesso fra i primi carichi di lavoro per eseguire la migrazione da un mainframe a un ambiente di sviluppo/test di Azure. Componenti server comuni che è possibile emulare includono processo delle transazioni online (OLTP), batch e i sistemi di inserimento dei dati come mostrato nella figura seguente.

![Ambienti di emulazione di Azure consentono di eseguire sistemi basati su z/OS.](media/01-overview.png)

Alcuni carichi di lavoro di mainframe possono essere migrate in Azure con relativa facilità, mentre altri utenti può essere ospitato nuovamente in Azure usando una soluzione dei partner. Per istruzioni dettagliate sulla scelta di una soluzione dei partner, il [centro migrazione Mainframe Azure](https://azure.microsoft.com/migration/mainframe/) può aiutare.

## <a name="mainframe-migration"></a>Migrazione dei mainframe

Rehosting, ricompilare, sostituire o ritiro? IaaS o PaaS? Per determinare la strategia di migrazione appropriato per l'applicazione mainframe, vedere la [migrazione del Mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) Guida nel centro architettura di Azure.

## <a name="micro-focus-rehosting-platform"></a>Micro piattaforma riallocazione messa a fuoco

Micro Focus Enterprise Server è una delle più grande mainframe rehosting piattaforme disponibili. Consente di eseguire carichi di lavoro z/OS un x86 meno costoso platform in Azure.

Attività iniziali

- [Installare Server Enterprise e Developer Enterprise in Azure](./microfocus/set-up-micro-focus-azure.md)
- [Configurare BankDemo CICS per gli sviluppatori aziendali in Azure](./microfocus/demo.md)
- [Eseguire Server Enterprise in un contenitore Docker in Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame in Azure

TmaxSoft OpenFrame è una soluzione di riallocazione mainframe diffuso usata negli scenari lift-and-shift. Un ambiente OpenFrame in Azure è adatto per lo sviluppo, demo, test o i carichi di lavoro di produzione.

Attività iniziali

- [Introduzione a TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Scarica l'ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z Development e l'ambiente di Test (IBM zD & T) configura un ambiente non di produzione in Azure che è possibile usare per lo sviluppo, test e demo di applicazioni basate su z/OS.

L'ambiente di emulazione in Azure Puoi ospitare diversi tipi di istanze di Z tramite applicazione sviluppatori controllato distribuzioni (ADCDs). È possibile eseguire zD & T Personal Edition, zD Sysplex parallele T, zD & e T Enterprise Edition su Azure e Azure Stack.

Attività iniziali

- [Configurare IBM zD & T 12.0 in Azure](./ibm/install-ibm-z-environment.md)
- [Configurare ADCD zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale in Azure

L'ambiente di pureScale IBM DB2 fornisce un cluster di database per Azure. Non è identico all'ambiente originale, ma offre disponibilità e scalabilità simili come IBM DB2 per z/OS in esecuzione in una configurazione Sysplex parallele.

Per iniziare, vedere [pureScale IBM DB2 su Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerazioni

Quando si esegue la migrazione di carichi di lavoro di mainframe nell'infrastruttura di Azure come servizio (IaaS), è possibile scegliere tra diversi tipi on demand scalabile risorse di calcolo, tra cui macchine virtuali di Azure. Azure offre numerose [Linux](/azure/virtual-machines/linux/overview) e [Windows](/azure/virtual-machines/windows/overview) macchine virtuali.

### <a name="compute"></a>Calcolo

Potenza di calcolo di Azure Confronta presenta dei vantaggi rispetto alla capacità di un mainframe. Se si intende lo spostamento di un carico di lavoro di mainframe in Azure, confrontare la metrica di mainframe di un milione di istruzioni al secondo (MIP) CPU virtuali. 

Informazioni su come [spostare il calcolo di mainframe in Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Disponibilità elevata e failover

Azure offre basato sull'impegno contratti di servizio (SLA). Il valore predefinito è la disponibilità di più nove e contratti di servizio può essere ottimizzate con la replica dei servizi locale o basate sulla geografica. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

Con IaaS di Azure, ad esempio una macchina virtuale, le funzioni di sistema specifico supportano il failover, ad esempio, le istanze di clustering di failover e [set di disponibilità](/azure/virtual-machines/windows/regions-and-availability#availability-sets). Quando si usa la piattaforma Azure come delle risorse del servizio (PaaS), la piattaforma gestisce automaticamente il failover. Gli esempi includono [Database SQL di Azure](/azure/sql-database/sql-database-technical-overview) e [Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Scalabilità

I mainframe supportano in genere la scalabilità verticale, mentre gli ambienti cloud supportano lo scale-out. Azure offre numerose [Linux](/azure/virtual-machines/linux/sizes) e [Windows](/azure/virtual-machines/windows/sizes) dimensioni in base alle esigenze. Inoltre, nel cloud Ridimensiona su o giù per le specifiche di utente esatta corrispondenza. Potenza di calcolo, archiviazione e i servizi [scalabilità](/azure/architecture/best-practices/auto-scaling) su richiesta in un modello di fatturazione basato sull'utilizzo.

### <a name="storage"></a>Archiviazione

Nel cloud, sono disponibili diverse opzioni di archiviazione flessibili, scalabili e pagare solo per ciò che è necessario. [Archiviazione di Azure](/azure/storage/common/storage-introduction) offre un archivio a scalabilità elevata per oggetti dati, un servizio di file system per il cloud, un archivio di messaggistica affidabile e un archivio NoSQL. Per le macchine virtuali, i dischi gestiti e non gestiti offrono risorse di archiviazione su disco persistenti e sicure.

Informazioni su come [spostare risorse di archiviazione di mainframe in Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Backup e ripristino

Mantenere il proprio sito di ripristino di emergenza può essere una soluzione costosa. Azure offre opzioni semplici da implementare e conveniente per [backup](/azure/backup/backup-introduction-to-azure-backup), [ripristino](/azure/site-recovery/site-recovery-overview), e [ridondanza](/azure/storage/common/storage-redundancy) a livello locale o a livello di area o tramite la ridondanza geografica.

## <a name="azure-government-for-mainframe-migrations"></a>Azure per enti pubblici per le migrazioni di mainframe

Molti enti pubblici graditi passare le applicazioni mainframe a una piattaforma flessibile e più moderna. Microsoft Azure per enti pubblici è un'istanza fisicamente separata della piattaforma Microsoft Azure globale, inclusi nel pacchetto per i sistemi governativi federali, statali e locali. Fornisce servizi di conformità in modo specifico per agenzie governative degli Stati Uniti e i rispettivi partner, protezione e sicurezza all'avanguardia.

Azure per enti pubblici ottenuto un'autorizzazione Provisional Authority to Operate (P-ATO) di FedRAMP High Impact per i sistemi che richiedono questo tipo di ambiente.

Per iniziare, scaricare [cloud di Microsoft Azure per enti pubblici per le applicazioni mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Passaggi successivi

Chiedere a nostro [partner](partner-workloads.md) che consentono di eseguire la migrazione o riallocare le tue applicazioni mainframe. Per istruzioni dettagliate sulla scelta di una soluzione dei partner, vedere la [piattaforma modernizzazione Alliance](https://www.platformmodernization.org/pages/mainframe.aspx) sito Web.

Vedere anche la pagina relativa alla

- [White paper su argomenti di mainframe](mainframe-white-papers.md)
- [Migrazione del mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Risoluzione dei problemi](/azure/virtual-machines/troubleshooting/)
- [Demistificazione di mainframe alla migrazione ad Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
