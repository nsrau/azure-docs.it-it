---
title: Disponibilità elevata di SAP HANA la scalabilità verticale con e in RHEL | Microsoft Docs
description: Stabilire la disponibilità elevata di SAP HANA con e in macchine virtuali (VM) di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2020
ms.author: radeltch
ms.openlocfilehash: 030677276fa077c06a95e7c677fec956b9c2a947
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556317"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Disponibilità elevata di SAP HANA scalabilità verticale con Azure NetApp Files in Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Questo articolo descrive come configurare la replica di sistema SAP HANA in una distribuzione con scalabilità verticale, quando i file System HANA sono montati tramite NFS, usando Azure NetApp Files (e). Nelle configurazioni e nei comandi di installazione di esempio, vengono usati il numero di istanza **03**e l'ID di sistema Hana **HN1** . La replica SAP HANA è costituita da un nodo primario e da almeno un nodo secondario.

Quando i passaggi descritti in questo documento sono contrassegnati con i prefissi seguenti, il significato è il seguente:

- **[A]**: il passaggio si applica a tutti i nodi
- **[1]**: il passaggio si applica solo a node1
- **[2]**: il passaggio si applica solo a node2
 
Leggere prima di tutto i documenti e le note SAP seguenti:

- Nota SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533), contenente:
    - Elenco delle dimensioni delle macchine virtuali di Azure supportate per la distribuzione di software SAP.
    - Informazioni importanti sulla capacità per le dimensioni delle macchine virtuali di Azure.
    - Software SAP e combinazioni di sistemi operativi e database supportati.
    - Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure.
