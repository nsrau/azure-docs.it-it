---
title: StorSimple 8000 series migration to Azure File Sync
description: Informazioni su come eseguire la migrazione di un'appliance StorSimple 8100 o 8600 ad Sincronizzazione file di Azure.Learn how to migrate a StorSimple 8100 or 8600 appliance to Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7f0c4da7caf71670746e84d5cfaa457ebae57156
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755040"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 and 8600 migration to Azure File Sync

La serie StorSimple 8000 è rappresentata dalle appliance fisiche 8100 o 8600 locali e dai relativi componenti del servizio cloud. È possibile eseguire la migrazione dei dati da una di queste appliance a un ambiente di sincronizzazione file di Azure.It is possible to migrate the data from either of these appliances to an Azure File Sync environment. Sincronizzazione file di Azure è il servizio di Azure predefinito e strategico a lungo termine in cui è possibile eseguire la migrazione delle appliance StorSimple.Azure File Sync is the default and strategic azure service that StorSimple appliances can be migrated to.

La serie StorSimple 8000 raggiungerà la [fine del ciclo di vita](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) nel dicembre 2022. È importante iniziare a pianificare la migrazione il prima possibile. Questo articolo fornisce le conoscenze di base necessarie e i passaggi di migrazione per una corretta migrazione a Sincronizzazione file di Azure.This article provides the necessary background knowledge and migrations steps for a successful migration to Azure File Sync. 

## <a name="azure-file-sync"></a>Sincronizzazione file di Azure

> [!IMPORTANT]
> Microsoft si impegna ad assistere i clienti nella migrazione. E-mail AzureFilesMigration@microsoft .com per un piano di migrazione personalizzato e assistenza durante la migrazione.

Sincronizzazione file di Azure è un servizio cloud Microsoft basato su due componenti principali:Azure File Sync is a Microsoft cloud service, based on two main components:

* Sincronizzazione dei file e suddivisione in livelli nel cloud.
* Condivisioni di file come archiviazione nativa in Azure, a cui è possibile accedere tramite più protocolli, ad esempio SMB e REST di file. Una condivisione file di Azure è paragonabile a una condivisione file in un Windows Server, che è possibile montare in modo nativo come unità di rete. Supporta importanti aspetti di fedeltà dei file come attributi, autorizzazioni e timestamp. Con le condivisioni file di Azure non è più necessario che un'applicazione o un servizio interpreti i file e le cartelle archiviati nel cloud. È possibile accedervi in modo nativo tramite protocolli e client familiari come Esplora file di Windows.You can access them natively over native ly protocols and clients like Windows File Explorer. Ciò rende le condivisioni file di Azure l'approccio ideale e più flessibile per archiviare i dati del file server generico e alcuni dati dell'applicazione nel cloud.

Questo articolo è incentrato sui passaggi di migrazione. Se prima di eseguire la migrazione si desidera ottenere altre informazioni su Sincronizzazione file di Azure, è consigliabile eseguire le procedure seguenti:If before migrating you're learn more about Azure File Sync, we recommend the following articles:

