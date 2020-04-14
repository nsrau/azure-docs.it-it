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
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273206"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Distribuzione DBMS per SAP ASE di macchine virtuali di Azure per un carico di lavoro SAP

Questo documento illustra le numerose aree da valutare quando si distribuisce SAP ASE in IaaS di Azure. Prima di questo documento è consigliabile avere letto il documento [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP) e le altre guide disponibili nella [documentazione relativa a un carico di lavoro SAP in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Questo documento illustra SAP ASE in esecuzione su Linux e su sistemi operativi Windows. La versione minima supportata in Azure è SAP ASE 16.0.02 (Versione 16 Support Pack 2). Si consiglia di distribuire la versione più recente di SAP e il livello di patch più recente.  Come minimo SAP ASE 16.0.03.07 (versione 16 Support Pack 3 Patch Level 7) è consigliato.  La versione più recente di SAP è disponibile in [Targeted ASE 16.0 Release Schedule and CR list Information](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Ulteriori informazioni sul supporto del rilascio con le applicazioni SAP o la posizione del supporto di installazione sono disponibili, oltre che nella matrice di disponibilità del prodotto SAP nelle seguenti posizioni:

- [Note del supporto SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [Nota #1941500 del supporto SAP](https://launchpad.support.sap.com/#/notes/1941500)
- [Note di supporto SAP #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [Nota del supporto SAP #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Nota: durante la documentazione all'interno e all'esterno del mondo SAP, il nome del prodotto viene fatto riferimento come Sybase ASE o SAP ASE o in alcuni casi entrambi. Per mantenere la coerenza, in questa documentazione viene usato il nome **SAP ASE.**

## <a name="operating-system-support"></a>Supporto dei sistemi operativi
La matrice di disponibilità del prodotto SAP contiene le combinazioni di sistema operativo e kernel SAP supportate per ogni applicazione SAP.  Distribuzioni Linux SUSE 12.x, SUSE 15.x, Red Hat 7.x sono completamente supportate.  Oracle Linux come sistema operativo per SAP ASE non è supportato.  Si consiglia di utilizzare le versioni Linux più recenti disponibili. I clienti di Windows devono utilizzare le versioni di Windows Server 2016 o Windows Server 2019.Windows customers should use Windows Server 2016 or Windows Server 2019 releases.  Le versioni precedenti di Windows come Windows 2012 sono tecnicamente supportate, ma è sempre consigliata l'ultima versione di Windows.


## <a name="specifics-to-sap-ase-on-windows"></a>Specifiche per SAP ASE in Windows
Grazie a Microsoft Azure, è possibile eseguire facilmente la migrazione delle applicazioni SAP ASE in macchine virtuali di Azure. L'uso di SAP ASE in una macchina virtuale di Azure consente di ridurre il costo totale di proprietà associato a distribuzione, gestione e manutenzione delle applicazioni aziendali eseguendo facilmente la migrazione di queste applicazioni a Microsoft Azure. Con SAP ASE in una macchina virtuale di Azure, gli amministratori e gli sviluppatori possono continuare a usare gli stessi strumenti di sviluppo e amministrazione disponibili in locale.

Microsoft Azure offre vari tipi di macchina virtuale che consentono di eseguire dai più piccoli panorami applicativi e sistemi SAP ai panorami applicativi e sistemi SAP più grandi che comprendono migliaia di utenti. Il dimensionamento SAP SKU dei diversi SKU delle macchine virtuali certificate SAP viene fornito nella [nota di supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).

La documentazione per l'installazione di SAP ASE in Windows è disponibile nella Guida all'installazione di [SAP ASE per Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Blocca pagine in memoria è un'impostazione che impedirà il paging del buffer del database SAP ASE.  Questa impostazione è utile per i sistemi occupati di grandi dimensioni con molta memoria. Per ulteriori informazioni, contattare BC-DB-SYB. 


## <a name="linux-operating-system-specific-settings"></a>Impostazioni specifiche del sistema operativo Linux
Nelle macchine virtuali `saptune` Linux, eseguire con il profilo SAP-ASE Linux Huge Pages deve essere abilitato per impostazione predefinita e può essere verificato con il comando  

`cat /proc/meminfo` 

La dimensione della pagina è in genere 2048 KB. Per i dettagli vedere l'articolo [Pagine enormi su Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Consigli sulla struttura di macchine virtuali e dischi per le distribuzioni SAP ASERecommendations on VM and disk structure for SAP ASE deployments

SAP ASE for SAP NetWeaver Applications è supportato in qualsiasi tipo di VM elencato in [SAP nota sul supporto](https://launchpad.support.sap.com/#/notes/1928533) #1928533 tipi di VM tipici utilizzati per i server di database SAP ASE di medie dimensioni includono Esv3.  I database multi-terabyte di grandi dimensioni possono sfruttare i tipi di VM serie M.Large multi-terabyte databases can leverage M-series VM types. Le prestazioni di scrittura del disco del log delle transazioni SAP ASE possono essere migliorate abilitando l'acceleratore di scrittura della serie M. L'acceleratore di scrittura deve essere testato attentamente con SAP ASE a causa del modo in cui SAP ASE esegue le scritture di log.  Esaminare [#2816580 nota del supporto SAP](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) e prendere in considerazione l'esecuzione di un test delle prestazioni.  
L'acceleratore di scrittura è progettato solo per il disco del log delle transazioni. La cache a livello di disco deve essere impostata su NONE. Non stupitevi se Acceleratore di scrittura di Azure non mostra miglioramenti simili a quelli di altri DBMS. In base al modo in cui SAP ASE scrive nel log delle transazioni, è possibile che l'accelerazione da parte di Azure Scogni poco.
Sono consigliati dischi separati per i dispositivi dati e i dispositivi di log.  I database di sistema `saptools` sybsecurity e non richiedono dischi dedicati e possono essere posizionati sui dischi contenenti i dati del database SAP e i dispositivi di log 

![Configurazione dell'archiviazione per SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>File system, dimensione dello stripe & bilanciamento i/o 
SAP ASE scrive i dati in sequenza nei dispositivi di archiviazione su disco, a meno che non siano configurati diversamente. Ciò significa che un database SAP ASE vuoto con quattro dispositivi scriverà i dati solo nel primo dispositivo.  Gli altri dispositivi disco verranno scritti solo quando il primo dispositivo è pieno.  È probabile che la quantità di READ e WRITE IO per ogni dispositivo SAP ASE sia diversa. Per bilanciare l'I/O su disco tra tutti i dischi di Azure disponibili, è necessario usare Windows Storage Spaces o Linux LVM2.To balance disk IO across all available Azure disks either Windows Storage Spaces or Linux LVM2 needs to be used. Su Linux, si consiglia di utilizzare il file system XFS per formattare i dischi. La dimensione della stripe LVM deve essere testata con un test delle prestazioni. La dimensione dello stripe di 128 KB è un buon punto di partenza. In Windows, è necessario testare le dimensioni dell'unità di allocazione NTFS (AUS). 64 KB possono essere utilizzati come valore iniziale. 

È consigliabile configurare l'espansione automatica del database come descritto nell'articolo Configurazione dell'espansione automatica dello spazio del [database in SAP Adaptive Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) e nella [nota](https://launchpad.support.sap.com/#/notes/1815695)del supporto SAP #1815695 . 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Esempio di SAP ASE nelle configurazioni della macchina virtuale, del disco e del file system di AzureSample SAP ASE on Azure virtual machine, disk and file system configurations 
I modelli seguenti mostrano configurazioni di esempio per Linux e Windows.The templates below show sample configurations for both Linux and Windows. Prima di confermare la configurazione della macchina virtuale e del disco, assicurarsi che le quote di larghezza di banda di rete e di archiviazione della singola macchina virtuale siano sufficienti per soddisfare i requisiti aziendali. Tenere inoltre presente che diversi tipi di macchine virtuali di Azure hanno un numero massimo di dischi diverso che può essere collegato alla macchina virtuale. Ad esempio, una macchina virtuale di E4s_v3 ha un limite di velocità effettiva di I/O di archiviazione di 48 MB/sec.For example, a E4s_v3 VM has a limit 48 MB/sec storage IO throughput. Se la velocità effettiva di archiviazione richiesta dall'attività di backup del database richiede più di 48 MB/sec, è inevitabile un tipo di macchina virtuale di dimensioni maggiori con una maggiore velocità effettiva della larghezza di banda di archiviazione. Quando si configura archiviazione di Azure, è inoltre necessario tenere presente che soprattutto con l'archiviazione di [Azure Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) la velocità effettiva e le operazioni di I/O al secondo per GB di capacità cambiano. Per altre informazioni su questo argomento, vedere l'articolo [Quali tipi di disco sono disponibili in Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) Le quote per tipi di macchine virtuali di Azure specifici sono documentate nell'articolo Dimensioni delle [macchine virtuali ottimizzate](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) per la memoria e articoli ad essa collegati. 

> [!NOTE]
>  Se un sistema DBMS viene spostato da locale ad Azure, è consigliabile eseguire il monitoraggio nella macchina virtuale e valutare la CPU, la memoria, le operazioni di I/O al secondo e la velocità effettiva di archiviazione. Confrontare i valori di picco osservati con i limiti di quota della macchina virtuale documentati negli articoli citati in precedenza

Gli esempi riportati di seguito sono a scopo illustrativo e possono essere modificati in base alle esigenze individuali. A causa della progettazione di SAP ASE, il numero di dispositivi dati non è critico come con altri database. Il numero di dispositivi dati descritti in questo documento è solo una guida. 

Un esempio di una configurazione per un server di database SAP ASE di piccole dimensioni con una dimensione del database compresa tra 50 GB e 250 GB, ad esempio SAP solution Manager, potrebbe essere simile a

| Configurazione | Windows | Linux | Commenti |
| --- | --- | --- | --- |
| Tipo macchina virtuale | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Rete accelerata | Abilitare | Abilitare | ---|
| Versione SAP ASE | 16.0.03.07 o superiore | 16.0.03.07 o superiore | --- |
| Numero di dispositivi di dati | 4 | 4 | ---|
| N. di dispositivi di log | 1 | 1 | --- |
| Numero di dispositivi temporanei | 1 | 1 | altro per il carico di lavoro SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 o RHEL 7.6 | --- |
| Aggregazione di dischi | Spazi di archiviazione | LVM2 | --- |
| File system | NTFS | Xfs |
| Formattare la dimensione del blocco | richiede il test del carico di lavoro | richiede il test del carico di lavoro | --- |
| - e tipo di dischi dati | Memoria Premium: 2 x P10 (RAID0) | Memoria Premium: 2 x P10 (RAID0)| Cache - Sola lettura |
| - e tipo di dischi di registro | Memoria Premium: 1 x P20  | Memoria Premium: 1 x P20 | Cache - NONE |
| Parametro MAXMemory di ASE | 90% della RAM fisica | 90% della RAM fisica | assumendo una singola istanza |
| Numero di dispositivi di backup | 4 | 4| --- |
| - e tipo di dischi di backup | 1 | 1 | --- |


Un esempio di configurazione per un server di database SAP ASE di medie dimensioni con una dimensione del database compresa tra 250 GB e 750 GB, ad esempio un sistema SAP Business Suite più piccolo, potrebbe essere simile a un sistema SAP Business Suite più piccolo

| Configurazione | Windows | Linux | Commenti |
| --- | --- | --- | --- |
| Tipo macchina virtuale | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Rete accelerata | Abilitare | Abilitare | ---|
| Versione SAP ASE | 16.0.03.07 o superiore | 16.0.03.07 o superiore | --- |
| Numero di dispositivi di dati | 8 | 8 | ---|
| N. di dispositivi di log | 1 | 1 | --- |
| Numero di dispositivi temporanei | 1 | 1 | altro per il carico di lavoro SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 o RHEL 7.6 | --- |
| Aggregazione di dischi | Spazi di archiviazione | LVM2 | --- |
| File system | NTFS | Xfs |
| Formattare la dimensione del blocco | richiede il test del carico di lavoro | richiede il test del carico di lavoro | --- |
| - e tipo di dischi dati | Memoria Premium: 4 x P20 (RAID0) | Memoria Premium: 4 x P20 (RAID0)| Cache - Sola lettura |
| - e tipo di dischi di registro | Memoria Premium: 1 x P20  | Memoria Premium: 1 x P20 | Cache - NONE |
| Parametro MAXMemory di ASE | 90% della RAM fisica | 90% della RAM fisica | assumendo una singola istanza |
| Numero di dispositivi di backup | 4 | 4| --- |
| - e tipo di dischi di backup | 1 | 1 | --- |

Un esempio di una configurazione per un server di database SAP ASE di piccole dimensioni con una dimensione del database compresa tra 750 GB e 2000 GB, ad esempio un sistema SAP Business Suite più grande, potrebbe essere simile a un sistema SAP Business Suite

| Configurazione | Windows | Linux | Commenti |
| --- | --- | --- | --- |
| Tipo macchina virtuale | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Rete accelerata | Abilitare | Abilitare | ---|
| Versione SAP ASE | 16.0.03.07 o superiore | 16.0.03.07 o superiore | --- |
| Numero di dispositivi di dati | 16 | 16 | ---|
| N. di dispositivi di log | 1 | 1 | --- |
| Numero di dispositivi temporanei | 1 | 1 | altro per il carico di lavoro SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 o RHEL 7.6 | --- |
| Aggregazione di dischi | Spazi di archiviazione | LVM2 | --- |
| File system | NTFS | Xfs |
| Formattare la dimensione del blocco | richiede il test del carico di lavoro | richiede il test del carico di lavoro | --- |
| - e tipo di dischi dati | Memoria Premium: 4 x P30 (RAID0) | Memoria Premium: 4 x P30 (RAID0)| Cache - Sola lettura |
| - e tipo di dischi di registro | Memoria Premium: 1 x P20  | Memoria Premium: 1 x P20 | Cache - NONE |
| Parametro MAXMemory di ASE | 90% della RAM fisica | 90% della RAM fisica | assumendo una singola istanza |
| Numero di dispositivi di backup | 4 | 4| --- |
| - e tipo di dischi di backup | 1 | 1 | --- |


Un esempio di una configurazione per un server di database SAP ASE di piccole dimensioni con una dimensione del database di 2 TB, ad esempio un sistema SAP Business Suite utilizzato a livello globale più grande, potrebbe essere simile a

| Configurazione | Windows | Linux | Commenti |
| --- | --- | --- | --- |
| Tipo macchina virtuale | Serie M (da 1,0 a 4,0 TB di RAM)  | Serie M (da 1,0 a 4,0 TB di RAM) | --- |
| Rete accelerata | Abilitare | Abilitare | ---|
| Versione SAP ASE | 16.0.03.07 o superiore | 16.0.03.07 o superiore | --- |
| Numero di dispositivi di dati | 32 | 32 | ---|
| N. di dispositivi di log | 1 | 1 | --- |
| Numero di dispositivi temporanei | 1 | 1 | altro per il carico di lavoro SAP BW |
| Sistema operativo | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 o RHEL 7.6 | --- |
| Aggregazione di dischi | Spazi di archiviazione | LVM2 | --- |
| File system | NTFS | Xfs |
| Formattare la dimensione del blocco | richiede il test del carico di lavoro | richiede il test del carico di lavoro | --- |
| - e tipo di dischi dati | Memoria Premium: 4x P30 (RAID0) | Memoria Premium: 4x P30 (RAID0)| Cache : sola lettura, considera il disco Azure Ultra |
| - e tipo di dischi di registro | Memoria Premium: 1 x P20  | Memoria Premium: 1 x P20 | Cache : NONE, Considera il disco Azure Ultra |
| Parametro MAXMemory di ASE | 90% della RAM fisica | 90% della RAM fisica | assumendo una singola istanza |
| Numero di dispositivi di backup | 16 | 16 | --- |
| - e tipo di dischi di backup | 4 | 4 | Utilizzare LVM2/Storage Spaces |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Backup & restore considerations for SAP ASE on Azure
L'aumento del numero di dati e dispositivi di backup aumenta le prestazioni di backup e ripristino. È consigliabile eseguire lo striping dei dischi di Azure che ospitano il dispositivo di backup SAP ASE come illustrato nelle tabelle illustrate in precedenza. È necessario prestare attenzione per bilanciare il numero di dispositivi e dischi di backup e garantire che la velocità effettiva di backup non superi il 40%-50% della quota totale di velocità effettiva della macchina virtuale. Si consiglia di utilizzare SAP Backup Compression come impostazione predefinita. Maggiori dettagli possono essere trovati negli articoli:

- [Nota del supporto SAP #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [nota di supporto SAP #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [Nota del supporto SAP #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Non utilizzare l'unità D: o /temp come destinazione di dump del database o del log.

### <a name="impact-of-database-compression"></a>Impatto della compressione del database
In configurations where I/O bandwidth can become a limiting factor, measures, which reduce IOPS might help to stretch the workload one can run in an IaaS scenario like Azure. È quindi consigliabile verificare che venga usata la compressione SAP ASE prima di caricare un database SAP esistente in Azure.

I motivi per cui è consigliabile comprimere il database prima di caricarlo in Azure sono molti:

* La quantità di dati da caricare in Azure è minore
* La durata dell'esecuzione della compressione è inferiore, presupponendo che si possa usare hardware più avanzato con più CPU o una maggiore larghezza di banda di I/O oppure meno latenza di I/O in locale
* L'uso di database di dimensioni inferiori può contribuire a ridurre i costi per l'allocazione dei dischi

Il funzionamento della compressione dati e LOB in una VM ospitata in macchine virtuali di Azure è uguale a quello in locale. Per ulteriori informazioni su come verificare se la compressione è già in uso in un database SAP ASE esistente, vedere nota supporto [SAP 1750510](https://launchpad.support.sap.com/#/notes/1750510). Per ulteriori informazioni sulla compressione del database SAP ASE, vedere [Nota del supporto SAP #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Disponibilità elevata di SAP ASE in AzureHigh availability of SAP ASE on Azure 
La Guida per gli utenti HADR descrive in dettaglio l'installazione e la configurazione di una soluzione SAP ASE "Always-on" a 2 nodi.  Inoltre, è supportato anche un terzo nodo di ripristino di emergenza. SAP ASE supporta molte configurazioni High Available, tra cui il disco condiviso e il clustering nativo del sistema operativo (IP mobile). L'unica configurazione supportata in Azure è l'uso di Gestione errori senza IP mobile.  Il metodo Floating IP Address non funzionerà in Azure.The Floating IP Address method will not work on Azure.  SAP Kernel è un'applicazione "HA Aware" e conosce i server SAP ASE primari e secondari. Non esistono integrazioni ravvicinate tra SAP ASE e Azure, il servizio di bilanciamento del carico interno di Azure non viene usato. Pertanto, la documentazione standard DI SAP ASE deve essere seguita a partire da [SAP ASE HADR Users Guide](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> L'unica configurazione supportata in Azure è l'uso di Gestione errori senza IP mobile.  Il metodo Floating IP Address non funzionerà in Azure.The Floating IP Address method will not work on Azure. 

### <a name="third-node-for-disaster-recovery"></a>Terzo nodo per il ripristino di emergenza
Oltre a usare SAP ASE Always-On per la disponibilità elevata locale, è possibile estendere la configurazione a un nodo replicato in modo asincrono in un'altra area di Azure.Beyond using SAP ASE Always-On for local high availability, you might want to extend the configuration to an asynchronously replicated node in another Azure region. La documentazione per tale scenario può essere trovata [qui](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>Crittografia del database SAP ASE & SSL 
SAP Software provisioning Manager (SWPM) offre un'opzione per crittografare il database durante l'installazione.  Se si desidera utilizzare la crittografia, è consigliabile usare la crittografia completa del database SAP.  Vedere i dettagli documentati in:

- [#2556658 nota del supporto SAP](https://launchpad.support.sap.com/#/notes/2556658)
- [Nota #2224138 del supporto SAP](https://launchpad.support.sap.com/#/notes/2224138)
- [Nota del supporto SAP #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [#2593925 nota di supporto SAP](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Se un database SAP ASE è crittografato, la compressione del dump di backup non funzionerà. Vedere anche [NOTa del supporto SAP #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Elenco di controllo per la distribuzione di SAP ASE in Azure
 
- Distribuire SAP ASE 16.0.03.07 o versione successiva
- Aggiornamento alla versione più recente e alle patch di FaultManager e SAPHostAgent
- Distribuzione nel sistema operativo certificato più recente disponibile, ad esempio Windows 2019, Suse 15.1 o Redhat 7.6 o versione successiva
- Usare macchine virtuali certificate SAP: sono consigliate sKU di macchine virtuali di Azure ad alta memoria, ad esempio Es_v3 o per sistemi x-large, sKU di VM serie M
- Abbinare le operazioni di I/O al secondo del disco e la quota totale di velocità effettiva aggregata della macchina virtuale con la progettazione del disco.  Distribuire un numero sufficiente di dischiDeploy sufficient number of disks
- Dischi aggregati utilizzando Windows Storage Spaces o Linux LVM2 con dimensioni di striping e file system corretti
- Creare un numero sufficiente di dispositivi per scopi di dati, log, temp e backup
- Considerare l'utilizzo di UltraDisk per sistemi x-large 
- Eseguire `saptune` SAP-ASE su Linux OS 
- Proteggere il database con Crittografia database: archiviare manualmente le chiavi nell'insieme di credenziali delle chiavi di AzureSecure the database with DB Encryption – manually store keys in Azure Key Vault 
- Completare [l'elenco di controllo di SAP in AzureComplete](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) the SAP on Azure Checklist 
- Configurare il backup del log e il backup completo 
- Test HA/DR, backup e ripristino ed esecuzione del test di & volume di sollecitazione 
- Verificare che l'estensione automatica del database funzioni 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizzo di DBACockpit per monitorare le istanze di databaseUsing DBACockpit to monitor database instances
Per i sistemi SAP che usano SAP ASE come piattaforma di database, DBACockpit è accessibile come finestra del browser incorporata nella transazione DBACockpit o come Webdynpro. Tuttavia, la funzionalità completa per il monitoraggio e l'amministrazione del database è disponibile solo nell'implementazione Webdynpro di DBACockpit.

Come per i sistemi locali, sono necessari diversi passaggi per abilitare tutte le funzionalità di SAP NetWeaver usate dall'implementazione Webdynpro di DBACockpit. Seguire la [nota](https://launchpad.support.sap.com/#/notes/1245200) del supporto SAP #1245200 per abilitare l'utilizzo di webdynpros e generare quelli necessari. Quando si seguono le istruzioni riportate nelle note`ICM`precedenti, si configura anche Gestione comunicazioni Internet ( ) insieme alle porte da utilizzare per le connessioni http e https. L'impostazione predefinita per HTTP è simile alla seguente:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

I collegamenti generati nella transazione DBACockpit sono simili ai seguenti:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

A seconda del modo in cui la macchina virtuale di Azure ospitante il sistema SAP è connessa ad Active Directory e DNS, è necessario verificare che ICM usi un nome host completo che può essere risolto nel computer da cui si sta aprendo DBACockpit. Vedere [#773830 nota](https://launchpad.support.sap.com/#/notes/773830) del supporto SAP per comprendere in che modo ICM determina il nome host completo in base ai parametri del profilo e impostare il parametro icm/host_name_full in modo esplicito, se necessario.

Se la macchina virtuale è stata distribuita in uno scenario solo cloud senza connettività cross-premise tra `domainlabel`l'ambiente locale e Azure, è necessario definire un indirizzo IP pubblico e un file . Il formato del nome DNS pubblico della VM si presenta come segue:

> `<custom domainlabel`&gt;.`<azure region`&gt;.cloudapp.azure.com
> 
> 

Ulteriori dettagli relativi al nome DNS sono disponibili [qui][virtual-machines-azurerm-versus-azuresm].

Impostando il parametro del profilo SAP icm/host_name_full sul nome DNS della VM di Azure, il collegamento sarà simile a:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

In questo caso è necessario assicurarsi di:

* Aggiungere regole in ingresso al gruppo di sicurezza di rete nel portale di Azure per le porte TCP/IP usate per comunicare con ICM
* Aggiungere regole in ingresso alla configurazione di Windows Firewall per le porte TCP/IP usate per comunicare con ICM

Per importare automaticamente tutte le correzioni disponibili, è consigliabile applicare periodicamente la nota SAP della raccolta di correzioni applicabile alla propria versione di SAP:

* [Note di supporto SAP #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [Nota del supporto SAP #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [#1882376 nota sul supporto SAP](https://launchpad.support.sap.com/#/notes/1882376)

Per altre informazioni su DBA Cockpit per SAP ASE, vedere le note SAP seguenti:

* [#1605680 nota del supporto SAP](https://launchpad.support.sap.com/#/notes/1605680)
* [Nota del supporto SAP #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [Nota sul supporto SAP #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [#1758182 nota di supporto SAP](https://launchpad.support.sap.com/#/notes/1758182)
* [Nota #1758496 del supporto SAP](https://launchpad.support.sap.com/#/notes/1758496)    
* [Nota #1814258 del supporto SAP](https://launchpad.support.sap.com/#/notes/1814258)
* [Nota #1922555 del supporto SAP](https://launchpad.support.sap.com/#/notes/1922555)
* [Nota #1956005 del supporto SAP](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Link utili, note & whitepaper per SAP ASE
La pagina iniziale di [SAP ASE 16.0.03.07 Documentation](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) fornisce collegamenti a vari documenti di cui i documenti di:

- Percorso di apprendimento SAP ASE - Amministrazione & monitoraggio
- Percorso di apprendimento SAP ASE - Installazione & aggiornamento

sono utili. Un altro documento utile è [applicazioni SAP in SAP Adaptive Server Enterprise Best Practices for Migration and Runtime](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Altre note utili sul supporto SAP sono:

- [Note del supporto SAP #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [#1748888 nota del supporto SAP](https://launchpad.support.sap.com/#/notes/1748888) 
- [Nota del supporto SAP #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [Nota del supporto SAP #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [#1724091 nota del supporto SAP](https://launchpad.support.sap.com/#/notes/1724091) 
- [Nota del supporto SAP #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [#2162183 nota sul supporto SAP](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota del supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [Nota del supporto SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [Nota #1750510 del supporto SAP](https://launchpad.support.sap.com/#/notes/1750510) 
- [Nota del supporto SAP #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [#2162183 nota sul supporto SAP](https://launchpad.support.sap.com/#/notes/2162183) 
- [Nota del supporto SAP #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Altre informazioni sono pubblicate su 

- [Applicazioni SAP nell'azienda di server adattivi SAP](https://community.sap.com/topics/applications-on-ase)
- [Infocenter SAP ASE](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Sempre in uso con l'installazione del terzo nodo DI ripristino di emergenza](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Una newsletter mensile viene pubblicata tramite [la nota](https://launchpad.support.sap.com/#/notes/2381575) di supporto SAP #2381575 


## <a name="next-steps"></a>Passaggi successivi
Controllare l'articolo Carichi di lavoro SAP in Azure: elenco di controllo per la pianificazione e la distribuzioneCheck the article [SAP workloads on Azure: planning and deployment checklist](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

