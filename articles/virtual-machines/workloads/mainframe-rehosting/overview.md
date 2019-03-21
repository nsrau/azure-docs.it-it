---
title: Mainframe rehosting in macchine virtuali di Azure | Microsoft Docs
description: Rehosting di carichi di lavoro di mainframe, ad esempio i sistemi basati su IBM Z usando macchine virtuali (VM) in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 8be0ebc486739f8826e8a1d5a5307a219ba71b6f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192718"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframe rehosting in macchine virtuali di Azure

Migrazione dei carichi di lavoro da mainframe o ambienti di cloud ti permette modernizza l'infrastruttura e risparmiare sui costi. Molti carichi di lavoro possono essere trasferiti in Azure con semplici modifiche del codice, ad esempio aggiornando i nomi dei database.

Il termine *mainframe* in genere fa riferimento a un computer di grandi dimensioni, ma la maggior parte attualmente distribuita sono Z sistema IBM server o sistemi plug-compatible IBM che esegue MVS, DOS, VSE, OS/390 o z/OS.

Una macchina virtuale di Azure (VM) viene utilizzata per isolare e gestire le risorse per un'applicazione specifica in una singola istanza. Ad esempio IBM z/OS mainframe usano partizioni logiche (LPARS) per questo scopo. Un mainframe potrebbe usare una LPAR per un'area CICS con programmi COBOL associati e un LPAR separato per il database IBM Db2. Una tipica [applicazione a più livelli in Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) distribuisce macchine virtuali di Azure in una rete virtuale che può essere segmentata in subnet per ogni livello.

Macchine virtuali di Azure possono eseguire ambienti di emulazione mainframe e i compilatori che supportano scenari lift-and-shift. Sviluppo e test sono spesso fra i primi carichi di lavoro per eseguire la migrazione da un mainframe a un ambiente di sviluppo/test di Azure. Componenti server comuni che è possibile emulare includono processo delle transazioni online (OLTP), batch e i sistemi di inserimento dei dati come mostrato nella figura seguente.

![Ambienti di emulazione di Azure consentono di eseguire sistemi basati su z/OS.](media/01-overview.png)

