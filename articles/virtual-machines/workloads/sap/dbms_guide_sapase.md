---
title: Distribuzione DBMS per SAP ASE di macchine virtuali di Azure per un carico di lavoro SAP | Microsoft Docs
description: Distribuzione DBMS per SAP ASE di macchine virtuali di Azure per un carico di lavoro SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25d911869c95baba6ac9db3b893292e702e9c0e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81273206"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Distribuzione DBMS per SAP ASE di macchine virtuali di Azure per un carico di lavoro SAP

Questo documento illustra le numerose aree da valutare quando si distribuisce SAP ASE in IaaS di Azure. Prima di questo documento è consigliabile avere letto il documento [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP) e le altre guide disponibili nella [documentazione relativa a un carico di lavoro SAP in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Questo documento illustra l'uso di SAP ASE in Linux e nei sistemi operativi Windows. La versione minima supportata in Azure è SAP ASE 16.0.02 (versione 16 Support Pack 2). È consigliabile distribuire la versione più recente di SAP e il livello di patch più recente.  Come minimo, è consigliabile usare SAP ASE 16.0.03.07 (versione 16 Support Pack 3 patch level 7).  La versione più recente di SAP è disponibile nelle informazioni sulla pianificazione della versione dell'ambiente del servizio app di [destinazione 16,0 e sugli elenchi CR](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Sono disponibili altre informazioni sul supporto della versione con le applicazioni SAP o il percorso dei supporti di installazione, oltre che nella matrice di disponibilità dei prodotti SAP in questi percorsi:

- [Nota di supporto SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [Nota di supporto SAP #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [Nota di supporto SAP #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [Nota di supporto SAP #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Nota: per tutta la documentazione all'interno e all'esterno del mondo SAP, viene fatto riferimento al nome del prodotto come Sybase ASE o SAP ASE o in alcuni casi. Per rimanere coerenti, viene usato il nome **SAP ASE** in questa documentazione.

## <a name="operating-system-support"></a>Supporto dei sistemi operativi
La matrice di disponibilità del prodotto SAP contiene il sistema operativo supportato e le combinazioni di kernel SAP per ogni applicazione SAP.  Le distribuzioni Linux SUSE 12. x, SUSE 15. x, Red Hat 7. x sono completamente supportate.  Oracle Linux come sistema operativo per SAP ASE non è supportato.  È consigliabile usare le versioni più recenti di Linux disponibili. I clienti Windows devono usare le versioni di Windows Server 2016 o Windows Server 2019.  Le versioni precedenti di Windows, ad esempio Windows 2012, sono tecnicamente supportate, ma è sempre consigliabile usare la versione più recente di Windows.


## <a name="specifics-to-sap-ase-on-windows"></a>Specifiche per SAP ASE in Windows
Grazie a Microsoft Azure, è possibile eseguire facilmente la migrazione delle applicazioni SAP ASE in macchine virtuali di Azure. L'uso di SAP ASE in una macchina virtuale di Azure consente di ridurre il costo totale di proprietà associato a distribuzione, gestione e manutenzione delle applicazioni aziendali eseguendo facilmente la migrazione di queste applicazioni a Microsoft Azure. Con SAP ASE in una macchina virtuale di Azure, gli amministratori e gli sviluppatori possono continuare a usare gli stessi strumenti di sviluppo e amministrazione disponibili in locale.

Microsoft Azure offre vari tipi di macchina virtuale che consentono di eseguire dai più piccoli panorami applicativi e sistemi SAP ai panorami applicativi e sistemi SAP più grandi che comprendono migliaia di utenti. I numeri di SAP per il ridimensionamento di SAP dei diversi SKU di VM con certificazione SAP sono disponibili nella [Nota del supporto sap #1928533](https://launchpad.support.sap.com/#/notes/1928533).

La documentazione per l'installazione di SAP ASE in Windows è disponibile nella [Guida all'installazione di SAP ASE per Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Lock Pages in Memory è un'impostazione che impedisce il paging del buffer del database SAP ASE.  Questa impostazione è utile per i sistemi di grandi dimensioni con una grande quantità di memoria. Per ulteriori informazioni, contattare BC-DB-SYB. 


## <a name="linux-operating-system-specific-settings"></a>Impostazioni specifiche del sistema operativo Linux
Nelle VM Linux eseguire `saptune` con il profilo SAP-ASE Linux huge Pages deve essere abilitato per impostazione predefinita e può essere verificato con il comando  

`cat /proc/meminfo` 

La dimensione della pagina è in genere 2048 KB. Per informazioni dettagliate, vedere l'articolo [pagine di grandi dimensioni in Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Raccomandazioni sulla macchina virtuale e sulla struttura del disco per le distribuzioni di SAP ASE

SAP ASE per le applicazioni SAP NetWeaver è supportato in qualsiasi tipo di macchina virtuale elencato nella [Nota del supporto sap #1928533](https://launchpad.support.sap.com/#/notes/1928533) tipi di VM tipici usati per i server di database SAP ASE di medie dimensioni includono Esv3.  I database con più terabyte di grandi dimensioni possono sfruttare i tipi di VM serie M. Le prestazioni di scrittura del disco del log delle transazioni di SAP ASE possono essere migliorate abilitando la serie M acceleratore di scrittura. Acceleratore di scrittura deve essere testato con cautela con SAP ASE a causa del modo in cui SAP ASE esegue le Scritture del log.  Esaminare la [Nota di supporto SAP #2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) e prendere in considerazione l'esecuzione di un test delle prestazioni.  
Acceleratore di scrittura è progettato solo per il disco del log delle transazioni. La cache a livello di disco deve essere impostata su NONE. Non sorprendere se Azure acceleratore di scrittura non Mostra miglioramenti simili a quelli di altri sistemi DBMS. A seconda del modo in cui SAP ASE scrive nel log delle transazioni, è possibile che non si verifichi alcuna accelerazione da parte di Azure acceleratore di scrittura.
Per i dispositivi e i dispositivi di log è consigliabile usare dischi separati.  I database di sistema sybsecurity e `saptools` non richiedono dischi dedicati e possono essere inseriti sui dischi contenenti i dati del database SAP e i dispositivi di log 

![Configurazione dell'archiviazione per SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>File System, dimensioni stripe & bilanciamento IO 
SAP ASE scrive i dati in sequenza nei dispositivi di archiviazione su disco, a meno che non sia configurato diversamente. Ciò significa che un database SAP ASE vuoto con quattro dispositivi scriverà i dati solo nel primo dispositivo.  Gli altri dispositivi disco verranno scritti quando il primo dispositivo è pieno.  La quantità di i/o di lettura e scrittura per ogni dispositivo SAP ASE è probabilmente diversa. Per bilanciare l'i/o su disco in tutti i dischi di Azure disponibili, è necessario usare spazi di archiviazione Windows o LVM2 Linux. In Linux è consigliabile usare XFS file system per formattare i dischi. La dimensione di striping LVM deve essere testata con un test delle prestazioni. dimensioni di striping di 128 KB è un punto di partenza valido. In Windows, la dimensione dell'unità di allocazione NTFS (AUS) deve essere testata. 64 KB può essere utilizzato come valore iniziale. 

Si consiglia di configurare l'espansione automatica del database, come descritto nell'articolo [configurazione dell'espansione automatica dello spazio del database in SAP Adaptive Server Enterprise e della](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) nota di [supporto SAP #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Esempio di SAP ASE in macchine virtuali di Azure, configurazioni di dischi e file system 
I modelli seguenti illustrano le configurazioni di esempio per Linux e Windows. Prima di confermare la configurazione della macchina virtuale e del disco, assicurarsi che le quote della larghezza di banda di rete e di archiviazione della singola VM siano sufficienti per soddisfare i requisiti aziendali. Tenere inoltre presente che i diversi tipi di VM di Azure hanno un numero massimo di dischi diverso che possono essere collegati alla macchina virtuale. Ad esempio, una macchina virtuale E4s_v3 ha una velocità effettiva di i/o di archiviazione limitata di 48 MB/sec. Se la velocità effettiva di archiviazione richiesta dall'attività di backup del database richiede più di 48 MB/sec, un tipo di macchina virtuale più grande con una maggiore velocità effettiva della larghezza di banda è inevitabile. Quando si configura archiviazione di Azure, è anche necessario tenere presente che, soprattutto con [archiviazione Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) , la velocità effettiva e i IOPS per GB di capacità cambiano. Per altre informazioni su questo argomento, vedere [quali tipi di dischi sono disponibili in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types). Le quote per specifici tipi di VM di Azure sono documentate nell'articolo dimensioni e articoli di [macchine virtuali con ottimizzazione](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) per la memoria collegati. 

> [!NOTE]
>  Se è in corso lo spostamento di un sistema DBMS dall'ambiente locale ad Azure, è consigliabile eseguire il monitoraggio della macchina virtuale e valutare la velocità effettiva di CPU, memoria, IOPS e archiviazione. Confrontare i valori di picco osservati con i limiti di quota della VM documentati negli articoli sopra indicati

Gli esempi riportati di seguito sono a scopo illustrativo e possono essere modificati in base alle esigenze specifiche. A causa della progettazione dell'ambiente del servizio app SAP, il numero di dispositivi dati non è altrettanto importante di altri database. Il numero di dispositivi dati descritti in dettaglio in questo documento è una guida. 

Un esempio di configurazione per un piccolo server di database SAP ASE con una dimensione del database compresa tra 50 GB e 250 GB, ad esempio SAP Solution Manager, potrebbe essere simile a

| Configurazione | Windows | Linux | Commenti |
| --- | --- | --- | --- |
| Tipo macchina virtuale | E4s_v3 (4 vCPU/32 GB di RAM) | E4s_v3 (4 vCPU/32 GB di RAM) | --- |
| Rete accelerata | Abilitare | Abilitare | ---|
| Versione di SAP ASE | 16.0.03.07 o versione successiva | 16.0.03.07 o versione successiva | --- |
| n. di dispositivi dati | 4 | 4 | ---|
| n. di dispositivi di log | 1 | 1 | --- |
| numero di dispositivi temporanei | 1 | 1 | Altre informazioni per il carico di lavoro SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/15 SP1 o RHEL 7,6 | --- |
| Aggregazione del disco | Spazi di archiviazione | LVM2 | --- |
| File system | NTFS | XFS |
| Dimensioni blocco formato | richiede test del carico di lavoro | richiede test del carico di lavoro | --- |
| n. e tipo di dischi dati | Archiviazione Premium: 2 x P10 (RAID0) | Archiviazione Premium: 2 x P10 (RAID0)| Cache = sola lettura |
| # e tipo di dischi di log | Archiviazione Premium: 1 x P20  | Archiviazione Premium: 1 x P20 | Cache = NONE |
| Parametro MaxMemory dell'ambiente del servizio app | 90% di RAM fisica | 90% di RAM fisica | Supponendo una singola istanza |
| n. di dispositivi di backup | 4 | 4| --- |
| n. e tipo di dischi di backup | 1 | 1 | --- |


Un esempio di configurazione per un server di database SAP ASE medio con dimensioni di database comprese tra 250 GB e 750 GB, ad esempio un sistema SAP Business Suite di dimensioni minori, potrebbe essere simile a

| Configurazione | Windows | Linux | Commenti |
| --- | --- | --- | --- |
| Tipo macchina virtuale | E16s_v3 (16 vCPU/128 GB di RAM) | E16s_v3 (16 vCPU/128 GB di RAM) | --- |
| Rete accelerata | Abilitare | Abilitare | ---|
| Versione di SAP ASE | 16.0.03.07 o versione successiva | 16.0.03.07 o versione successiva | --- |
| n. di dispositivi dati | 8 | 8 | ---|
| n. di dispositivi di log | 1 | 1 | --- |
| numero di dispositivi temporanei | 1 | 1 | Altre informazioni per il carico di lavoro SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/15 SP1 o RHEL 7,6 | --- |
| Aggregazione del disco | Spazi di archiviazione | LVM2 | --- |
| File system | NTFS | XFS |
| Dimensioni blocco formato | richiede test del carico di lavoro | richiede test del carico di lavoro | --- |
| n. e tipo di dischi dati | Archiviazione Premium: 4 x P20 (RAID0) | Archiviazione Premium: 4 x P20 (RAID0)| Cache = sola lettura |
| # e tipo di dischi di log | Archiviazione Premium: 1 x P20  | Archiviazione Premium: 1 x P20 | Cache = NONE |
| Parametro MaxMemory dell'ambiente del servizio app | 90% di RAM fisica | 90% di RAM fisica | Supponendo una singola istanza |
| n. di dispositivi di backup | 4 | 4| --- |
| n. e tipo di dischi di backup | 1 | 1 | --- |

Un esempio di configurazione per un piccolo server di database SAP ASE con dimensioni di database comprese tra 750 GB e 2000 GB, ad esempio un sistema SAP Business Suite di dimensioni maggiori, potrebbe essere simile a

| Configurazione | Windows | Linux | Commenti |
| --- | --- | --- | --- |
| Tipo macchina virtuale | E64s_v3 (64 vCPU/432 GB di RAM) | E64s_v3 (64 vCPU/432 GB di RAM) | --- |
| Rete accelerata | Abilitare | Abilitare | ---|
| Versione di SAP ASE | 16.0.03.07 o versione successiva | 16.0.03.07 o versione successiva | --- |
| n. di dispositivi dati | 16 | 16 | ---|
| n. di dispositivi di log | 1 | 1 | --- |
| numero di dispositivi temporanei | 1 | 1 | Altre informazioni per il carico di lavoro SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/15 SP1 o RHEL 7,6 | --- |
| Aggregazione del disco | Spazi di archiviazione | LVM2 | --- |
| File system | NTFS | XFS |
| Dimensioni blocco formato | richiede test del carico di lavoro | richiede test del carico di lavoro | --- |
| n. e tipo di dischi dati | Archiviazione Premium: 4 x P30 (RAID0) | Archiviazione Premium: 4 x P30 (RAID0)| Cache = sola lettura |
| # e tipo di dischi di log | Archiviazione Premium: 1 x P20  | Archiviazione Premium: 1 x P20 | Cache = NONE |
| Parametro MaxMemory dell'ambiente del servizio app | 90% di RAM fisica | 90% di RAM fisica | Supponendo una singola istanza |
| n. di dispositivi di backup | 4 | 4| --- |
| n. e tipo di dischi di backup | 1 | 1 | --- |


Un esempio di configurazione per un server di database SAP ASE di piccole dimensioni con una dimensione del database di 2 TB +, ad esempio un sistema SAP Business Suite più grande usato a livello globale, potrebbe essere simile a

| Configurazione | Windows | Linux | Commenti |
| --- | --- | --- | --- |
| Tipo macchina virtuale | Serie M (da 1,0 a 4,0 TB di RAM)  | Serie M (da 1,0 a 4,0 TB di RAM) | --- |
| Rete accelerata | Abilitare | Abilitare | ---|
| Versione di SAP ASE | 16.0.03.07 o versione successiva | 16.0.03.07 o versione successiva | --- |
| n. di dispositivi dati | 32 | 32 | ---|
| n. di dispositivi di log | 1 | 1 | --- |
| numero di dispositivi temporanei | 1 | 1 | Altre informazioni per il carico di lavoro SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/15 SP1 o RHEL 7,6 | --- |
| Aggregazione del disco | Spazi di archiviazione | LVM2 | --- |
| File system | NTFS | XFS |
| Dimensioni blocco formato | richiede test del carico di lavoro | richiede test del carico di lavoro | --- |
| n. e tipo di dischi dati | Archiviazione Premium: 4 + x P30 (RAID0) | Archiviazione Premium: 4 + x P30 (RAID0)| Cache = sola lettura, prendere in considerazione il disco Ultra di Azure |
| # e tipo di dischi di log | Archiviazione Premium: 1 x P20  | Archiviazione Premium: 1 x P20 | Cache = nessuno, prendere in considerazione il disco Ultra di Azure |
| Parametro MaxMemory dell'ambiente del servizio app | 90% di RAM fisica | 90% di RAM fisica | Supponendo una singola istanza |
| n. di dispositivi di backup | 16 | 16 | --- |
| n. e tipo di dischi di backup | 4 | 4 | Usare spazi di archiviazione/LVM2 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Considerazioni sul ripristino del & di backup per SAP ASE in Azure
L'aumento del numero di dati e dei dispositivi di backup aumenta le prestazioni di backup e ripristino. È consigliabile eseguire lo striping dei dischi di Azure che ospitano il dispositivo di backup di SAP ASE come illustrato nelle tabelle riportate in precedenza. È necessario prestare attenzione per bilanciare il numero di dischi e dispositivi di backup e garantire che la velocità effettiva del backup non superi il 40% del 50% della quota di velocità effettiva totale della macchina virtuale. Per impostazione predefinita, è consigliabile usare la compressione di backup di SAP. Altre informazioni sono disponibili negli articoli seguenti:

- [Nota di supporto SAP #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [Nota di supporto SAP #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [Nota di supporto SAP #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Non usare l'unità D:\ o/Temp spazio come destinazione del dump del database o del log.

### <a name="impact-of-database-compression"></a>Impatto della compressione del database
Nelle configurazioni in cui la larghezza di banda di I/O può diventare un fattore limitante, le misure, che riducono le operazioni di i/O al secondo, potrebbero aiutare ad allungare il carico di lavoro che è possibile eseguire È quindi consigliabile verificare che venga usata la compressione SAP ASE prima di caricare un database SAP esistente in Azure.

I motivi per cui è consigliabile comprimere il database prima di caricarlo in Azure sono molti:

* La quantità di dati da caricare in Azure è minore
* La durata dell'esecuzione della compressione è inferiore, presupponendo che si possa usare hardware più avanzato con più CPU o una maggiore larghezza di banda di I/O oppure meno latenza di I/O in locale
* L'uso di database di dimensioni inferiori può contribuire a ridurre i costi per l'allocazione dei dischi

Il funzionamento della compressione dati e LOB in una VM ospitata in macchine virtuali di Azure è uguale a quello in locale. Per altri dettagli su come verificare se la compressione è già in uso in un database SAP ASE esistente, vedere la [Nota di supporto sap 1750510](https://launchpad.support.sap.com/#/notes/1750510). Per altre informazioni sulla compressione del database SAP ASE, vedere la [Nota di supporto sap #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Disponibilità elevata di SAP ASE in Azure 
La guida per gli utenti di HADR illustra l'installazione e la configurazione di una soluzione "always-on" di SAP ASE a 2 nodi.  Inoltre, è supportato anche un terzo nodo di ripristino di emergenza. SAP ASE supporta molte configurazioni a disponibilità elevata, tra cui il disco condiviso e il clustering del sistema operativo nativo (IP mobile). L'unica configurazione supportata in Azure è l'uso di gestione errori senza IP mobile.  Il metodo di indirizzo IP mobile non funziona in Azure.  Il kernel SAP è un'applicazione a disponibilità elevata ed è a conoscenza dei server SAP ASE primari e secondari. Non ci sono integrazioni di chiusura tra SAP ASE e Azure, ma non viene usato il servizio di bilanciamento del carico interno di Azure. Per questo motivo, è necessario seguire la documentazione di SAP ASE standard a partire dalla [Guida per gli utenti di SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> L'unica configurazione supportata in Azure è l'uso di gestione errori senza IP mobile.  Il metodo di indirizzo IP mobile non funziona in Azure. 

### <a name="third-node-for-disaster-recovery"></a>Terzo nodo per il ripristino di emergenza
Oltre all'uso di SAP ASE always on per la disponibilità elevata locale, potrebbe essere necessario estendere la configurazione a un nodo replicato in modo asincrono in un'altra area di Azure. La documentazione relativa a tale scenario è disponibile [qui](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>Crittografia del database SAP ASE & SSL 
SAP software Provisioning Manager (SWPM) offre un'opzione per crittografare il database durante l'installazione.  Se si vuole usare la crittografia, è consigliabile usare la crittografia completa del database SAP.  Vedere i dettagli documentati in:

- [Nota di supporto SAP #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [Nota di supporto SAP #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [Nota di supporto SAP #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [Nota di supporto SAP #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Se un database SAP ASE è crittografato, la compressione del dump del backup non funzionerà. Vedere anche la [Nota di supporto SAP #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Elenco di controllo per la distribuzione di SAP ASE in Azure
 
- Distribuire SAP ASE 16.0.03.07 o versione successiva
- Eseguire l'aggiornamento alla versione più recente e alle patch di FaultManager e SAPHostAgent
- Distribuisci in un sistema operativo certificato più recente disponibile, ad esempio Windows 2019, SUSE 15,1 o RedHat 7,6 o versione successiva
- Usare macchine virtuali con certificazione SAP: SKU di VM di Azure a memoria elevata, ad esempio Es_v3 o per i sistemi x-large. gli SKU di VM serie M sono consigliati
- Corrisponde alla quota di IOPS del disco e alla velocità effettiva aggregata totale della VM della VM con la progettazione del disco.  Distribuire un numero sufficiente di dischi
- Aggregare i dischi usando spazi di archiviazione Windows o Linux LVM2 con le dimensioni di striping corrette e file system
- Creazione di un numero sufficiente di dispositivi per scopi di dati, log, Temp e backup
- Prendere in considerazione l'uso di UltraDisk per sistemi x-large 
- Eseguire `saptune` SAP-ASE in un sistema operativo Linux 
- Proteggere il database con la crittografia DB: archiviare manualmente le chiavi in Azure Key Vault 
- Completare l' [elenco di controllo di SAP in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 
- Configurare backup del log e backup completo 
- Test di disponibilità elevata/ripristino di emergenza, backup e ripristino ed esecuzione di stress & test del volume 
- Verificare che l'estensione del database automatica sia funzionante 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizzo di DBACockpit per monitorare le istanze di database
Per i sistemi SAP che usano SAP ASE come piattaforma di database, DBACockpit è accessibile come finestra del browser incorporata nella transazione DBACockpit o come Webdynpro. Tuttavia, la funzionalità completa per il monitoraggio e l'amministrazione del database è disponibile solo nell'implementazione WebDynpro del DBACockpit.

Come per i sistemi locali, sono necessari diversi passaggi per abilitare tutte le funzionalità di SAP NetWeaver usate dall'implementazione Webdynpro di DBACockpit. Seguire la [Nota di supporto SAP #1245200](https://launchpad.support.sap.com/#/notes/1245200) per abilitare l'uso di WebDynpro e generare quelli necessari. Quando si seguono le istruzioni riportate nelle note precedenti, si configura anche Internet Communication Manager ( `ICM` ) insieme alle porte da utilizzare per le connessioni HTTP e HTTPS. L'impostazione predefinita per HTTP è simile alla seguente:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

I collegamenti generati nella transazione DBACockpit sono simili ai seguenti:

> https: \/ / \<fullyqualifiedhostname> : 44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> : 8000/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> 

A seconda del modo in cui la macchina virtuale di Azure ospitante il sistema SAP è connessa ad Active Directory e DNS, è necessario verificare che ICM usi un nome host completo che può essere risolto nel computer da cui si sta aprendo DBACockpit. Vedere la [Nota sul supporto SAP #773830](https://launchpad.support.sap.com/#/notes/773830) per comprendere in che modo ICM determina il nome host completo in base ai parametri del profilo e impostare il parametro icm/host_name_full in modo esplicito, se necessario.

Se la macchina virtuale è stata distribuita in uno scenario solo cloud senza connettività cross-premise tra l'istanza locale e Azure, è necessario definire un indirizzo IP pubblico e un `domainlabel` . Il formato del nome DNS pubblico della VM si presenta come segue:

> `<custom domainlabel`&gt;.`<azure region`&gt;.cloudapp.azure.com
> 
> 

Per altri dettagli relativi al nome DNS, vedere [qui] [Virtual-Machines-azurerm-versus-azuresm].

Impostando il parametro del profilo SAP icm/host_name_full sul nome DNS della VM di Azure, il collegamento sarà simile a:

> https: \/ /mydomainlabel.westeurope.cloudapp.NET:44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.NET:8000/SAP/BC/WebDynpro/SAP/dba_cockpit

In questo caso è necessario assicurarsi di:

* Aggiungere regole in ingresso al gruppo di sicurezza di rete nel portale di Azure per le porte TCP/IP usate per comunicare con ICM
* Aggiungere regole in ingresso alla configurazione di Windows Firewall per le porte TCP/IP usate per comunicare con ICM

Per importare automaticamente tutte le correzioni disponibili, è consigliabile applicare periodicamente la nota SAP della raccolta di correzioni applicabile alla propria versione di SAP:

* [Nota di supporto SAP #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [Nota di supporto SAP #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [Nota di supporto SAP #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Per altre informazioni su DBA Cockpit per SAP ASE, vedere le note SAP seguenti:

* [Nota di supporto SAP #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [Nota di supporto SAP #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [Nota di supporto SAP #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [Nota di supporto SAP #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [Nota di supporto SAP #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [Nota di supporto SAP #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [Nota di supporto SAP #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [Nota di supporto SAP #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Collegamenti utili, note & white paper per SAP ASE
La pagina iniziale per la [documentazione di SAP ASE 16.0.03.07](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) fornisce collegamenti a diversi documenti di cui i documenti di:

- Percorso di apprendimento di SAP ASE-monitoraggio & amministrazione
- Percorso di apprendimento di SAP ASE-installazione & aggiornamento

sono utili. Un altro documento utile è [le applicazioni SAP in SAP Adaptive Server Enterprise procedure consigliate per la migrazione e il runtime](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Altre utili note sul supporto SAP sono:

- [Nota di supporto SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [Nota di supporto SAP #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [Nota di supporto SAP #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [Nota di supporto SAP #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [Nota di supporto SAP #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [Nota di supporto SAP #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [Nota di supporto SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota di supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [Nota di supporto SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [Nota di supporto SAP #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [Nota di supporto SAP #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [Nota di supporto SAP #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota di supporto SAP #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Altre informazioni pubblicate in 

- [Applicazioni SAP in SAP Adaptive Server Enterprise](https://community.sap.com/topics/applications-on-ase)
- [Centro informazioni per SAP ASE](http://infocenter.sybase.com/help/index.jsp) 
- [Installazione always on di SAP ASE con terzo nodo di ripristino di emergenza](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Una newsletter mensile viene pubblicata tramite la [Nota di supporto SAP #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo [carichi di lavoro SAP in Azure: elenco di controllo per la pianificazione e la distribuzione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