- La nota SAP [2015553](https://launchpad.support.sap.com/#/notes/2015553) elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
- La nota SAP [405827](https://launchpad.support.sap.com/#/notes/405827) elenca le file System consigliate per l'ambiente Hana.
- La nota SAP [2002167](https://launchpad.support.sap.com/#/notes/2002167) include impostazioni del sistema operativo consigliate per Red Hat Enterprise Linux.
- La nota SAP [2009879](https://launchpad.support.sap.com/#/notes/2009879) contiene linee guida SAP HANA per Red Hat Enterprise Linux.
- La nota SAP [2178632](https://launchpad.support.sap.com/#/notes/2178632) contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
- La nota SAP [2191498](https://launchpad.support.sap.com/#/notes/2191498) contiene la versione dell'agente host SAP per Linux necessaria in Azure.
- La nota SAP [2243692](https://launchpad.support.sap.com/#/notes/2243692) contiene informazioni sulle licenze SAP in Linux in Azure.
- La nota SAP [1999351](https://launchpad.support.sap.com/#/notes/1999351) contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
- Il [wiki della community SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) include tutte le note SAP necessarie per Linux.
- [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
- [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
- [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
- [SAP HANA la replica di sistema nel cluster pacemaker.](https://access.redhat.com/articles/3004101)
- Documentazione generale di RHEL
    - [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
    - [Amministrazione componenti aggiuntivi a disponibilità elevata.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Informazioni di riferimento sul componente aggiuntivo per la disponibilità elevata.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Configurare SAP HANA la replica di sistema in una scalabilità verticale in un cluster pacemaker quando i filesystem HANA si trovano su condivisioni NFS](https://access.redhat.com/solutions/5156571)
- Documentazione di RHEL specifica di Azure:
    - [Criteri di supporto per i cluster RHEL a disponibilità elevata Macchine virtuali di Microsoft Azure come membri del cluster.](https://access.redhat.com/articles/3131341)
    - [Installazione e configurazione di un cluster a disponibilità elevata Red Hat Enterprise Linux 7,4 (e versioni successive) su Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Installare SAP HANA in Red Hat Enterprise Linux per l'uso in Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Configurare SAP HANA la replica del sistema con scalabilità verticale su un cluster pacemaker quando il file System HANA si trova in condivisioni NFS](https://access.redhat.com/solutions/5156571)
- [Applicazioni NetApp SAP su Microsoft Azure con Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)

## <a name="overview"></a>Panoramica

Tradizionalmente nell'ambiente con scalabilità verticale tutti i file System per SAP HANA vengono montati dalla risorsa di archiviazione locale. La configurazione della disponibilità elevata della replica di sistema SAP HANA in Red Hat Enterprise Linux è pubblicata nella Guida [configurare SAP Hana la replica di sistema in RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)

Per ottenere SAP HANA disponibilità elevata del sistema di scalabilità verticale nelle condivisioni NFS [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/) , è necessaria una configurazione aggiuntiva delle risorse nel cluster, per consentire il ripristino delle risorse Hana, quando un nodo perde l'accesso alle condivisioni NFS in e.  Il cluster gestisce i montaggi NFS, consentendo il monitoraggio dell'integrità delle risorse. Vengono applicate le dipendenze tra il file system i montaggi e le risorse SAP HANA.  

![SAP HANA con scalabilità verticale in e](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA i filesystem sono montati in condivisioni NFS usando Azure NetApp Files in ogni nodo. I file System/Hana/data,/Hana/log e/Hana/Shared sono univoci per ogni nodo. 

Montato in Node1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 su/Hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 su/Hana/log
- 10.32.2.4:/**hanadb1**-Shared-mnt00001 su/Hana/Shared

Montato in Node2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 su/Hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 su/Hana/log
- 10.32.2.4:/**hanadb2**-Shared-mnt00001 su/Hana/Shared

> [!NOTE]
> I file System/Hana/Shared,/Hana/data e/Hana/log non sono condivisi tra i due nodi. Ogni nodo del cluster dispone di file System distinti.   

La configurazione della replica di sistema SAP HANA usa un nome host virtuale dedicato e indirizzi IP virtuali. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. L'elenco seguente mostra la configurazione del servizio di bilanciamento del carico:

- Configurazione front-end: indirizzo IP 10.32.0.10 per HN1-DB
- Configurazione back-end: Connessione a interfacce di rete primarie di tutte le macchine virtuali che devono far parte della replica del sistema HANA
- Porta probe: Porta 62503
- Regole di bilanciamento del carico: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (se si usa il servizio di bilanciamento del carico di Azure Basic)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Configurare l'infrastruttura file di Azure NetApp

Prima di procedere con la configurazione per l'infrastruttura Azure NetApp Files, acquisire familiarità con la [documentazione dei file](https://docs.microsoft.com/azure/azure-netapp-files/)di Azure NetApp.

Azure NetApp Files è disponibile in diverse [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Verificare se l'area di Azure selezionata offre Azure NetApp Files.

Per informazioni sulla disponibilità di Azure NetApp Files dall'area di Azure, vedere [Azure NetApp files disponibilità per area di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all).

Prima di distribuire Azure NetApp Files, richiedere l'onboarding per Azure NetApp Files eseguendo [la registrazione per istruzioni Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).

### <a name="deploy-azure-netapp-files-resources"></a>Distribuire le risorse di Azure NetApp Files

Le istruzioni seguenti presuppongono che la [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)sia già stata distribuita. Le risorse Azure NetApp Files e le macchine virtuali in cui verranno montate le risorse di Azure NetApp Files devono essere distribuite nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering.

1. Se le risorse non sono già state distribuite, richiedere l' [onboarding per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).

2. Per creare un account NetApp nell'area di Azure selezionata, seguire le istruzioni riportate in [creare un account NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).

3.  Configurare un pool di capacità Azure NetApp Files seguendo le istruzioni riportate in [configurare un pool di capacità Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).

    L'architettura HANA presentata in questo articolo usa un singolo pool di capacità Azure NetApp Files al livello di servizio *ultra* . Per i carichi di lavoro HANA in Azure, è consigliabile usare un [livello di servizio](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)Azure NetApp files *ultra* o *Premium* .

4.  Delegare una subnet a Azure NetApp Files, come descritto nelle istruzioni in [delegare una subnet a Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).

5.  Distribuire Azure NetApp Files volumi seguendo le istruzioni riportate in [creare un volume NFS per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).

    Quando si distribuiscono i volumi, assicurarsi di selezionare la versione NFSv 4.1. Distribuire i volumi nella subnet di Azure NetApp Files designata. Gli indirizzi IP dei volumi di Azure NetApp vengono assegnati automaticamente.

    Tenere presente che le risorse Azure NetApp Files e le macchine virtuali di Azure devono trovarsi nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering. Ad esempio, hanadb1-data-mnt00001, hanadb1-log-mnt00001 e così via, sono i nomi di volume e nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 e così via, sono i percorsi di file per i volumi Azure NetApp Files.
    
    In **hanadb1**

    - Volume hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-Shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    In **hanadb2**

    - Volume hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-Shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Considerazioni importanti

Quando si crea la Azure NetApp Files per SAP HANA sistemi di scalabilità verticale, tenere presente quanto segue:

- Il pool di capacità minimo è 4 TB (TiB).
- La dimensione minima del volume è 100 gibibyte (GiB).
- Azure NetApp Files e tutte le macchine virtuali in cui verranno montati i volumi di Azure NetApp Files devono trovarsi nella stessa rete virtuale di Azure o in [reti virtuali con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nella stessa area.
- La rete virtuale selezionata deve avere una subnet delegata a Azure NetApp Files.
- La velocità effettiva di un volume Azure NetApp Files è una funzione della quota del volume e del livello di servizio, come documentato nel [livello di servizio per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Quando si ridimensionano i volumi di Azure NetApp di HANA, assicurarsi che la velocità effettiva risultante soddisfi i requisiti di sistema HANA.
- Con i [criteri di esportazione](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)Azure NetApp files, è possibile controllare i client consentiti, il tipo di accesso (lettura/scrittura, sola lettura e così via).
- La funzionalità Azure NetApp Files non è ancora in grado di riconoscere la zona. Attualmente, la funzionalità non è distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni di latenza potenziali in alcune aree di Azure.

> [!IMPORTANT]
> Per carichi di lavoro SAP HANA, è fondamentale una bassa latenza. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi Azure NetApp Files vengano distribuiti in prossimità.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Dimensionamento del database HANA in Azure NetApp Files

La velocità effettiva di un volume Azure NetApp Files è una funzione delle dimensioni del volume e del livello di servizio, come documentato nel [livello di servizio per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels).

Quando si progetta l'infrastruttura per SAP in Azure, tenere presenti alcuni requisiti minimi di archiviazione da parte di SAP, che si traduce in caratteristiche di velocità effettiva minime:

- Lettura/scrittura su/Hana/log di 250 megabyte al secondo (MB/s) con dimensioni di I/O di 1 MB.
- Attività di lettura di almeno 400 MB/s per/Hana/data per le dimensioni di I/O di 16 MB e 64 MB.
- Attività di scrittura di almeno 250 MB/s per/Hana/data con dimensioni di I/O di 16 MB e 64 MB.

I [limiti di velocità effettiva di Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TiB della quota del volume sono:

- Livello di archiviazione Premium-64 MiB/s.
- Livello di archiviazione Ultra-128 MiB/s.

Per soddisfare i requisiti di velocità effettiva minima SAP per/Hana/data e/Hana/log e le linee guida per/Hana/Shared, le dimensioni consigliate sono:

|    Volume    | Dimensioni del livello di archiviazione Premium | Dimensioni del livello di archiviazione Ultra | Protocollo NFS supportato |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 o v4.1    |


> [!NOTE]
> Le indicazioni relative al dimensionamento Azure NetApp Files indicate di seguito sono destinate a soddisfare i requisiti minimi consigliati da SAP per i provider di infrastruttura. Nelle distribuzioni reali dei clienti e negli scenari di carico di lavoro, queste dimensioni potrebbero non essere sufficienti. Considerare quindi queste indicazioni come punto di inizio e adattarle in base ai requisiti del carico di lavoro specifico.

> [!TIP]
> È possibile ridimensionare Azure NetApp Files volumi in modo dinamico, senza dover *smontare* i volumi, arrestare le macchine virtuali o arrestare SAP Hana. Questo approccio offre la flessibilità necessaria per soddisfare le esigenze di velocità effettiva prevista e non prevista dell'applicazione.

> [!NOTE]
> Tutti i comandi per montare/Hana/Shared in questo articolo sono presentati per i volumi NFSv 4.1/Hana/Shared.
> Se i volumi/Hana/Shared sono stati distribuiti come volumi NFSv3, non dimenticare di modificare i comandi di montaggio per/Hana/Shared per NFSv3.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Distribuire una macchina virtuale Linux tramite portale di Azure 

Per prima cosa è necessario creare i volumi Azure NetApp Files. Eseguire quindi i passaggi seguenti:

1.  Creare un gruppo di risorse.
2.  Creare una rete virtuale.
3.  Creare un set di disponibilità. Impostare il numero massimo di domini di aggiornamento.
4.  Creare un servizio di bilanciamento del carico (interno). Si consiglia di usare Load Balancer Standard.
    Selezionare la rete virtuale creata nel passaggio 2.
5.  Creazione della macchina virtuale 1 (**hanadb1**). 
6.  Creazione della macchina virtuale 2 (**hanadb2**).  
7.  Durante la creazione della macchina virtuale, non verrà aggiunto alcun disco, perché tutti i punti di montaggio si troveranno sulle condivisioni NFS da Azure NetApp Files. 
8.  Se si usa Load Balancer standard, seguire questa procedura di configurazione:
    1.  Prima, creare il pool di indirizzi IP front-end:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Pool di indirizzi IP front-end** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, **hana-frontend**).
        1.  Impostare l' **assegnazione** su **statico** e immettere l'indirizzo IP (ad esempio, **10.32.0.10**).
        1.  Selezionare **OK**.
        1.  Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.
    1.  Creare quindi un pool back-end:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Pool back-end** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo pool back-end (ad esempio, **hana-backend**).
        1.  Selezionare **Aggiungi una macchina virtuale**.
        1.  Selezionare **Macchina virtuale**.
        1.  Selezionare le macchine virtuali del cluster SAP HANA e il rispettivo indirizzo IP.
        1.  Selezionare **Aggiungi**.
    1.  Creare quindi un probe di integrità:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Probe integrità** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo probe di integrità (ad esempio, **hana-hp**).
        1.  Selezionare TCP come protocollo e la porta 625**03**. Lasciare il valore di **Intervallo** impostato su 5 e il valore di **Soglia di non integrità** impostato su 2.
        1.  Selezionare **OK**.
    1.  Successivamente, creare le regole del servizio di bilanciamento del carico:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
        1.  Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, **hana-lb**).
        1.  Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**, **hana-backend** e **hana-hp**).
        1.  Selezionare **Porte a disponibilità elevata**.
        1.  Aumentare il valore di **Timeout di inattività** a 30 minuti.
        1.  Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
        1.  Selezionare **OK**.

> [!NOTE] 
> Quando le macchine virtuali senza indirizzi IP pubblici vengono inserite nel pool back-end del Load Balancer interno standard di Azure (nessun indirizzo IP pubblico), non vi sarà connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).

9. In alternativa, se lo scenario prevede l'uso del servizio di bilanciamento del carico di base, seguire questa procedura di configurazione:
    1.  Configurare il servizio di bilanciamento del carico. Prima, creare il pool di indirizzi IP front-end:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Pool di indirizzi IP front-end** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, **hana-frontend**).
        1.  Impostare l' **assegnazione** su **statico** e immettere l'indirizzo IP (ad esempio, **10.32.0.10**).
        1.  Selezionare **OK**.
        1.  Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.
    1.  Creare quindi un pool back-end:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Pool back-end** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo pool back-end (ad esempio, **hana-backend**).
        1.  Selezionare **Aggiungi una macchina virtuale**.
        1.  Selezionare il set di disponibilità creato nel passaggio 3.
        1.  Selezionare le macchine virtuali del cluster SAP HANA.
        1.  Selezionare **OK**.
    1.  Creare quindi un probe di integrità:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Probe integrità** e quindi **Aggiungi**.
        1.  Immettere il nome del nuovo probe di integrità (ad esempio, **hana-hp**).
        1.  Selezionare **TCP** come protocollo e la porta 625**03**. Lasciare il valore di **Intervallo** impostato su 5 e il valore di **Soglia di non integrità** impostato su 2.
        1.  Selezionare **OK**.
    1.  Per SAP HANA 1.0, creare le regole di bilanciamento del carico:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
        1.  Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3**03**15).
        1.  Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**).
        1.  Lasciare il valore di **Protocollo** impostato su **TCP** e immettere la porta 3**03**15.
        1.  Aumentare il valore di **Timeout di inattività** a 30 minuti.
        1.  Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
        1.  Selezionare **OK**.
        1.  Ripetere questi passaggi per la porta 3**03**17.
    1.  Per SAP HANA 2.0, creare le regole di bilanciamento del carico per il database di sistema:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
        1.  Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3**03**13).
        1.  Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**).
        1.  Lasciare il valore di **Protocollo** impostato su **TCP** e immettere la porta 3**03**13.
        1.  Aumentare il valore di **Timeout di inattività** a 30 minuti.
        1.  Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
        1.  Selezionare **OK**.
        1.  Ripetere questi passaggi per la porta 3**03**14.
    1.  Per SAP HANA 2.0, creare prima le regole di bilanciamento del carico per il database tenant:
        1.  Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
        1.  Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, hana-lb-3**03**40).
        1.  Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**).
        1.  Lasciare il valore di **Protocollo** impostato su **TCP** e immettere la porta 3**03**40.
        1.  Aumentare il valore di **Timeout di inattività** a 30 minuti.
        1.  Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
        1.  Selezionare **OK**.
        1.  Ripetere questi passaggi per le porte 3**03**41 e 3**03**42.

Per ulteriori informazioni sulle porte necessarie per SAP HANA, vedere il capitolo [connessioni ai database tenant](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) nella Guida ai [database tenant di SAP Hana](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) o la nota SAP [2388694](https://launchpad.support.sap.com/#/notes/2388694).

> [!IMPORTANT]
> Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net.ipv4.tcp_timestamps** su **0**. Per informazioni dettagliate, vedere [Probe di integrità di Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview). Vedere anche la nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Montare il volume Azure NetApp Files

1. **[A]** creare punti di montaggio per i volumi del database Hana. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** verificare l'impostazione del dominio NFS. Verificare che il dominio sia configurato come predefinito Azure NetApp Files dominio, ad esempio **defaultv4iddomain.com** e che il mapping sia impostato su **nessuno**.

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Assicurarsi di impostare il dominio NFS in/etc/idmapd.conf nella macchina virtuale in modo che corrisponda alla configurazione del dominio predefinito su Azure NetApp Files: **defaultv4iddomain.com**. Se si verifica una mancata corrispondenza tra la configurazione del dominio nel client NFS (ovvero la macchina virtuale) e il server NFS, ad esempio la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi NetApp di Azure montate nelle VM verranno visualizzate come nessuno.
    

3. **[1]** montare i volumi specifici del nodo in Node1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** montare i volumi specifici del nodo in Node2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** verificare che tutti i volumi Hana siano montati con il protocollo NFS versione NFSv4.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** verificare **nfs4_disable_idmapping**. Deve essere impostato su **Y**. Per creare la struttura di directory in cui si trova **nfs4_disable_idmapping** , eseguire il comando mount. Non sarà possibile creare manualmente la directory in/sys/modules, perché l'accesso è riservato per il kernel/driver.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Per ulteriori informazioni su come modificare **nfs_disable_idmapping** parametro, vedere [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>Installazione di SAP HANA

1. **[T]** Configurare la risoluzione dei nomi host per tutti gli host.

   È possibile usare un server DNS o modificare il file /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts. Sostituire l'indirizzo IP e il nome host nei comandi seguenti:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL per la configurazione Hana

   Configurare RHEL come descritto nella nota SAP seguente basata sulla versione di RHEL

   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7)
   - [2777782-SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: esecuzione di applicazioni SAP compilate con GCC 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: esecuzione di applicazioni SAP compilate con GCC 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: esecuzione di applicazioni SAP compilate con GCC 9. x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[T]** Installare SAP HANA

   Avviato con HANA 2,0 SPS 01, MDC è l'opzione predefinita. Quando si installa HANA System, SYSTEMDB e un tenant con lo stesso SID verranno creati insieme. In alcuni casi non si vuole usare il tenant predefinito. In caso contrario, se non si vuole creare un tenant iniziale insieme all'installazione, è possibile seguire la nota SAP [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Eseguire il programma **hdblcm** dal DVD di HANA. Immettere i valori seguenti al prompt:  
    Scegliere l'installazione: immettere **1** (per l'installazione)  
    Selezionare i componenti aggiuntivi per l'installazione: Immettere **1**.  
    Immettere il percorso di installazione [/Hana/Shared]: premere INVIO per accettare il valore predefinito  
    Immettere il nome host locale [..]: premere INVIO per accettare il valore predefinito  
    Aggiungere altri host al sistema? (s/n) [n]: **n**  
    Immettere SAP HANA ID sistema: immettere **HN1**.  
    Immettere il numero di istanza [00]: immettere **03**  
    Selezione modalità database/immettere l'indice [1]: premere INVIO per accettare il valore predefinito  
    Seleziona utilizzo sistema/immettere l'indice [4]: immettere **4** (per personalizzato)  
    Immettere il percorso dei volumi di dati [/Hana/data]: premere INVIO per accettare il valore predefinito  
    Immettere il percorso dei volumi di log [/Hana/log]: premere INVIO per accettare il valore predefinito  
    Limitare l'allocazione massima della memoria? [n]: premere INVIO per accettare il valore predefinito  
    Immettere il nome host del certificato per l'host '.. .' [...]: premere INVIO per accettare il valore predefinito  
    Immettere la password dell'utente agente host SAP (sapadm): immettere la password utente dell'agente host  
    Confermare la password dell'utente agente host SAP (sapadm): immettere di nuovo la password dell'utente dell'agente host per confermare  
    Immettere la password dell'amministratore di sistema (hn1adm): immettere la password dell'amministratore di sistema  
    Confermare la password dell'amministratore di sistema (hn1adm): immettere di nuovo la password dell'amministratore di sistema per confermare  
    Immettere la home directory dell'amministratore di sistema [/usr/sap/HN1/home]: premere INVIO per accettare il valore predefinito  
    Immettere la shell di accesso dell'amministratore di sistema [/bin/sh]: premere INVIO per accettare il valore predefinito  
    Immettere l'ID utente dell'amministratore di sistema [1001]: premere INVIO per accettare il valore predefinito  
    Immettere l'ID del gruppo utenti (sapsys) [79]: premere INVIO per accettare il valore predefinito  
    Immettere la password dell'utente del database (SYSTEM): immettere la password dell'utente del database  
    Conferma password utente database (sistema): immettere di nuovo la password dell'utente del database per confermare  
    Riavviare il sistema dopo il riavvio della macchina? [n]: premere INVIO per accettare il valore predefinito  
    Continuare? (y/n): Convalidare il riepilogo. Immettere **y** per continuare  

4. **[A]** Aggiornare l'agente host SAP

   Scaricare l'archivio dell'agente host SAP più recente dal sito [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter) ed eseguire il comando seguente per aggiornare l'agente. Sostituire il percorso dell'archivio in modo da puntare al file scaricato:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[T]** Configurare il firewall

   Creare la regola del firewall per la porta probe di Azure Load Balancer.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Configure SAP HANA system replication (Configurare la replica di sistema SAP HANA)

Eseguire la procedura descritta in configurare [SAP Hana la replica di sistema](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) per configurare SAP Hana la replica di sistema. 

## <a name="cluster-configuration"></a>Configurazione del cluster

In questa sezione vengono descritti i passaggi necessari per il funzionamento uniforme del cluster quando SAP HANA viene installato nelle condivisioni NFS tramite Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi descritti in [configurazione di pacemaker in Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) in Azure per creare un cluster Pacemaker di base per questo server Hana.

### <a name="configure-filesystem-resources"></a>Configurare le risorse filesystem

In questo esempio ogni nodo del cluster ha i propri filesystem HANA NFS/Hana/Shared,/Hana/data e/Hana/log.   

1. **[1]** impostare il cluster in modalità manutenzione.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** creare le risorse del file System per i montaggi **hanadb1** .

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** creare le risorse del file System per i montaggi **hanadb2** .

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` l'attributo viene aggiunto all'operazione di monitoraggio in modo che ogni monitoraggio esegua un test di lettura/scrittura sul file System. Senza questo attributo, l'operazione di monitoraggio verifica solo che il file System sia montato. Questo può costituire un problema perché, in caso di perdita della connettività, il file System può rimanere montato nonostante non sia accessibile.

    `on-fail=fence` l'attributo viene aggiunto anche all'operazione di monitoraggio. Con questa opzione, se l'operazione di monitoraggio ha esito negativo in un nodo, il nodo viene immediatamente recintato. Senza questa opzione, il comportamento predefinito prevede l'arresto di tutte le risorse che dipendono dalla risorsa non riuscita, quindi il riavvio della risorsa non riuscita, quindi l'avvio di tutte le risorse che dipendono dalla risorsa che ha avuto esito negativo. Questo comportamento non solo può richiedere molto tempo quando una risorsa SAPHana dipende dalla risorsa che ha avuto esito negativo, ma può anche avere esito negativo. La risorsa SAPHana non può arrestarsi correttamente se il server NFS che contiene i file eseguibili HANA non è accessibile.

4. **[1]** configurazione di vincoli di percorso

   Configurare i vincoli di posizione per assicurarsi che le risorse che gestiscono i montaggi univoci di hanadb1 non possano mai essere eseguite in hanadb2 e viceversa.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    L' `resource-discovery=never` opzione è impostata perché i montaggi univoci per ogni nodo condividono lo stesso punto di montaggio. Ad esempio, `hana_data1` Usa il punto di montaggio `/hana/data` e `hana_data2` usa anche il punto di montaggio `/hana/data` . Questo può causare un falso positivo per un'operazione Probe, quando lo stato della risorsa viene verificato all'avvio del cluster e questo può a sua volta causare un comportamento di recupero non necessario. Questo problema può essere evitato impostando `resource-discovery=never`

5. **[1]** configurazione delle risorse degli attributi

   Configurare le risorse degli attributi. Questi attributi verranno impostati su true se vengono montati tutti i montaggi NFS di un nodo (/Hana/data,/Hana/log e/Hana/Data) e verranno impostati su false in caso contrario.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** configurazione di vincoli di percorso

   Configurare i vincoli di percorso per assicurarsi che la risorsa dell'attributo hanadb1's non venga mai eseguita in hanadb2 e viceversa.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** creazione di vincoli di ordinamento

   Configurare i vincoli di ordinamento in modo che le risorse degli attributi di un nodo vengano avviate solo dopo l'installazione di tutti i montaggi NFS del nodo.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Se la configurazione include file System, all'esterno del gruppo `hanadb1_nfs` o `hanadb2_nfs` , includere l' `sequential=false` opzione, in modo che non esistano dipendenze di ordinamento tra i file System. Tutti i file System devono iniziare prima `hana_nfs1_active` , ma non devono essere avviati in alcun ordine. Per altri dettagli, vedere [ricerca per categorie configurare la replica del sistema SAP Hana in scalabilità verticale in un cluster pacemaker quando i filesystem Hana si trovano su condivisioni NFS](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Configurare SAP HANA risorse cluster

1. Per creare le risorse SAP HANA nel cluster, seguire la procedura descritta in [creare SAP Hana risorse del cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#create-sap-hana-cluster-resources) . Una volta create le risorse di SAP HANA, è necessario creare un vincolo della regola di posizione tra SAP HANA risorse e i filesystem (montaggi NFS)

2. **[1]** configurare i vincoli tra le risorse SAP HANA e i montaggi NFS

   I vincoli della regola di percorso verranno impostati in modo che le risorse SAP HANA possano essere eseguite su un nodo solo se sono montati tutti i montaggi NFS del nodo.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Verificare lo stato del cluster e tutte le risorse

    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

Questa sezione descrive come testare la configurazione. 

1. Prima di avviare un test, assicurarsi che pacemaker non abbia un'azione non riuscita (tramite lo stato dei PC), non vi sono vincoli di posizione imprevisti (ad esempio, avanzi di un test di migrazione) e che la replica di sistema HANA è stato di sincronizzazione, ad esempio con systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Verificare la configurazione del cluster per uno scenario di errore quando un nodo perde l'accesso alla condivisione NFS (/Hana/Shared)

   Gli agenti di risorse SAP HANA dipendono dai file binari, archiviati in `/hana/shared` per eseguire operazioni durante il failover. Il file System  `/hana/shared` viene montato su NFS nello scenario presentato.  
   È difficile simulare un errore, in cui uno dei server perde l'accesso alla condivisione NFS. Un test che può essere eseguito consiste nel rimontare il file system come di sola lettura.
   Questo approccio consente di verificare che il cluster sia in grado di eseguire il failover, se l'accesso a `/hana/shared` viene perso nel nodo attivo.     


   **Risultato previsto:** Quando si effettua la `/hana/shared` file System di sola lettura, l' `OCF_CHECK_LEVEL` attributo della risorsa `hana_shared1` che esegue l'operazione di lettura/scrittura su file System avrà esito negativo perché non è in grado di scrivere alcun elemento nel file System ed eseguirà il failover delle risorse Hana.  Lo stesso risultato è previsto quando il nodo HANA perde l'accesso alle condivisioni NFS. 

   Stato delle risorse prima dell'avvio del test:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   È possibile inserire/Hana/Shared in modalità di sola lettura nel nodo del cluster attivo, usando il comando seguente:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 viene riavviato o spento in base al set di azioni in STONITH ( `pcs property show stonith-action` ).  Quando il server (hanadb1) è inattivo, la risorsa HANA viene spostata in hanadb2. È possibile controllare lo stato del cluster da hanadb2.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   Si consiglia di testare accuratamente la configurazione del cluster di SAP HANA, eseguendo anche i test descritti in [installazione SAP Hana la replica di sistema in RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#test-the-cluster-setup).   