Alcuni carichi di lavoro di mainframe possono essere migrate in Azure con relativa facilità, mentre altri utenti può essere ospitato nuovamente in Azure usando una soluzione dei partner. Per istruzioni dettagliate sulla scelta di una soluzione dei partner, il [centro migrazione Mainframe Azure](https://azure.microsoft.com/migration/mainframe/) può aiutare.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Configurare l'ambiente di sviluppo/test usando una piattaforma di riallocazione di Micro Focus

Micro Focus Enterprise Server è una delle più grande mainframe rehosting piattaforme disponibili. Consente di eseguire carichi di lavoro z/OS un x86 meno costoso platform in Azure.

Per iniziare, vedere gli articoli seguenti:

- [Installare Micro Focus Enterprise Server 4.0 e gli sviluppatori aziendali 4.0 in Azure](./microfocus/set-up-micro-focus-on-azure.md)
- [Impostare i Micro messa a fuoco CICS BankDemo per Micro messa a fuoco Enterprise Developer 4.0 in Azure](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame in Azure

TmaxSoft OpenFrame è un diffuso mainframe riallocazione della soluzione che rende più semplice per gli asset esistenti mainframe lift- and -shift li in Azure. Un ambiente OpenFrame in Azure è adatto per lo sviluppo, demo, test o i carichi di lavoro di produzione.

Per iniziare, scaricare il [installare TmaxSoft OpenFrame in Azure](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) ebook.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>Configurare un ambiente di sviluppo/test con IBM Z Devtest 12.0

IBM Z Development e l'ambiente di Test (IBM zD & T) configura un ambiente non di produzione in Azure che è possibile usare per lo sviluppo, test e demo di applicazioni basate su z/OS.

L'ambiente di emulazione in Azure può ospitare un'ampia gamma di istanze di Z tramite applicazione sviluppatori controllato distribuzioni (ADCDs). È possibile eseguire zD & T Personal Edition, zD Sysplex parallele T, zD & e T Enterprise Edition su Azure e Azure Stack.

Per iniziare, vedere gli articoli seguenti:

-   [Configurare IBM zD & T 12.0 in Azure](./ibm/install-ibm-z-environment.md)
-   [Configurare ADCD zD & T](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>Eseguire la migrazione di IBM DB2 pureScale in Azure

L'ambiente IBM DB2 pureScale rappresenta un cluster database per Azure con disponibilità e scalabilità elevate in sistemi operativi Linux. Anche se non è identico all'ambiente originale, IBM DB2 pureScale su Linux offre funzionalità di scalabilità e disponibilità elevate analoghe a IBM DB2 per z/OS in esecuzione in una configurazione Parallel Sysplex nel mainframe.

Per iniziare, vedere [pureScale IBM DB2 su Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Considerazioni

Quando si esegue la migrazione di carichi di lavoro di mainframe nell'infrastruttura di Azure come servizio (IaaS), è possibile scegliere tra diversi tipi on demand scalabile risorse di calcolo, tra cui macchine virtuali di Azure. Azure offre numerose [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) macchine virtuali.

### <a name="high-availability-and-failover"></a>Disponibilità elevata e failover

Azure offre basato sull'impegno contratti di servizio (SLA), in cui più 9 s disponibilità è il valore predefinito, ottimizzato con la replica dei servizi locale o basate sulla geografica. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

IaaS di Azure, ad esempio VM, il failover si basa sulla funzionalità di sistema specifici, ad esempio le istanze di clustering di failover e [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets). Quando si usa la piattaforma Azure con un servizio (PaaS) alle risorse, ad esempio [Database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) e [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), la piattaforma gestisce automaticamente i failover.

### <a name="scalability"></a>Scalabilità

Mainframe in genere aumentare le prestazioni, mentre il livello di ambiente cloud out. Azure offre numerose [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) dimensioni in base alle esigenze. Inoltre, nel cloud Ridimensiona su o giù per le specifiche di utente esatta corrispondenza. Potenza di calcolo, archiviazione e i servizi [scalabilità](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) su richiesta in un modello di fatturazione basato sull'utilizzo.

### <a name="storage"></a>Archiviazione

Nel cloud, sono disponibili diverse opzioni di archiviazione flessibili, scalabili e pagare solo per ciò che è necessario. [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) offre un archivio a scalabilità elevata per oggetti dati, un servizio di file system per il cloud, un archivio di messaggistica affidabile e un archivio NoSQL. Per le macchine virtuali, i dischi gestiti e non gestiti offrono risorse di archiviazione su disco persistenti e sicure.

### <a name="backup-and-recovery"></a>Backup e ripristino

Mantenere il proprio sito di ripristino di emergenza può essere una soluzione costosa. Azure offre opzioni semplici da implementare e conveniente per [backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), [ripristino](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), e [ridondanza](https://docs.microsoft.com/azure/storage/common/storage-redundancy) a livello locale o a livello di area o tramite la ridondanza geografica.

## <a name="azure-government-for-mainframe-migrations"></a>Azure per enti pubblici per le migrazioni di mainframe

Molti enti pubblici graditi passare le applicazioni mainframe a una piattaforma flessibile e più moderna. Microsoft Azure per enti pubblici è un'istanza fisicamente separata di servizi cloud basato sulla piattaforma Microsoft Azure globale, ma inclusi nel pacchetto per i sistemi governativi federali, statali e locali. Fornisce servizi di conformità in modo specifico per agenzie governative degli Stati Uniti e i rispettivi partner, protezione e sicurezza all'avanguardia.

Azure per enti pubblici ottenuto un'autorizzazione Provisional Authority to Operate (P-ATO) di FedRAMP High Impact per i sistemi che richiedono questo tipo di ambiente. 

Per iniziare, scaricare [cloud di Microsoft Azure per enti pubblici per le applicazioni mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="learn-more"></a>Altre informazioni

Se si stanno prendendo in considerazione una migrazione mainframe, nostro estesa [partner](partner-workloads.md) ecosistema è offrono supporto all'utente. Per indicazioni dettagliate sulla scelta di una soluzione dei partner, vedere [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

Vedere anche la pagina relativa alla

-   [Migrazione del mainframe](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [Risoluzione dei problemi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Demistificazione di mainframe alla migrazione ad Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
