---
title: Installazione di SAP HANA in macchine virtuali di Azure Documenti Microsoft'
description: Guida per l'installazione di SAP HANA in macchine virtuali di AzureGuide for installation of SAP HANA on Azure VMs
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: e017e082472e7a4a2fab6a2845e52d3dc7acc460
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123356"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Installazione di SAP HANA nelle macchine virtuali di AzureInstallation of SAP HANA on Azure virtual machines
## <a name="introduction"></a>Introduzione
Questa guida consente di puntare alle risorse giuste per distribuire correttamente HANA nelle macchine virtuali di Azure.This guide helps you to point to the right resources to deploy HANA in Azure virtual machines successfully successfully. Questa guida indicherà le risorse della documentazione che è necessario controllare prima di installare SAP HANA in una macchina virtuale di Azure.This guide is to point you to documentation resources that you need to check before installing SAP HANA in an Azure VM. Pertanto, è possibile eseguire i passaggi corretti per terminare con una configurazione supportata di SAP HANA nelle macchine virtuali di Azure.So, that you are able to perform the right steps to end with a supported configuration of SAP HANA in Azure VMs.  

> [!NOTE]
> Questa guida descrive le distribuzioni di SAP HANA in macchine virtuali di Azure. Per informazioni su come distribuire SAP HANA in istanze di grandi dimensioni, vedere [Come installare e configurare SAP HANA (istanze di grandi dimensioni) in Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
 
## <a name="prerequisites"></a>Prerequisiti
Questa guida presuppone inoltre che l'utente abbia familiarità con:This guide also assumes that you're familiar with:
* SAP HANA e SAP NetWeaver e loro installazione locale.
* Come installare e gestire le istanze dell'applicazione SAP HANA e SAP in Azure.
* I concetti e le procedure documentati in:
   * Pianificazione della distribuzione SAP in Azure, che include la pianificazione della rete virtuale di Azure e l'utilizzo di Archiviazione di Azure.Planning for SAP deployment on Azure, which includes Azure Virtual Network planning and Azure Storage usage. Vedere [SAP NetWeaver in Macchine virtuali di Azure - Guida alla pianificazione e all'implementazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Principi di distribuzione e modalità di distribuzione delle macchine virtuali in Azure. Vedere Distribuzione di [Macchine virtuali di Azure per SAPSee Azure Virtual Machines deployment for SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Concetti di disponibilità elevata per SAP HANA, come documentato in [disponibilità elevata SAP HANA per macchine virtuali di AzureHigh](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) availability concepts for SAP HANA as documented in SAP HANA high availability for Azure virtual machines

## <a name="step-by-step-before-deploying"></a>Procedura dettagliata prima della distribuzione
In questa sezione sono elencati i diversi passaggi che è necessario eseguire prima di iniziare con l'installazione di SAP HANA in una macchina virtuale di Azure.In this section, the different steps are listed that you need to perform before start with the installation of SAP HANA in an Azure virtual machine. L'ordine viene enumerato e come tale deve essere seguito come enumerato:The order is enumerated and as such should be followed through as enumerated:

1. Non tutti gli scenari di distribuzione possibili sono supportati in Azure.Not all possible deployment scenarios are supported on Azure. Pertanto, è necessario controllare il carico di lavoro SAP del documento [negli scenari supportati della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) di Azure per lo scenario in uso con la distribuzione SAP HANA. Se lo scenario non è elencato, è necessario presupporre che non sia stato testato e, di conseguenza, non sia supportato
2. Supponendo di avere un'idea approssimativa sui requisiti di memoria per la distribuzione SAP HANA, è necessario trovare una macchina virtuale di Azure adatta. Non tutte le macchine virtuali certificate per SAP NetWeaver, come documentato nella [nota](https://launchpad.support.sap.com/#/notes/1928533)del supporto SAP #1928533 , sono certificate SAP HANA. La fonte della verità per le macchine virtuali di Azure certificate SAP HANA è la [directory hardware SAP HANA del](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)sito Web. Le unità che iniziano con S sono unità [di istanze di grandi dimensioni HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) e non macchine virtuali di Azure.The units starting with **S** are HANA Large Instances units and not Azure VMs.
3. Different Azure VM types have different minimum operating system releases for SUSE Linux or Red Hat Linux. Nel sito Web [SAP HANA hardware directory](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), è necessario fare clic su una voce nell'elenco delle unità certificate SAP HANA per ottenere dati dettagliati di questa unità. Oltre al carico di lavoro HANA supportato, sono elencate le versioni del sistema operativo supportate con tali unità per SAP HANA
4. A partire dal rilascio del sistema operativo, è necessario prendere in considerazione alcune versioni minime del kernel. Queste versioni minime sono documentate in queste note di supporto SAP:These minimum releases are documented in these SAP support notes:
    - [Nota di supporto SAP #2814271 SAP HANA Backup non riesce in Azure con errore di checksum](https://launchpad.support.sap.com/#/notes/2814271)
    - [Nota sul supporto SAP #2753418 potenziale riduzione delle prestazioni a causa del fallback del timer](https://launchpad.support.sap.com/#/notes/2753418)
    - [Nota del supporto SAP #2791572 la riduzione delle prestazioni a causa della mancanza del supporto VDSO per Hyper-V in Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. In base alla versione del sistema operativo supportata per il tipo di macchina virtuale scelta, è necessario verificare se la versione SAP HANA desiderata è supportata con tale versione del sistema operativo. Leggere la [nota di supporto SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) per una matrice di supporto delle versioni SAP HANA con le diverse versioni del sistema operativo.
5. Come si potrebbe aver trovato una combinazione valida di tipo di macchina virtuale di Azure, versione del sistema operativo e versione SAP HANA, è necessario controllare la matrice di disponibilità del prodotto SAP. Nella matrice di disponibilità SAP è possibile verificare se il prodotto SAP che si desidera eseguire nel database SAP HANA è supportato.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Considerazioni dettagliate sulla distribuzione di macchine virtuali e sul sistema operativo guestStep-by-step VM deployment and guest OS considerations
In questa fase, è necessario eseguire i passaggi di distribuzione delle macchine virtuali per installare HANA ed eventualmente ottimizzare il sistema operativo scelto dopo l'installazione.

1. Scegliere l'immagine di base dalla raccolta di Azure.Chose the base image out of the Azure gallery. Se si desidera creare la propria immagine del sistema operativo per SAP HANA, è necessario conoscere tutti i diversi pacchetti necessari per una corretta installazione di SAP HANA. In caso contrario, è consigliabile usare le immagini SUSE e Red Hat per SAP o SAP HANA dalla raccolta immagini di Azure.Otherwise it is recommended using the SUSE and Red Hat images for SAP or SAP HANA out of the Azure image gallery. Queste immagini includono i pacchetti necessari per una corretta installazione HANA. In base al contratto di supporto stipulato con il provider del sistema operativo, è necessario scegliere un'immagine in cui portare la propria licenza. Oppure scegli un'immagine del sistema operativo che include il supporto
2. Se si sceglie un'immagine del sistema operativo guest che richiede di portare la propria licenza, è necessario registrare l'immagine del sistema operativo con l'abbonamento, in modo da poter scaricare e applicare le patch più recenti. Questo passaggio richiederà l'accesso a Internet pubblico. A meno che non si configuri l'istanza privata di, ad esempio, un server SMT in Azure.Unless you set up your private instance of, for example, an SMT server in Azure.
3. Decidere la configurazione di rete della macchina virtuale. Per altre informazioni, vedere il documento [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) Tenere presente che non sono presenti quote di velocità effettiva di rete che è possibile assegnare alle schede di rete virtuale in Azure.Keep in mind that there are no network throughput quotas you can assign to virtual network cards in Azure. Di conseguenza, l'unico scopo di indirizzare il traffico attraverso diversi vNIC si basa su considerazioni di sicurezza. Ci fidiamo che tu trovi un compromesso supportabile tra la complessità del routing del traffico attraverso più VNIC e i requisiti applicati dagli aspetti di sicurezza.
3. Applicare le patch più recenti al sistema operativo dopo la distribuzione e la registrazione della macchina virtuale. Registrato con il proprio abbonamento. In alternativa, nel caso in cui si sceglie un'immagine che include il supporto del sistema operativo la macchina virtuale deve avere già accesso alle patch. 
4. Applicare i brani necessari per SAP HANA. Questi brani sono elencati in queste note di supporto SAP:These tunes are listed in these SAP support notes:

    - [Nota del supporto SAP #2694118 - Red Hat Enterprise Linux HA Add-On in Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Nota del supporto SAP #1984787 - SUSE LINUX Enterprise Server 12: Note sull'installazione](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Nota del supporto #2578899 SAP - SUSE Linux Enterprise Server 15: Nota sull'installazione](https://launchpad.support.sap.com/#/notes/2578899)
    - [Nota del supporto SAP #2002167 - Red Hat Enterprise Linux 7.x: installazione e aggiornamento](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP Support Note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) (Nota di supporto n. 2247020 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7) 
    -  [Nota del supporto SAP #2772999 - Red Hat Enterprise Linux 8.x: installazione e configurazione](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Nota sul supporto SAP #2777782 - SAP HANA DB: Impostazioni consigliate del sistema operativo per RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Nota di supporto SAP #2455582 - Linux: esecuzione di applicazioni SAP compilate con GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Nota del supporto SAP #2382421 - Ottimizzazione della configurazione di rete a livello di HANA e oS](https://launchpad.support.sap.com/#/notes/2382421)

1. Selezionare il tipo di archiviazione di Azure per SAP HANA. In questo passaggio è necessario decidere il layout di archiviazione per l'installazione di SAP HANA. Si intende usare dischi di Azure collegati o condivisioni NFS di Azure native. I tipi di archiviazione di Azure supportati e le combinazioni di diversi tipi di archiviazione di Azure che possono essere usati sono documentati nelle configurazioni di archiviazione delle macchine virtuali SAP HANA Azure.The Azure storage types that or supported and combinations of different Azure storage types that can be used, are documented in [SAP HANA Azure virtual machine storage configurations.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) Prendere le configurazioni documentate come punto di partenza. Per i sistemi non di produzione, è possibile configurare velocità effettiva inferiore o IOPS. Ai fini della produzione, potrebbe essere necessario configurare una velocità effettiva e operazioni di I/O al secondo.
2. Assicurarsi di aver configurato Azure Write Accelerator per i volumi che contengono i log delle transazioni DBMS o i log di ripetizione quando si usano macchine virtuali M-Series o Mv2-Series.Make sure that you configured [Azure Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) for your volumes that contain the DBMS transaction logs or redo logs when you are using M-Series or Mv2-Series VMs. Tenere presente le limitazioni per l'acceleratore di scrittura come documentato.
2. Verificare se [la rete accelerata](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) di Azure è abilitata nelle macchine virtuali distribuite.

> [!NOTE]
> Non tutti i comandi nei diversi profili sap-tune o come descritto nelle note potrebbero essere eseguiti correttamente in Azure.Not all the commands in the different sap-tune profiles or as described in the notes might run successfully on Azure. I comandi che potrebbero manipolare la modalità di alimentazione delle macchine virtuali restituiscono in genere un errore poiché la modalità di alimentazione dell'hardware host di Azure sottostante non può essere modificata.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Preparazioni dettagliate specifiche per le macchine virtuali di AzureStep-by-step preparations specific to Azure virtual machines
Una delle specifiche di Azure è l'installazione di un'estensione della macchina virtuale di Azure che fornisce i dati di monitoraggio per l'agente host SAP. I dettagli sull'installazione di questa estensione di monitoraggio sono documentati in:

-  [Sap Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) illustra il monitoraggio avanzato SAP con macchine virtuali Linux in Azure 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) illustra le informazioni su SAPOSCOL su Linux 
-  [Nota SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) illustra le metriche di monitoraggio chiave per SAP in Microsoft Azure
-  [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Installazione di SAP HANA
Con le macchine virtuali di Azure distribuite e i sistemi operativi registrati e configurati, è possibile installare SAP HANA in base all'installazione di SAP. Per iniziare a utilizzare questa documentazione, iniziare con questo sito Web SAP [RISORSE HANA](https://www.sap.com/products/hana/implementation/resources.html)

Per le configurazioni con scalabilità orizzontale SAP HANA che usano dischi collegati diretti di Azure Premium Storage o disco Ultra, leggere le specifiche nel documento [Configurazioni e operazioni dell'infrastruttura SAP HANA in AzureFor](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out) SAP HANA scale-out configurations using direct attached disks of Azure Premium Storage or Ultra disk, read the specifics in the document SAP HANA infrastructure configurations and operations on Azure


## <a name="additional-resources-for-sap-hana-backup"></a>Risorse aggiuntive per il backup SAP HANA
Per informazioni su come eseguire il backup dei database SAP HANA in macchine virtuali di Azure, vedere:For information on how to back up SAP HANA databases on Azure VMs, see:
* [Guida del backup di SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Backup di SAP HANA di Azure a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>Passaggi successivi
Leggi la documentazione:

- [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