* [Sincronizzazione file di Azure - Panoramica](https://aka.ms/AFS "Panoramica")
* [Sincronizzazione file di Azure - guida alla distribuzioneAzure File Sync - deployment guide](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Obiettivi della migrazione

L'obiettivo è garantire l'integrità dei dati di produzione e garantire la disponibilità. Quest'ultimo richiede di mantenere i tempi di inattività al minimo, in modo che possa adattarsi o superare solo leggermente le finestre di manutenzione regolari.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000 series migration path to Azure File Sync

Per eseguire un agente di Sincronizzazione file di Azure è necessario un Windows Server locale. Windows Server può essere almeno un server 2012R2 ma idealmente è un Windows Server 2019.

Esistono numerosi percorsi di migrazione alternativi e creerebbe troppo a lungo di un articolo per documentarli tutti e illustrare perché sono in grado di sostenere rischi o svantaggi lungo il percorso che si consiglia come procedura consigliata in questo articolo.

![Panoramica delle fasi di migrazione della serie StorSimple 8000](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 serie percorso di migrazione panoramica delle fasi più avanti in questo articolo.")

L'immagine precedente illustra le fasi che corrispondono alle sezioni di questo articolo.
Utilizziamo una migrazione lato cloud per evitare inutili richiami di file nell'appliance StorSimple locale. Questo approccio evita di influire sul comportamento di memorizzazione nella cache locale o sull'uso della larghezza di banda di rete, uno dei quali può influire sui carichi di lavoro di produzione.
Una migrazione lato cloud sta operando su uno snapshot (un clone del volume) dei dati. In questo modo i dati di produzione sono isolati da questo processo fino al cut-over alla fine della migrazione. Lavorando fuori di quello che è essenzialmente un backup, rende la migrazione sicura e facilmente ripetibile, in caso di difficoltà.

## <a name="considerations-around-existing-storsimple-backups"></a>Considerazioni sui backup StorSimple esistenti

StorSimple consente di eseguire backup sotto forma di cloni di volume. Questo articolo usa un nuovo clone del volume per eseguire la migrazione dei file in tempo reale.
Se è necessario eseguire la migrazione dei backup oltre ai dati attivi, tutte le indicazioni fornite in questo articolo sono ancora valide. L'unica differenza è che invece di iniziare con un nuovo clone del volume, si inizierà con il clone del volume di backup meno recente di cui è necessario eseguire la migrazione.

La sequenza è la seguente:

* Determinare il set minimo di cloni di volume di cui eseguire la migrazione. Se possibile, è consigliabile mantenere l'elenco al minimo, poiché maggiore è il numero di backup di cui si esegue la migrazione.
* Quando si esegue il processo di migrazione, iniziare con il clone del volume meno recente che si intende migrare e a ogni migrazione successiva, usare il più recente successivo.
* Al termine della migrazione di ogni clone del volume, è necessario creare uno snapshot di condivisione file di Azure.When each volume clone migration completes, you must take an Azure file share snapshot. Gli snapshot di condivisione file di Azure consentono di mantenere i backup temporizzato dei file e della struttura di cartelle per le condivisioni file di [Azure.Azure file share snapshots](storage-snapshots-files.md) are how you keep point-in-time backups of the files and folder structure for your Azure file shares. Questi snapshot saranno necessari al termine della migrazione, per assicurarsi di aver mantenuto le versioni di ogni clone del volume durante l'avanzamento della migrazione.
* Assicurarsi di creare snapshot di condivisione file di Azure per tutte le condivisioni file di Azure servite dallo stesso volume StorSimple.Ensure that you take Azure file share snapshots for all Azure file shares, that are served by the same StorSimple volume. I cloni di volume sono a livello di volume, gli snapshot di condivisione file di Azure sono a livello di condivisione. È necessario creare uno snapshot di condivisione (in ogni condivisione file di Azure) al termine della migrazione di un clone del volume.
* Ripetere il processo di migrazione per un clone del volume e acquisire snapshot di condivisione dopo ogni clone del volume fino a quando non si viene rilevati uno snapshot dei dati attivi. Il processo di migrazione di un clone del volume è descritto nelle fasi seguenti. 

Se non è necessario spostare i backup e avviare una nuova catena di backup sul lato condivisione file di Azure dopo la migrazione dei soli dati attivi, è possibile ridurre la complessità della migrazione e la quantità di tempo necessaria per la migrazione. È possibile decidere se spostare o meno i backup e quanti per ogni volume (non ogni condivisione) in StorSimple.

## <a name="phase-1-get-ready"></a>Fase 1: preparatevi

:::row:::
    :::column:::
        ![Immagine che illustra una parte dell'immagine di panoramica precedente che consente di mettere a fuoco questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        La base per la migrazione è un clone del volume e un'appliance cloud virtuale, denominata StorSimple 8020.The basis for the migration is a volume clone and a virtual cloud appliance, called a StorSimple 8020.
Questa fase è incentrata sulla distribuzione di queste risorse in Azure.This phase focuses on deployment of these resources in Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Distribuire un'appliance virtuale StorSimple 8020Deploy a StorSimple 8020 virtual appliance

La distribuzione di un'appliance cloud è un processo che richiede sicurezza, rete e alcune altre considerazioni.

> [!IMPORTANT]
> La seguente guida contiene alcune sezioni non necessarie. Leggi e segui l'articolo dall'inizio fino al "Passaggio 3". Quindi tornare a questo articolo. Non è necessario completare il "Passaggio 3" o qualsiasi altra cosa oltre in quella guida, in questo momento.

[Distribuzione di un'appliance virtuale StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Determinare un clone del volume da utilizzare

Quando si è pronti per iniziare la migrazione, il primo passaggio consiste nell'eseguire un nuovo clone del volume, proprio come si farebbe per il backup, che acquisisca lo stato corrente dell'archiviazione cloud StorSimple. Prendi un clone per ciascuno dei volumi StorSimple che hai.
Se è necessario spostare i backup, il primo clone del volume utilizzato non è un clone appena creato, ma il clone del volume meno recente (backup meno recente) di cui è necessario eseguire la migrazione.
Per istruzioni dettagliate, fare riferimento alla sezione ["Considerazioni sui backup StorSimple esistenti".](#considerations-around-existing-storsimple-backups)

> [!IMPORTANT]
> La seguente guida contiene alcune sezioni non necessarie. Leggere e seguire solo i passaggi descritti nella sezione collegata. Quindi tornare a questo articolo. Non è necessario seguire la sezione "Passaggi successivi".

[Creare un clone di un volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Utilizzare il clone del volume

The last phase of phase 1 is to make the volume clone you've chosen, available on the 8020 virtual appliance in Azure.

> [!IMPORTANT]
> La seguente guida contiene i passaggi necessari ma anche - alla fine - un'istruzione per formattare il volume. **NON FORMATTARE IL VOLUME** Leggere e seguire la "sezione 7" collegata dall'inizio fino all'istruzione: "10. Per formattare un volume semplice, ..."  Basta prima di seguire questo passaggio e tornare a questo articolo.

[Montare un clone del volume nell'appliance virtuale 8020 in AzureMount a volume clone on the 8020 virtual appliance in Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Riassunto della fase 1

Dopo aver completato la fase 1, avrai completato le seguenti operazioni:

* Distribuito un'appliance virtuale StorSimple 8020 in Azure.Deployed a StorSimple 8020 virtual appliance in Azure.
* Determinato quale clone del volume si inizierà la migrazione con.
* È stato montato i cloni del volume (uno per ogni volume attivo) nell'appliance virtuale StorSimple in Azure, con i dati disponibili per un ulteriore utilizzo.

## <a name="phase-2-cloud-vm"></a>Fase 2: VM cloudPhase 2: Cloud VM

:::row:::
    :::column:::
        ![Immagine che illustra una parte dell'immagine di panoramica precedente che consente di mettere a fuoco questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Dopo che il clone iniziale è disponibile nell'appliance virtuale StorSimple 8020 in Azure, è ora necessario eseguire il provisioning di una macchina virtuale ed esporre il clone del volume (o multiplo) a tale macchina virtuale tramite iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Distribuire una macchina virtuale di AzureDeploy an Azure VM

La macchina virtuale Windows Server in Azure è analoga a StorSimple 8020, un'infrastruttura temporanea necessaria solo durante la migrazione.
La configurazione della macchina virtuale distribuita dipende principalmente dal numero di elementi (file e cartelle) da sincronizzare. Si consiglia di utilizzare una configurazione con prestazioni più elevate in caso di problemi.

Un singolo Windows Server può sincronizzare fino a 30 condivisioni file di Azure.A single Windows Server can sync up to 30 Azure file shares.
Le specifiche che si decide di includere ogni condivisione / percorso o la radice del volume StorSimple e contare gli elementi (file e cartelle).

La dimensione complessiva dei dati è meno di un collo di bottiglia - è il numero di elementi che è necessario personalizzare le specifiche della macchina.

* [Informazioni su come ridimensionare un Windows Server in base al numero di elementi (file e cartelle) da sincronizzare.](storage-sync-files-planning.md#recommended-system-resources)

    **Siete pregati di notare che:** L'articolo collegato in precedenza presenta una tabella con un intervallo per la memoria del server (RAM). Orientarsi verso il numero elevato per la macchina virtuale di Azure.Orient towards the large number for the Azure VM. È possibile orientarsi verso il numero più piccolo per il computer locale.

* [Informazioni su come distribuire una macchina virtuale Windows Sever.Learn how to deploy a Windows Sever VM.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Assicurarsi che la macchina virtuale sia distribuita nella stessa area di Azure dell'appliance virtuale StorSimple 8020. Se come parte di questa migrazione, è anche necessario modificare l'area dei dati cloud dall'area in cui sono archiviati oggi, è possibile farlo in un passaggio successivo, quando si esegue il provisioning delle condivisioni file di Azure.If as part of this migration, you also need to change the region of your cloud data from the region it is stored in today, you can do that in a later step, when you provision Azure file shares.

> [!IMPORTANT]
> Spesso, un server Windows locale viene utilizzato per eseguire il front-front dell'appliance StorSimple locale. In tale configurazione, è possibile attivare la funzionalità "[Deduplicazione dati](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)" su tale Windows Server. **Se è stata usata la deduplicazione dei dati con i dati StorSimple, assicurarsi di abilitare la deduplicazione dei dati anche in questa macchina virtuale di Azure.If you used Data Deduplication with your StorSimple data, ensure that you enable Data Deduplication on this Azure VM well.** Non confondere questa deduplicazione a livello di file con StorSimples deduplicazione a livello di blocco incorporata, per la quale non è necessaria alcuna azione.

> [!IMPORTANT]
> Per ottimizzare le prestazioni, distribuire un **disco del sistema operativo veloce** per la macchina virtuale cloud. Il database di sincronizzazione verrà archiviato sul disco del sistema operativo per tutti i volumi di dati. Inoltre, assicurarsi di creare un **disco del sistema operativo**di grandi dimensioni . A seconda del numero di elementi (file e cartelle) nei volumi StorSimple, il disco del sistema operativo potrebbe richiedere **diverse centinaia** di GiB di spazio per contenere il database di sincronizzazione.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Esporre i volumi StorSimple 8020 alla macchina virtuale di AzureExpose the StorSimple 8020 volumes to the Azure VM

In questa fase si connettono uno o più volumi StorSimple dall'appliance virtuale 8020 tramite iSCSI alla macchina virtuale Windows Server di cui è stato eseguito il provisioning.

> [!IMPORTANT]
> Per gli articoli seguenti, completare solo le sezioni **Ottieni IP privato per l'appliance cloud** e Connetti **tramite iSCSI** e torna a questo articolo.

1. [Ottenere l'indirizzo IP privato per l'appliance cloud](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Connessione tramite iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Riassunto della fase 2

Ora che hai completato la fase 2, hai: 

* Provisioning di una macchina virtuale Windows Server nella stessa area dell'appliance StorSimple virtuale 8020
* Esposti tutti i volumi applicabili da 8020 a VM di Windows Server su iSCSI.
* Il contenuto di file e cartelle dovrebbe ora essere visualizzato quando si usa Esplora file nella macchina virtuale del server nei volumi installati.

Procedere alla fase 3 solo dopo aver completato questi passaggi per tutti i volumi che richiedono la migrazione.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fase 3: Configurare le condivisioni file di Azure e prepararsi per Sincronizzazione file di AzurePhase 3: Set up Azure file shares and get ready for Azure File Sync

:::row:::
    :::column:::
        ![Immagine che illustra una parte dell'immagine di panoramica precedente che consente di mettere a fuoco questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        In this phase, you will be determining and provisioning a number of Azure file shares, creating a Windows Server on-premises as a StorSimple appliance replacement and configure that server for Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Eseguire il mapping degli spazi dei nomi esistenti alle condivisioni file di AzureMap your existing namespaces to Azure file shares

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Distribuire condivisioni file di AzureDeploy Azure file shares

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Se è necessario modificare l'area di Azure dall'area corrente in cui si trova i dati di StorSimple, eseguire il provisioning delle condivisioni file di Azure nella nuova area che si vuole usare. Per determinare l'area, selezionarla quando si esegue il provisioning degli account di archiviazione che contengono le condivisioni file di Azure.You determine the region by selecting it when you provision the storage accounts that hold your Azure file shares. Assicurarsi che anche la risorsa di Sincronizzazione file di Azure di cui verrà eseguito il provisioning di seguito si trovi nella stessa area nuova.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Distribuire la risorsa cloud di Sincronizzazione file di AzureDeploy the Azure File Sync cloud resource

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se è necessario modificare l'area di Azure dall'area corrente in cui si trovano i dati di StorSimple, è stato eseguito il provisioning degli account di archiviazione per le condivisioni file di Azure nella nuova area. Assicurarsi di aver selezionato la stessa area quando si distribuisce il servizio di sincronizzazione archiviazione.

### <a name="deploy-an-on-premises-windows-server"></a>Distribuire un server Windows localeDeploy an on-premises Windows Server

* Creare un Windows Server 2019 - almeno 2012R2 - come macchina virtuale o server fisico. È supportato anche un cluster di failover di Windows Server.A Windows Server fail-over cluster is also supported. Non riutilizzare il server si potrebbe avere davanti il StorSimple 8100 o 8600.
* Effettuare il provisioning o aggiungere DAS (Direct Attached Storage) rispetto al NAS, che non è supportato.

È consigliabile fornire al nuovo Windows Server una quantità di spazio di archiviazione uguale o maggiore rispetto all'appliance StorSimple 8100 o 8600 disponibile localmente per la memorizzazione nella cache. Si utilizzerà Windows Server nello stesso modo in cui è stato utilizzato l'appliance StorSimple, se ha la stessa quantità di spazio di archiviazione dell'appliance, l'esperienza di memorizzazione nella cache dovrebbe essere simile, se non la stessa.
È possibile aggiungere o rimuovere spazio di archiviazione da Windows Server a volontà. In questo modo è possibile ridimensionare le dimensioni del volume locale e la quantità di spazio di archiviazione locale disponibile per la memorizzazione nella cache.

### <a name="prepare-the-windows-server-for-file-sync"></a>Preparare Windows Server per la sincronizzazione dei file

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Configurare Sincronizzazione file di Azure in Windows ServerConfigure Azure File Sync on the Windows Server

Per questo processo, il server Windows Server locale registrato deve essere pronto e connesso a Internet.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Assicurati di attivare la suddivisione in livelli cloud!** Il tiering cloud è la funzionalità AFS che consente al server locale di disporre di una capacità di archiviazione inferiore a quella archiviata nel cloud, ma dello spazio dei nomi completo è disponibile. Anche i dati interessanti a livello locale vengono memorizzati nella cache locale per veloci prestazioni di accesso locale. Un altro motivo per attivare la suddivisione in livelli cloud in questo passaggio è che non si desidera sincronizzare il contenuto del file in questa fase, solo lo spazio dei nomi deve essere spostato in questo momento.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fase 4: Configurare la macchina virtuale di Azure per la sincronizzazionePhase 4: Configure the Azure VM for sync

:::row:::
    :::column:::
        ![Immagine che illustra una parte dell'immagine di panoramica precedente che consente di mettere a fuoco questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Questa fase riguarda la macchina virtuale di Azure con i cloni del primo volume montati su iSCSI. Durante questa fase, si otterrà la macchina virtuale connessa tramite Sincronizzazione file di Azure e verrà avviato un primo ciclo di spostamento dei file dai cloni del volume StorSimple.During this phase, you will get the VM connected via Azure File Sync and start a first round of moving files from your StorSimple volume clone(s).
        
    :::column-end:::
:::row-end:::

Il server locale che sostituirà l'appliance StorSimple 8100 o 8600 per Sincronizzazione file di Azure.You already have configured your on-premises server that will replace your StorSimple 8100 or 8600 appliance, for Azure File Sync. 

La configurazione della macchina virtuale di Azure è un processo quasi identico, con un passaggio aggiuntivo. I seguenti passaggi ti guideranno attraverso il processo.

> [!IMPORTANT]
> È importante che la macchina virtuale di Azure non sia **configurata con la suddivisione in livelli cloud abilitata.** Il volume di questo server verrà scambiato con cloni di volume più recenti durante la migrazione. La suddivisione in livelli nel cloud non presenta vantaggi e sovraccarichi sull'utilizzo della CPU da evitare.

1. [Distribuire l'agente AFS. (vedere la sezione precedente)](#prepare-the-windows-server-for-file-sync)
2. [Preparazione della macchina virtuale per Sincronizzazione file di Azure.Getting the VM ready for Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Configurare la sincronizzazione](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Preparare la macchina virtuale per Sincronizzazione file di AzureGet the VM ready for Azure File Sync

Sincronizzazione file di Azure viene usato per spostare i file dai volumi iSCSI StorSimple montati alle condivisioni file di Azure di destinazione.
Durante questo processo di migrazione, verranno montati diversi cloni del volume nella macchina virtuale, sotto la stessa lettera di unità. Azure File Sync must be configured to see the next volume clone you've mounted as a newer version of the files and folders and update the Azure file shares connected via Azure File Sync. 

> [!IMPORTANT]
> Affinché funzioni, è necessario impostare una chiave del Registro di sistema nel server prima di configurare Sincronizzazione file di Azure.For this to work, a registry key must be set on the server before Azure File Sync is configured.

1. Creare una nuova directory nell'unità di sistema della macchina virtuale. Le informazioni di Sincronizzazione file di Azure dovranno essere mantenute anziché nei cloni del volume montati. Ad esempio: `"C:\syncmetadata"`
2. Aprire regedit e individuare il seguente hive del Registro di sistema:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Creare una nuova chiave di tipo String, denominata: ***MetadataRootPathCreate*** a new Key of type String, named: MetadataRootPath
4. Impostare il percorso completo della directory creata nel volume di sistema, ad esempio:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Configurare Sincronizzazione file di Azure nella macchina virtuale di AzureConfigure Azure File Sync on the Azure VM

Questo passaggio è simile alla sezione precedente, in cui viene illustrato come configurare AFS nel server locale.

La differenza è che non è necessario abilitare la suddivisione in livelli cloud in questo server e che è necessario assicurarsi che le cartelle corrette siano connesse alle condivisioni file di Azure corrette. In caso contrario, la denominazione delle condivisioni file di Azure e del contenuto dei dati non corrisponderà e non è possibile rinominare le risorse cloud o le cartelle locali senza riconfigurare la sincronizzazione.

Fare riferimento alla [sezione precedente su come configurare Sincronizzazione file](#configure-azure-file-sync-on-the-windows-server)di Azure in un server Windows .

### <a name="step-4-summary"></a>Riepilogo passo 4

A questo punto, si sarà configurato correttamente Sincronizzazione file di Azure nella macchina virtuale di Azure sono stati montati i cloni del volume StorSimple tramite iSCSI.
I dati scorrono ora dalla macchina virtuale di Azure alle varie condivisioni file di Azure e da lì viene visualizzato uno spazio dei nomi completamente stanco nel server Windows locale.

> [!IMPORTANT]
> Assicurarsi che non vengano apportate modifiche o che l'accesso utente sia stato concesso a Windows Server in questo momento.

Lo spostamento iniziale dei dati di clonazione del volume nella macchina virtuale di Azure nelle condivisioni file di Azure può richiedere molto tempo, potenzialmente settimane. Stimare il tempo che questo richiederà è difficile e dipende da molti fattori. In particolare, la velocità con cui la macchina virtuale di Azure può accedere ai file nei volumi StorSimple e la velocità con cui Sincronizzazione file di Azure può elaborare i file e le cartelle che devono essere sincronizzati. 

Per esperienza, possiamo supporre che la larghezza di banda - quindi la dimensione effettiva dei dati - gioca un ruolo subordinato. Il tempo necessario per questo o qualsiasi round di migrazione successivo dipende principalmente dal numero di elementi che possono essere elaborati al secondo. Così, per esempio 1 TiB con un 100.000 file e cartelle sarà molto probabilmente finire più lentamente di 1 TiB con solo 50.000 file e cartelle.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fase 5: Scorrere più cloni di volumePhase 5: Iterate through multiple volume clones

:::row:::
    :::column:::
        ![Immagine che illustra una parte dell'immagine di panoramica precedente che consente di mettere a fuoco questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Come illustrato nella fase precedente, la sincronizzazione iniziale può richiedere molto tempo. Gli utenti e le applicazioni stanno ancora accedendo all'appliance StorSimple 8100 o 8600 locale. Ciò significa che le modifiche si stanno accumulando e ogni giorno si accumula un delta più grande tra i dati attivi e il clone del volume iniziale, si sta attualmente migrando, forme. In questa sezione verrà illustrato come ridurre al minimo i tempi di inattività usando più cloni di volume e indicando quando la sincronizzazione è terminata.
    :::column-end:::
:::row-end:::

Sfortunatamente, il processo di migrazione non è istantaneo. Ciò significa che il suddetto delta per i dati in tempo reale è una conseguenza inevitabile. La buona notizia è che è possibile ripetere il processo di montaggio di nuovi cloni di volume. Il delta di ogni clone del volume sarà progressivamente più piccolo. Quindi, alla fine, una sincronizzazione terminerà in un periodo di tempo che si ritiene accettabile per portare gli utenti e le app non in linea per tagliare al server Windows locale.

Ripeti i passaggi seguenti fino al completamento della sincronizzazione in una durata abbastanza veloce che ti senti a tuo agio nel portare gli utenti e le app offline:

1. [Determinare che la sincronizzazione è stata completata per un determinato clone del volume.](#determine-when-sync-is-done)
2. [Prendere un nuovo clone del volume e montarlo sull'appliance virtuale 8020.](#the-next-volume-clones)
3. [Determinare quando la sincronizzazione è terminata.](#determine-when-sync-is-done)
4. [Strategia di cut-over](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>I cloni di volume successivi

Abbiamo discusso prendendo un clone del volume in precedenza in questo articolo.
Questa fase prevede due azioni:

1. [Prendi un clone del volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Monta il clone del volume (vedi sopra)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Determinare quando la sincronizzazione è terminata

Al termine della sincronizzazione, è possibile interrompere la misurazione del tempo e determinare se è necessario ripetere il processo di preazione e montaggio di un clone del volume o se la sincronizzazione del tempo impiegato con l'ultimo clone del volume era sufficientemente piccola.

Per determinare la sincronizzazione è stata completata:

1. Aprire il Visualizzatore eventi e passare ad **Applicazioni e servizi**
2. Spostarsi e aprire **Microsoft, FileSync, Agente, Telemetria**
3. Cercare **l'evento 9102**più recente, che corrisponde a una sessione di sincronizzazione completata
4. Selezionare **Dettagli** e verificare che il valore **SyncDirection** sia **Upload**
5. Controllare **HResult** e verificare che mostri **0**. Ciò significa che la sessione di sincronizzazione ha avuto esito positivo. Se HResult è un valore diverso da zero, si è verificato un errore durante la sincronizzazione. Se **perItemErrorCount** è maggiore di 0, alcuni file o cartelle non sono stati sincronizzati correttamente. È possibile ricevere un valore HResult pari a 0, ma al contempo un PerItemErrorCount maggiore di 0. A questo punto, non è necessario preoccuparsi di PerItemErrorCount. Prenderemo questi file più tardi. Se questo numero di errori è significativo, migliaia di elementi, contattare il supporto tecnico e chiedere di essere connessi al gruppo di prodotti Azure File Sync per indicazioni dirette sulle fasi migliori e successive.
6. Verificare che più eventi 9102 con HResult 0 di fila. Ciò indica che la sincronizzazione è stata completata per questo clone del volume.

### <a name="cut-over-strategy"></a>Strategia di cut-over

1. Determinare se la sincronizzazione da un clone del volume è abbastanza veloce ora. (Delta abbastanza piccolo.)
2. Disconnettere l'appliance StorSimple.
3. Un'ultima RoboCopy.

Misurare il tempo e determinare se la sincronizzazione da un clone del volume recente può terminare entro un intervallo di tempo sufficientemente piccolo, che è possibile permettersi come tempo di inattività nel sistema.

È ora di disabilitare l'accesso utente all'appliance StorSimple. Niente più cambiamenti. I tempi di inattività sono iniziati.
È necessario lasciare l'apparecchio online e connesso, ma ora è necessario evitare modifiche su di esso.

Nella fase 6 si raggiunge con qualsiasi delta nei dati attivi dall'ultimo clone del volume.

## <a name="phase-6-a-final-robocopy"></a>Fase 6: Un RoboCopy finalePhase 6: A Final RoboCopy

A questo punto, esistono due differenze tra Windows Server locale e l'appliance StorSimple 8100 o 8600:

1. Potrebbero essere presenti file che non sono stati sincronizzati (vedere **PerItemErrors** dal registro eventi precedente)
2. L'appliance StorSimple dispone di una cache popolata rispetto a Windows Server solo uno spazio dei nomi senza alcun contenuto di file archiviato localmente in questo momento.

![Immagine che illustra una parte dell'immagine di panoramica precedente che consente di mettere a fuoco questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Possiamo portare la cache di Windows Server allo stato dell'appliance e garantire che nessun file venga lasciato con un RoboCopy finale.

> [!CAUTION]
> È imperativo che il comando RoboCopy seguito sia esattamente come descritto di seguito. Vogliamo solo copiare i file che sono locali e i file che non sono stati spostati attraverso l'approccio di clonazione del volume - sincronizzazione prima. Possiamo risolvere i problemi per cui non sono stati sincronizzati in un secondo momento, al termine della migrazione. (Vedere [Risoluzione dei problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). È molto probabile che non stampabile caratteri nei nomi di file che non perderai quando vengono eliminati.)

Comando RoboCopy:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Sfondo:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Consente a RoboCopy di eseguire multithreading. Il valore predefinito è 8, max è 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Restituisce lo stato nel file LOG come UNICODE (sovrascrive il registro esistente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE (in inglese)
   :::column-end:::
   :::column span="1":::
      Uscite nella finestra della console. Utilizzato insieme all'output in un file di log.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / B
   :::column-end:::
   :::column span="1":::
      Esegue RoboCopy nella stessa modalità utilizzata da un'applicazione di backup. Consente a RoboCopy di spostare i file per i cui l'utente corrente non dispone delle autorizzazioni.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Consente a RoboCopy di considerare solo i delta tra l'origine (appliance StorSimple) e la destinazione (directory di Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fedeltà della copia del file (l'impostazione predefinita è /COPY:DAT), i flag di copia: D: dati, A - Attributi, T -Timestamp, S- Sicurezza ACL NTFS, O -Owner info, U -aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL (COPIATUTTO)
   :::column-end:::
   :::column span="1":::
      COPY TUTTE le informazioni sul file (equivalente a /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fedeltà per la copia delle directory (l'impostazione predefinita è /DCOPY:DA), i flag di copia: D: dati, A - Attributi, T -Timestamps
   :::column-end:::
:::row-end:::

È necessario eseguire questo comando RoboCopy per ognuna delle directory in Windows Server come destinazione, configurata con la sincronizzazione dei file con un file di Azure.You should run this RoboCopy command for each of the directories on the Windows Server as a target, that you've configured with file sync to an Azure file.

È possibile eseguire più di questi comandi in parallelo.
Una volta completato questo passaggio RoboCopy, è possibile consentire agli utenti e alle app di accedere a Windows Server come hanno fatto prima dell'appliance StorSimple.

Consultare i file di registro robocopy per verificare se i file sono stati lasciati indietro. Se si verificano problemi, nella maggior parte dei casi è possibile risolverli al termine della migrazione e gli utenti e le app sono stati reinesplorati in Windows Server. Se è necessario risolvere eventuali problemi, farlo prima della fase 7.

È probabilmente necessario creare le condivisioni SMB su Windows Server che si erano sui dati StorSimple in precedenza. È possibile caricare questo passaggio ed eseguirlo in precedenza per non perdere tempo qui, ma è necessario assicurarsi che prima di questo punto, non si verificano modifiche ai file sul server Windows.

Se si dispone di una distribuzione DFS-N, è possibile puntare gli spazi dei nomi DFN ai nuovi percorsi delle cartelle del server. Se non si dispone di una distribuzione DFS-N e l'appliance 8100 8600 è stata anticipata localmente con Un server Windows Server, è possibile disconnettere il server dal dominio e aggiungere il nuovo Windows Server con AFS al dominio, assegnargli lo stesso nome del server precedente e gli stessi nomi di condivisione, quindi il cut-over al nuovo server rimane trasparente per gli utenti. , criteri di gruppo o script.

## <a name="phase-7-deprovision"></a>Fase 7: Deprovision

Durante l'ultima fase è stato eseguito l'iterazione tra più cloni di volume e alla fine si è passati l'accesso utente al nuovo Windows Server dopo avervi portato StorSimple appliance offline.

È ora possibile iniziare a eseguire il deprovisioning delle risorse non necessarie.
Prima di iniziare, è consigliabile osservare la nuova distribuzione di Sincronizzazione file di Azure nell'ambiente di produzione, per un po' . Che ti dà opzioni per risolvere eventuali problemi che potresti incontrare.

Dopo aver soddisfatto e aver osservato la distribuzione di AFS per almeno alcuni giorni, è possibile iniziare a eseguire il deprovisioning delle risorse nell'ordine seguente:Once you are satisfied and have observed your AFS deployment for least a few days, you can begin to deprovision resources in this order:

1. Disattivare la macchina virtuale di Azure usata per spostare i dati dai cloni del volume alle condivisioni file di Azure tramite la sincronizzazione dei file.
2. Passare alla risorsa del servizio di sincronizzazione archiviazione in Azure e annullare la registrazione della macchina virtuale di Azure.Go to your Storage Sync Service resource in Azure, and unregister the Azure VM. In questo modo viene rimosso da tutti i gruppi di sincronizzazione.

    > [!WARNING]
    > **ASSICURARSi di scegliere la macchina giusta.** La macchina virtuale cloud è stata disattivata, ovvero dovrebbe essere visualizzata come l'unico server offline nell'elenco dei server registrati. Non è necessario selezionare il server Windows locale in questo passaggio, in questo modo verrà annullata la registrazione.

3. Eliminare la macchina virtuale di Azure e le relative risorse.
4. Disattivare l'appliance storSimple virtuale 8020.
5. Eseguire il deprovisioning di tutte le risorse StorSimple in Azure.Deprovision all StorSimple resources in the Azure.
6. Scollegare l'appliance fisica StorSimple dal data center.

La migrazione è stata completata.

## <a name="next-steps"></a>Passaggi successivi

Acquisire maggiore familiarità con Sincronizzazione file di Azure.Get more familiar with Azure File Sync. Soprattutto con la flessibilità dei criteri di suddivisione in livelli cloud.

Se nel portale di Azure o negli eventi precedenti viene visualizzato che alcuni file non vengono sincronizzati in modo permanente, vedere la guida alla risoluzione dei problemi per la procedura di risoluzione di questi problemi.

* [Panoramica di Sincronizzazione file di Azure: aka.ms/AFS](https://aka.ms/AFS)
* [Suddivisione in livelli nel cloud](storage-sync-cloud-tiering.md) 
* [Guida alla risoluzione dei problemi di Sincronizzazione file di AzureAzure File Sync troubleshooting guide](storage-sync-files-troubleshoot.md)
