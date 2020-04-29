---
title: Installazione di SAP HANA in macchine virtuali di Azure | Microsoft Docs '
description: Guida per l'installazione di SAP HANA in macchine virtuali di Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123356"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Installazione di SAP HANA in macchine virtuali di Azure
## <a name="introduction"></a>Introduzione
Questa guida consente di puntare a risorse appropriate per la distribuzione di HANA in macchine virtuali di Azure. Questa guida indica le risorse di documentazione che è necessario verificare prima di installare SAP HANA in una macchina virtuale di Azure. Quindi, è possibile eseguire i passaggi corretti per terminare con una configurazione supportata di SAP HANA nelle VM di Azure.  

> [!NOTE]
> Questa guida descrive le distribuzioni di SAP HANA in macchine virtuali di Azure. Per informazioni su come distribuire SAP HANA in istanze large di HANA, vedere [come installare e configurare SAP Hana (istanze large) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation).
 
## <a name="prerequisites"></a>Prerequisiti
Questa guida presuppone anche che l'utente abbia familiarità con:
* SAP HANA e SAP NetWeaver e loro installazione locale.
* Come installare e usare SAP HANA e le istanze dell'applicazione SAP in Azure.
* I concetti e le procedure documentati in:
   * Pianificazione per la distribuzione di SAP in Azure, che include la pianificazione della rete virtuale di Azure e l'utilizzo di archiviazione di Azure. Vedere [SAP NetWeaver in macchine virtuali di Azure-Guida alla pianificazione e all'implementazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
   * Principi di distribuzione e modalità di distribuzione delle macchine virtuali in Azure. Vedere [distribuzione di macchine virtuali di Azure per SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
   * Concetti relativi alla disponibilità elevata per SAP HANA come documentati in [SAP Hana disponibilità elevata per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)

## <a name="step-by-step-before-deploying"></a>Procedura dettagliata prima della distribuzione
In questa sezione vengono elencati i diversi passaggi che è necessario eseguire prima di iniziare l'installazione di SAP HANA in una macchina virtuale di Azure. L'ordine è enumerato e, di conseguenza, deve essere seguito da come enumerato:

1. Non tutti gli scenari di distribuzione possibili sono supportati in Azure. Pertanto, è consigliabile controllare il carico di lavoro del documento [SAP negli scenari supportati da macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) per lo scenario che si sta tenendo in considerazione con la distribuzione di SAP Hana. Se lo scenario non è elencato, è necessario presupporre che non sia stato testato e, di conseguenza, non è supportato
2. Supponendo di avere un'idea approssimativa del requisito di memoria per la distribuzione di SAP HANA, è necessario trovare una macchina virtuale di Azure adatta. Non tutte le VM certificate per SAP NetWeaver, come documentato nella [Nota di supporto sap #1928533](https://launchpad.support.sap.com/#/notes/1928533), sono SAP Hana certificate. L'origine della verità per SAP HANA macchine virtuali di Azure certificate è il sito Web [SAP Hana directory hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Le unità a partire da **S** sono unità di [istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) e non macchine virtuali di Azure.
3. Tipi di VM di Azure diversi hanno versioni minime del sistema operativo diverse per SUSE Linux o Red Hat Linux. Nel sito Web [SAP Hana directory hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)è necessario fare clic su una voce nell'elenco di SAP Hana unità certificate per ottenere i dati dettagliati di questa unità. Oltre al carico di lavoro HANA supportato, vengono elencate le versioni del sistema operativo supportate con tali unità per SAP HANA
4. A partire dalle versioni del sistema operativo, è necessario considerare alcune versioni minime del kernel. Queste versioni minime sono documentate nelle note di supporto SAP seguenti:
    - [Nota di supporto SAP #2814271 SAP HANA backup non riesce in Azure con errore di checksum](https://launchpad.support.sap.com/#/notes/2814271)
    - [Nota di supporto SAP #2753418 possibile riduzione delle prestazioni a causa del fallback del timer](https://launchpad.support.sap.com/#/notes/2753418)
    - [Nota di supporto SAP #2791572 riduzione delle prestazioni a causa del supporto VDSO mancante per Hyper-V in Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. In base alla versione del sistema operativo supportata per il tipo di macchina virtuale scelta, è necessario verificare se la versione di SAP HANA desiderata è supportata con tale versione del sistema operativo. Leggere la [Nota di supporto SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) per una matrice di supporto di SAP Hana versioni con le diverse versioni del sistema operativo.
5. Come è possibile trovare una combinazione valida di tipo di VM di Azure, versione del sistema operativo e versione SAP HANA, è necessario archiviare la matrice di disponibilità del prodotto SAP. Nella matrice di disponibilità di SAP è possibile verificare se il prodotto SAP che si vuole eseguire nel database di SAP HANA è supportato.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Considerazioni dettagliate sulla distribuzione di macchine virtuali e sul sistema operativo guest
In questa fase è necessario eseguire i passaggi per la distribuzione delle macchine virtuali per installare HANA e infine ottimizzare il sistema operativo scelto dopo l'installazione.

1. Scegliere l'immagine di base dalla raccolta di Azure. Se si desidera creare un'immagine del sistema operativo personalizzata per SAP HANA, è necessario conoscerli tutti i diversi pacchetti necessari per un'installazione SAP HANA corretta. In caso contrario, è consigliabile usare le immagini SUSE e Red Hat per SAP o SAP HANA dalla raccolta immagini di Azure. Queste immagini includono i pacchetti necessari per una corretta installazione di HANA. In base al contratto di supporto con il provider del sistema operativo, è necessario scegliere un'immagine in cui inserire la propria licenza. In alternativa, è possibile scegliere un'immagine del sistema operativo che includa il supporto
2. Se si sceglie un'immagine del sistema operativo guest che richiede l'uso della propria licenza, è necessario registrare l'immagine del sistema operativo con la sottoscrizione, in modo che sia possibile scaricare e applicare le patch più recenti. Questo passaggio richiede l'accesso a Internet pubblico. A meno che non si imposti l'istanza privata di, ad esempio un server SMT in Azure.
3. Decidere la configurazione di rete della macchina virtuale. Per altre informazioni, vedere il documento [SAP Hana le configurazioni e le operazioni dell'infrastruttura in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). Tenere presente che non sono disponibili quote di velocità effettiva della rete che è possibile assegnare alle schede di rete virtuali in Azure. Di conseguenza, l'unico scopo di indirizzare il traffico attraverso diversi schede si basa sulle considerazioni sulla sicurezza. Ci riteniamo attendibile per trovare un compromesso supportato tra la complessità del routing del traffico attraverso più schede e i requisiti applicati dagli aspetti di sicurezza.
3. Applicare le patch più recenti al sistema operativo dopo che la macchina virtuale è stata distribuita e registrata. Registrato con una sottoscrizione personalizzata. In alternativa, nel caso in cui si scelga un'immagine che includa il supporto del sistema operativo, la macchina virtuale deve avere già accesso alle patch. 
4. Applicare le melodie necessarie per SAP HANA. Queste melodie sono elencate nelle note di supporto SAP seguenti:

    - [Nota di supporto SAP #2694118-Red Hat Enterprise Linux componente aggiuntivo a disponibilità elevata in Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Nota di supporto SAP #1984787-SUSE LINUX Enterprise Server 12: Note sull'installazione](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Nota di supporto SAP #2578899-SUSE Linux Enterprise Server 15: Nota sull'installazione](https://launchpad.support.sap.com/#/notes/2578899)
    - [Nota di supporto SAP #2002167-Red Hat Enterprise Linux 7. x: installazione e aggiornamento](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP Support Note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) (Nota di supporto n. 2247020 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7) 
    -  [Nota di supporto SAP #2772999-Red Hat Enterprise Linux 8. x: installazione e configurazione](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Nota di supporto SAP #2777782-SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Nota di supporto SAP #2455582-Linux: esecuzione di applicazioni SAP compilate con GCC 6. x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Nota di supporto SAP #2382421-ottimizzazione della configurazione di rete a livello di HANA e del sistema operativo](https://launchpad.support.sap.com/#/notes/2382421)

1. Selezionare il tipo di archiviazione di Azure per SAP HANA. In questo passaggio è necessario scegliere il layout di archiviazione per l'installazione di SAP HANA. Si useranno i dischi di Azure collegati o le condivisioni NFS native di Azure. I tipi di archiviazione di Azure supportati da o e le combinazioni di diversi tipi di archiviazione di Azure che possono essere usati sono documentati in [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Eseguire le configurazioni documentate come punto di partenza. Per i sistemi non di produzione, si potrebbe essere in grado di configurare una velocità effettiva o IOPS inferiore. Per finalità di produzione, potrebbe essere necessario configurare un numero maggiore di velocità effettiva e IOPS.
2. Assicurarsi di aver configurato [Azure acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) per i volumi che contengono i log delle transazioni DBMS o i log di rollforward quando si usano VM serie M o Mv2. Tenere presente le limitazioni per acceleratore di scrittura come documentato.
2. Controllare se la [rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) è abilitata nelle macchine virtuali distribuite.

> [!NOTE]
> Non tutti i comandi nei diversi profili SAP-Tune o come descritto nelle note potrebbero essere eseguiti correttamente in Azure. I comandi che modificano la modalità di risparmio energia delle macchine virtuali in genere restituiscono un errore poiché non è possibile modificare la modalità di alimentazione dell'hardware host di Azure sottostante.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Preparazioni dettagliate specifiche per le macchine virtuali di Azure
Una delle specifiche di Azure è l'installazione di un'estensione di macchina virtuale di Azure che fornisce i dati di monitoraggio per l'agente host SAP. I dettagli sull'installazione di questa estensione di monitoraggio sono documentati in:

-  [Nota sap 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) illustra il monitoraggio avanzato di SAP con macchine virtuali Linux in Azure 
-  La [Nota SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) illustra le informazioni su SAPOSCOL in Linux 
-  [Nota sap 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) illustra le metriche di monitoraggio principali per sap in Microsoft Azure
-  [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Installazione di SAP HANA
Con le macchine virtuali di Azure distribuite e i sistemi operativi registrati e configurati, è possibile installare SAP HANA in base all'installazione di SAP. Per iniziare a usare questa documentazione, iniziare da questo sito Web SAP [risorse Hana](https://www.sap.com/products/hana/implementation/resources.html)

Per SAP HANA configurazioni con scalabilità orizzontale usando dischi collegati direttamente di archiviazione Premium di Azure o ultra disk, leggi le specifiche nel documento [SAP Hana le configurazioni e le operazioni dell'infrastruttura in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Risorse aggiuntive per il backup SAP HANA
Per informazioni su come eseguire il backup di SAP HANA database in macchine virtuali di Azure, vedere:
* [Guida del backup di SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [Backup di SAP HANA di Azure a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)

## <a name="next-steps"></a>Passaggi successivi
Leggi la documentazione:

- [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)





