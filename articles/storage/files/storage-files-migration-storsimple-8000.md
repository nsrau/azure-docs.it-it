---
title: Migrazione della serie StorSimple 8000 a Sincronizzazione file di Azure
description: Informazioni su come eseguire la migrazione di un'appliance StorSimple 8100 o 8600 a Sincronizzazione file di Azure.
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
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 e 8600 migrazione a Sincronizzazione file di Azure

La serie StorSimple 8000 viene rappresentata dal 8100 o dalle appliance fisiche, locali e dai componenti del servizio cloud 8600. È possibile eseguire la migrazione dei dati da uno di questi Appliance a un ambiente Sincronizzazione file di Azure. Sincronizzazione file di Azure è il servizio di Azure a lungo termine predefinito e strategico a cui è possibile eseguire la migrazione delle appliance StorSimple.

La serie StorSimple 8000 raggiungerà la [fine del ciclo di vita](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) nel 2022 dicembre. È importante iniziare a pianificare la migrazione appena possibile. Questo articolo fornisce i passaggi necessari per la Knowledge base e le migrazioni per una migrazione corretta a Sincronizzazione file di Azure. 

## <a name="azure-file-sync"></a>Sincronizzazione file di Azure

> [!IMPORTANT]
> Microsoft si impegna a supportare i clienti nella migrazione. Email AzureFilesMigration@microsoft . com per un piano di migrazione personalizzato e assistenza durante la migrazione.

Sincronizzazione file di Azure è un servizio cloud Microsoft basato su due componenti principali:

* Sincronizzazione di file e suddivisione in livelli nel cloud.
* Condivisioni file come archiviazione nativa in Azure a cui è possibile accedere tramite più protocolli, ad esempio SMB e REST di file. Una condivisione file di Azure è paragonabile a una condivisione file in un server Windows, che può essere montata in modo nativo come unità di rete. Supporta importanti aspetti di fedeltà dei file, ad esempio attributi, autorizzazioni e timestamp. Con le condivisioni file di Azure non è più necessario che un'applicazione o un servizio interpreti i file e le cartelle archiviati nel cloud. È possibile accedervi in modo nativo su protocolli e client noti, ad esempio Esplora file di Windows. In questo modo, i file di Azure condividono l'approccio ideale e più flessibile per archiviare i dati file server per utilizzo generico, oltre ad alcuni dati dell'applicazione nel cloud.

Questo articolo è incentrato sulla procedura di migrazione. Se prima della migrazione si desidera ottenere altre informazioni su Sincronizzazione file di Azure, è consigliabile usare gli articoli seguenti:

* [Sincronizzazione file di Azure-Panoramica](https://aka.ms/AFS "Panoramica")
* [Guida alla distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Obiettivi della migrazione

L'obiettivo è garantire l'integrità dei dati di produzione e garantire la disponibilità. Il secondo richiede il mantenimento del tempo di inattività minimo, in modo che possa rientrare o solo leggermente più di una normale finestra di manutenzione.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Sincronizzazione file di Azure percorso di migrazione della serie 8000 di StorSimple

Per eseguire un agente di Sincronizzazione file di Azure, è necessario un server Windows locale. Windows Server può essere almeno un server 2012R2, ma idealmente è Windows Server 2019.

Sono disponibili numerosi percorsi di migrazione alternativi, che creerebbe troppo tempo per documentare tutti gli articoli e per illustrare il motivo per cui i rischi o gli svantaggi sul percorso si consigliano come procedura consigliata in questo articolo.

![Panoramica delle fasi di migrazione di StorSimple 8000 Series](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "Panoramica della route di migrazione serie StorSimple 8000 delle fasi più avanti in questo articolo.")

L'immagine precedente illustra le fasi che corrispondono alle sezioni di questo articolo.
Si usa una migrazione sul lato cloud per evitare il richiamo non necessario dei file nell'appliance StorSimple locale. Questo approccio evita un impatto sul comportamento della cache locale o sull'uso della larghezza di banda di rete, che può influire sui carichi di lavoro di produzione.
Una migrazione sul lato cloud sta operando in uno snapshot (un clone del volume) dei dati. I dati di produzione sono pertanto isolati da questo processo, fino al termine della migrazione. Per quanto riguarda essenzialmente un backup, la migrazione diventa sicura e facilmente ripetibile, in caso di problemi.

## <a name="considerations-around-existing-storsimple-backups"></a>Considerazioni sui backup StorSimple esistenti

StorSimple consente di eseguire i backup sotto forma di cloni del volume. Questo articolo usa un nuovo clone del volume per eseguire la migrazione dei file Live.
Se è necessario eseguire la migrazione dei backup oltre ai dati in tempo reale, tutte le istruzioni riportate in questo articolo sono comunque valide. L'unica differenza consiste nel fatto che anziché iniziare con un nuovo clone del volume, si inizierà con il clone del volume di backup meno recente che è necessario migrare.

La sequenza è la seguente:

* Determinare il set minimo di cloni del volume di cui è necessario eseguire la migrazione. Si consiglia di mantenere questo elenco come minimo, se possibile, perché maggiore è il numero di backup di cui si esegue la migrazione più a lungo verrà eseguito il processo di migrazione globale.
* Durante il processo di migrazione, iniziare con il clone del volume meno recente di cui si intende eseguire la migrazione e a ogni migrazione successiva, usare il successivo meno recente.
* Quando ogni migrazione del clone del volume viene completata, è necessario eseguire uno snapshot di condivisione file di Azure. Gli [snapshot di condivisione file di Azure](storage-snapshots-files.md) sono la modalità di mantenimento dei backup temporizzati della struttura di file e cartelle per le condivisioni file di Azure. Questi snapshot sono necessari dopo il completamento della migrazione, per assicurarsi di avere mantenuto le versioni di ogni clone del volume durante l'avanzamento della migrazione.
* Assicurarsi di eseguire snapshot di condivisione file di Azure per tutte le condivisioni file di Azure, che vengono gestite dallo stesso volume StorSimple. I cloni del volume sono a livello di volume, gli snapshot di condivisione file di Azure sono a livello di condivisione. Al termine della migrazione di un clone del volume, è necessario eseguire uno snapshot di condivisione (in ogni condivisione file di Azure).
* Ripetere il processo di migrazione per un clone del volume e acquisire snapshot di condivisione dopo ogni clonazione del volume fino a quando non si riceve uno snapshot dei dati in tempo reale. Il processo di migrazione di un clone del volume è descritto nelle fasi seguenti. 

Se non è necessario spostare i backup e si può avviare una nuova catena di backup nel lato condivisione file di Azure dopo che è stata eseguita la migrazione dei soli dati attivi, questo è utile per ridurre la complessità della migrazione e la quantità di tempo necessaria per la migrazione. È possibile decidere se spostare i backup e il numero per ogni volume (non ogni condivisione) presente in StorSimple.

## <a name="phase-1-get-ready"></a>Fase 1: preparare

:::row:::
    :::column:::
        ![Un'immagine che illustra una parte della precedente immagine di panoramica che consente di concentrare questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        La base della migrazione è un clone del volume e un'appliance cloud virtuale, denominata StorSimple 8020.
Questa fase è incentrata sulla distribuzione di queste risorse in Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Distribuire un'appliance virtuale StorSimple 8020

La distribuzione di un'appliance cloud è un processo che richiede sicurezza, rete e alcune altre considerazioni.

> [!IMPORTANT]
> Nella Guida seguente sono contenute alcune sezioni non necessarie. Leggere e seguire l'articolo dall'inizio fino al "passaggio 3". Tornare quindi a questo articolo. Al momento non è necessario completare il "passaggio 3" o qualsiasi altro elemento al suo interno in questa guida.

[Distribuzione di un'appliance virtuale StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Determinare un clone del volume da usare

Quando si è pronti per iniziare la migrazione, il primo passaggio consiste nell'acquisire un nuovo clone del volume, come per il backup, che acquisisce lo stato corrente della memoria cloud di StorSimple. Eseguire un clone per ogni volume StorSimple.
Se è necessario spostare i backup, il primo clone del volume utilizzato non è un clone appena creato, ma il clone del volume meno recente (backup meno recente) necessario migrare.
Per istruzioni dettagliate, vedere la sezione ["Considerazioni sui backup esistenti di StorSimple"](#considerations-around-existing-storsimple-backups) .

> [!IMPORTANT]
> Nella Guida seguente sono contenute alcune sezioni non necessarie. Leggere e seguire solo i passaggi descritti nella sezione collegata a. Tornare quindi a questo articolo. Non è necessario seguire la sezione "passaggi successivi".

[Creare un clone di un volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Usare il clone del volume

L'ultima fase della fase 1 consiste nel creare il clone del volume scelto, disponibile nell'appliance virtuale 8020 in Azure.

> [!IMPORTANT]
> La guida seguente contiene i passaggi necessari, ma anche-alla fine, un'istruzione per formattare il volume. **non formattare il volume** Leggere e seguire il collegamento "Section 7" dall'inizio fino all'istruzione: "10. Per formattare un volume semplice,... "  Arrestare prima di seguire questo passaggio e tornare a questo articolo.

[Montare un clone del volume nell'appliance virtuale 8020 in Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Riepilogo fase 1

Ora che è stata completata la fase 1, sarà necessario eseguire le operazioni seguenti:

* È stata distribuita un'appliance virtuale StorSimple 8020 in Azure.
* Determinare quale clone del volume si avvierà per la migrazione.
* Sono stati montati i cloni del volume (uno per ogni volume Live) nell'appliance virtuale StorSimple in Azure, con i relativi dati disponibili per un ulteriore uso.

## <a name="phase-2-cloud-vm"></a>Fase 2: VM cloud

:::row:::
    :::column:::
        ![Un'immagine che illustra una parte della precedente immagine di panoramica che consente di concentrare questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Dopo che il clone iniziale è disponibile nell'appliance virtuale StorSimple 8020 in Azure, è ora necessario effettuare il provisioning di una macchina virtuale ed esporre il clone del volume (o più) alla macchina virtuale tramite iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Distribuire una macchina virtuale di Azure

La macchina virtuale Windows Server in Azure è esattamente come StorSimple 8020, un'infrastruttura temporanea che è necessaria solo durante la migrazione.
La configurazione della macchina virtuale distribuita dipende principalmente dal numero di elementi (file e cartelle) che verranno sincronizzati. Se si verificano problemi, è consigliabile procedere con una configurazione di prestazioni superiore.

Un singolo server Windows può sincronizzare fino a 30 condivisioni file di Azure.
Le specifiche scelte devono includere ogni condivisione/percorso o la radice del volume StorSimple e contare gli elementi (file e cartelle).

La dimensione complessiva dei dati è minore di un collo di bottiglia. si tratta del numero di elementi necessari per adattare le specifiche del computer a.

* [Informazioni su come ridimensionare un server Windows in base al numero di elementi (file e cartelle) che è necessario sincronizzare.](storage-sync-files-planning.md#recommended-system-resources)

    **Nota:** L'articolo collegato in precedenza presenta una tabella con un intervallo di memoria del server (RAM). Orientarsi verso il numero elevato per la macchina virtuale di Azure. È possibile orientarsi verso il numero più piccolo per il computer locale.

* [Informazioni su come distribuire una VM Windows Server.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Assicurarsi che la macchina virtuale sia distribuita nella stessa area di Azure dell'appliance virtuale StorSimple 8020. Se come parte di questa migrazione, è necessario modificare anche l'area dei dati cloud dall'area in cui è archiviata, è possibile eseguire questa operazione in un passaggio successivo, quando si esegue il provisioning di condivisioni file di Azure.

> [!IMPORTANT]
> Spesso, un server Windows locale viene usato per l'uso dell'appliance StorSimple locale. In tale configurazione è possibile abilitare la funzionalità di[Deduplicazione dati](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)in tale server Windows. **Se è stata usata la deduplicazione dei dati con i dati di StorSimple, assicurarsi di abilitare la deduplicazione dati anche in questa macchina virtuale di Azure.** Non confondere questa deduplicazione a livello di file con la deduplicazione a livello di blocco incorporata StorSimples, per cui non è necessaria alcuna azione.

> [!IMPORTANT]
> Per ottimizzare le prestazioni, distribuire un **disco del sistema operativo veloce** per la VM cloud. Il database di sincronizzazione viene archiviato nel disco del sistema operativo per tutti i volumi di dati. Assicurarsi inoltre di creare un disco del **sistema operativo di grandi dimensioni**. A seconda del numero di elementi (file e cartelle) sui volumi StorSimple, il disco del sistema operativo potrebbe richiedere un **centinaio di GIB** di spazio per contenere il database di sincronizzazione.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Esporre i volumi StorSimple 8020 alla macchina virtuale di Azure

In questa fase si connettono uno o più volumi StorSimple dall'appliance virtuale 8020 tramite iSCSI alla macchina virtuale Windows Server di cui è stato effettuato il provisioning.

> [!IMPORTANT]
> Per gli articoli seguenti, completare solo le sezioni **ottenere un indirizzo IP privato per l'appliance cloud** e **connettersi a iSCSI** e tornare a questo articolo.

1. [Ottenere l'indirizzo IP privato per l'appliance cloud](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Connetti tramite iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Riepilogo fase 2

Ora che è stata completata la fase 2, sono disponibili: 

* Provisioning di una macchina virtuale Windows Server nella stessa area dell'appliance StorSimple virtuale 8020
* Sono stati esposti tutti i volumi applicabili dalla 8020 alla macchina virtuale Windows Server tramite iSCSI.
* A questo punto è possibile visualizzare il contenuto di file e cartelle, quando si usa Esplora file nella macchina virtuale del server sui volumi montati.

Procedere solo alla fase 3 dopo aver completato questi passaggi per tutti i volumi che necessitano di migrazione.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fase 3: configurare condivisioni file di Azure e prepararsi per Sincronizzazione file di Azure

:::row:::
    :::column:::
        ![Un'immagine che illustra una parte della precedente immagine di panoramica che consente di concentrare questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        In questa fase si stabilirà come determinare e effettuare il provisioning di un numero di condivisioni file di Azure, creare un'istanza locale di Windows Server come sostituzione dell'appliance StorSimple e configurare tale server per Sincronizzazione file di Azure. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Eseguire il mapping degli spazi dei nomi esistenti alle condivisioni file di Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Distribuire condivisioni file di Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Se è necessario modificare l'area di Azure dall'area corrente in cui risiedono i dati di StorSimple, effettuare il provisioning delle condivisioni file di Azure nella nuova area che si vuole usare. Per determinare l'area, selezionarla quando si effettua il provisioning degli account di archiviazione che contengono le condivisioni file di Azure. Assicurarsi anche che la risorsa Sincronizzazione file di Azure di cui si eseguirà il provisioning, si trovi nella stessa area nuova.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Distribuire la risorsa Sincronizzazione file di Azure cloud

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se è necessario modificare l'area di Azure dall'area corrente in cui risiedono i dati di StorSimple, è stato effettuato il provisioning degli account di archiviazione per le condivisioni file di Azure nella nuova area. Assicurarsi di aver selezionato la stessa area quando si distribuisce questo servizio di sincronizzazione archiviazione.

### <a name="deploy-an-on-premises-windows-server"></a>Distribuire un server Windows locale

* Creare una macchina virtuale di Windows Server 2019, almeno 2012R2, come macchina virtuale o server fisico. È supportato anche un cluster di failover di Windows Server. Non riutilizzare il server che si potrebbe avere davanti a StorSimple 8100 o 8600.
* Effettuare il provisioning o aggiungere l'archiviazione collegata direttamente (DAS rispetto a NAS, che non è supportata).

È consigliabile assegnare al nuovo Windows Server una quantità di spazio di archiviazione uguale o superiore a quella del dispositivo StorSimple 8100 o 8600 disponibile localmente per la memorizzazione nella cache. Si userà Windows Server nello stesso modo in cui è stato usato il dispositivo StorSimple, se ha la stessa quantità di spazio di archiviazione dell'appliance, l'esperienza di memorizzazione nella cache dovrebbe essere simile, in caso contrario.
È possibile aggiungere o rimuovere lo spazio di archiviazione dal server Windows in base alle esigenze. In questo modo è possibile ridimensionare le dimensioni del volume locale e la quantità di spazio di archiviazione locale disponibile per la memorizzazione nella cache.

### <a name="prepare-the-windows-server-for-file-sync"></a>Preparare Windows Server per la sincronizzazione dei file

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Configurare Sincronizzazione file di Azure in Windows Server

Per questo processo, è necessario che il server Windows locale registrato sia pronto e connesso a Internet.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Assicurarsi di attivare la suddivisione in livelli nel cloud.** La suddivisione in livelli nel cloud è la funzionalità AFS che consente al server locale di avere una capacità di archiviazione inferiore a quella archiviata nel cloud, ma è disponibile lo spazio dei nomi completo. I dati localmente interessanti vengono anche memorizzati nella cache localmente per ottenere prestazioni di accesso locale rapide. Un altro motivo per attivare la suddivisione in livelli nel cloud in questo passaggio è che non si vuole sincronizzare il contenuto del file in questa fase, ma è necessario che lo spazio dei nomi venga spostato in questo momento.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fase 4: configurare la macchina virtuale di Azure per la sincronizzazione

:::row:::
    :::column:::
        ![Un'immagine che illustra una parte della precedente immagine di panoramica che consente di concentrare questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Questa fase riguarda la macchina virtuale di Azure con iSCSI montati, i primi cloni del volume. Durante questa fase, si otterrà la connessione della macchina virtuale tramite Sincronizzazione file di Azure e si avvierà un primo ciclo di file da un clone del volume StorSimple.
        
    :::column-end:::
:::row-end:::

È già stato configurato il server locale che sostituirà l'appliance StorSimple 8100 o 8600, per Sincronizzazione file di Azure. 

La configurazione della macchina virtuale di Azure è un processo quasi identico, con un passaggio aggiuntivo. Nei passaggi seguenti viene illustrato il processo.

> [!IMPORTANT]
> È importante che la macchina virtuale di Azure **non sia configurata con la suddivisione in livelli cloud abilitata.** Il volume di questo server verrà scambiato con i cloni dei volumi più recenti durante la migrazione. La suddivisione in livelli nel cloud non offre alcun vantaggio e sovraccarico per l'utilizzo della CPU da evitare.

1. [Distribuire l'agente AFS. (vedere la sezione precedente)](#prepare-the-windows-server-for-file-sync)
2. [Preparare la macchina virtuale per Sincronizzazione file di Azure.](#get-the-vm-ready-for-azure-file-sync)
3. [Configurare la sincronizzazione](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Preparare la macchina virtuale per Sincronizzazione file di Azure

Sincronizzazione file di Azure viene usato per spostare i file dai volumi StorSimple iSCSI montati alle condivisioni file di Azure di destinazione.
Durante questo processo di migrazione, sarà possibile montare diversi cloni di volume nella macchina virtuale, con la stessa lettera di unità. È necessario configurare Sincronizzazione file di Azure per visualizzare il successivo clone del volume montato come versione più recente dei file e delle cartelle e aggiornare le condivisioni file di Azure connesse tramite Sincronizzazione file di Azure. 

> [!IMPORTANT]
> Per il corretto funzionamento, è necessario impostare una chiave del registro di sistema sul server prima di configurare Sincronizzazione file di Azure.

1. Creare una nuova directory nell'unità di sistema della macchina virtuale. Sincronizzazione file di Azure le informazioni devono essere salvate in modo permanente anziché nei cloni dei volumi montati. Ad esempio: `"C:\syncmetadata"`
2. Aprire regedit e individuare l'hive del registro di sistema seguente:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Creare una nuova chiave di tipo String, denominata: ***MetadataRootPath***
4. Impostare il percorso completo della directory creata nel volume di sistema, ad esempio:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Configurare Sincronizzazione file di Azure nella macchina virtuale di Azure

Questo passaggio è simile a quello della sezione precedente, in cui viene illustrato come configurare AFS nel server locale.

La differenza consiste nel fatto che non è necessario abilitare la suddivisione in livelli cloud in questo server e che è necessario assicurarsi che le cartelle corrette siano connesse alle condivisioni file di Azure corrette. In caso contrario, i nomi delle condivisioni file di Azure e del contenuto dati non corrispondono e non è possibile rinominare le risorse cloud o le cartelle locali senza riconfigurare la sincronizzazione.

Vedere la [sezione precedente su come configurare sincronizzazione file di Azure in un server Windows](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Riepilogo del passaggio 4

A questo punto, è stato configurato correttamente Sincronizzazione file di Azure nella macchina virtuale di Azure in cui sono stati montati i cloni del volume StorSimple tramite iSCSI.
I dati vengono ora propagati dalla macchina virtuale di Azure alle varie condivisioni file di Azure e da qui viene visualizzato uno spazio dei nomi completamente stanco nell'istanza locale di Windows Server.

> [!IMPORTANT]
> Assicurarsi che non siano state apportate modifiche o che l'accesso utente sia stato concesso al server Windows in questo momento.

I dati di clonazione del volume iniziale che passano attraverso la macchina virtuale di Azure alle condivisioni file di Azure possono richiedere molto tempo, potenzialmente settimane. La stima del tempo che questa operazione sarà complessa e dipende da molti fattori. In particolare, la velocità con cui la macchina virtuale di Azure può accedere ai file nei volumi StorSimple e la velocità con cui Sincronizzazione file di Azure possibile elaborare i file e le cartelle che devono essere sincronizzati. 

Dall'esperienza, si può presupporre che la larghezza di banda, quindi la dimensione effettiva dei dati, riproduca un ruolo subordinato. Il tempo impiegato da questo o da un ciclo di migrazione successivo dipende principalmente dal numero di elementi che possono essere elaborati al secondo. Quindi, ad esempio, 1 TiB con file e cartelle di 100.000 avrà una velocità superiore a 1 TiB con soli 50.000 file e cartelle.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fase 5: scorrere più cloni di volume

:::row:::
    :::column:::
        ![Un'immagine che illustra una parte della precedente immagine di panoramica che consente di concentrare questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Come illustrato nella fase precedente, la sincronizzazione iniziale può richiedere molto tempo. Gli utenti e le applicazioni accedono ancora all'appliance locale StorSimple 8100 o 8600. Ciò significa che le modifiche si accumulano e, con ogni giorno, un delta più grande tra i dati attivi e il clone del volume iniziale, si stanno attualmente migrando, moduli. In questa sezione si apprenderà come ridurre al minimo i tempi di inattività usando più cloni di volume e indicando quando viene eseguita la sincronizzazione.
    :::column-end:::
:::row-end:::

Sfortunatamente, il processo di migrazione non è istantaneo. Ciò significa che il Delta precedente per i dati attivi è una conseguenza inevitabile. La novità è che è possibile ripetere il processo di montaggio dei nuovi cloni del volume. Ogni Delta del clone del volume sarà progressivamente minore. Quindi, la sincronizzazione verrà completata in un periodo di tempo che è possibile considerare accettabile per portare offline gli utenti e le app al fine di passare al server Windows locale.

Ripetere i passaggi seguenti fino a quando la sincronizzazione non viene completata in una durata abbastanza rapida da consentire agli utenti e alle app di essere offline:

1. [Determinare se la sincronizzazione è stata completata per un determinato clone del volume.](#determine-when-sync-is-done)
2. [Prendere un nuovo clone del volume e montarlo nell'appliance virtuale 8020.](#the-next-volume-clones)
3. [Determinare quando viene eseguita la sincronizzazione.](#determine-when-sync-is-done)
4. [Strategia di riduzione](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>I successivi cloni del volume

In questo articolo è stato illustrato come eseguire uno o più cloni di volume.
Questa fase prevede due azioni:

1. [Eseguire un clone del volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Montare il clone del volume (vedere sopra)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Determinare al termine della sincronizzazione

Al termine della sincronizzazione, è possibile arrestare la misurazione del tempo e determinare se è necessario ripetere il processo di creazione di un clone del volume e montarlo o se la sincronizzazione dell'ora eseguita con l'ultimo clone del volume era sufficientemente piccola.

Per determinare se la sincronizzazione è stata completata:

1. Aprire il Visualizzatore eventi e passare ad **applicazioni e servizi**
2. Esplorare e aprire **Microsoft\FileSync\Agent\Telemetry**
3. Cercare l'evento più recente **9102**, che corrisponde a una sessione di sincronizzazione completata
4. Selezionare **Details (dettagli** ) e verificare che il valore **SyncDirection** sia **upload**
5. Controllare **HRESULT** e confermare che mostra **0**. Ciò significa che la sessione di sincronizzazione ha avuto esito positivo. Se HResult è un valore diverso da zero, si è verificato un errore durante la sincronizzazione. Se **PerItemErrorCount** è maggiore di 0, alcuni file o cartelle non sono stati sincronizzati correttamente. È possibile ricevere un valore HResult pari a 0, ma al contempo un PerItemErrorCount maggiore di 0. A questo punto, non è necessario preoccuparsi del PerItemErrorCount. Questi file vengono intercettati in un secondo momento. Se il numero di errori è significativo, migliaia di elementi, contattare il supporto tecnico e chiedere di essere connessi al gruppo di prodotti Sincronizzazione file di Azure per indicazioni dirette sulle fasi migliori e successive.
6. Controllare per visualizzare più eventi 9102 con HResult 0 in una riga. Indica che la sincronizzazione è stata completata per questo clone del volume.

### <a name="cut-over-strategy"></a>Strategia di riduzione

1. Determinare se la sincronizzazione da un clone del volume è ora sufficientemente veloce. (Delta sufficientemente piccolo).
2. Portare offline il dispositivo StorSimple.
3. RoboCopy finale.

Misurare il tempo e determinare se la sincronizzazione da un clone del volume recente può terminare entro un intervallo di tempo abbastanza piccolo, che è possibile permettersi come tempo di inattività nel sistema.

A questo punto è possibile disabilitare l'accesso utente al dispositivo StorSimple. Non sono state apportate altre modifiche. Il tempo di inattività è iniziato.
È necessario lasciare l'appliance online e connessa, ma è ora necessario impedire le modifiche.

Nella fase 6 si raggiungerà qualsiasi Delta nei dati dinamici dall'ultimo clone del volume.

## <a name="phase-6-a-final-robocopy"></a>Fase 6: RoboCopy finale

A questo punto, esistono due differenze tra il server Windows locale e l'appliance StorSimple 8100 o 8600:

1. Potrebbero essere presenti file che non sono stati sincronizzati (vedere **PerItemErrors** dal registro eventi precedente)
2. Il dispositivo StorSimple dispone di una cache popolata rispetto a Windows Server solo uno spazio dei nomi senza contenuto di file archiviato localmente in questo momento.

![Un'immagine che illustra una parte della precedente immagine di panoramica che consente di concentrare questa sottosezione dell'articolo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

È possibile riportare la cache di Windows Server allo stato del dispositivo e assicurarsi che nessun file venga lasciato con un RoboCopy finale.

> [!CAUTION]
> È fondamentale che il comando RoboCopy seguito sia esattamente come descritto di seguito. Si vuole solo copiare i file locali e i file che non sono stati spostati in precedenza tramite l'approccio clone del volume e sincronizzazione. È possibile risolvere i problemi perché non sono stati sincronizzati in un secondo momento, al termine della migrazione. Vedere [sincronizzazione file di Azure risoluzione dei problemi](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). È molto probabile che i caratteri non stampabili nei nomi di file non vengano rilasciati quando vengono eliminati.

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
      Consente a RoboCopy di eseguire multithread. Il valore predefinito è 8, Max è 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Restituisce lo stato del file di LOG come UNICODE (sovrascrive il log esistente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Output nella finestra della console. Utilizzato in combinazione con l'output in un file di log.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / B
   :::column-end:::
   :::column span="1":::
      Esegue RoboCopy nella stessa modalità usata da un'applicazione di backup. Consente a RoboCopy di spostare i file che l'utente corrente non dispone delle autorizzazioni per.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Consente a RoboCopy di prendere in considerazione solo i delta tra l'origine (StorSimple Appliance) e la destinazione (directory di Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fedeltà della copia del file (il valore predefinito è/COPY: DAT), flag di copia: D = dati, A = attributi, T = timestamp, S = Security = ACL NTFS, O = informazioni sul proprietario, U = informazioni sul controllo
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      COPIA tutte le informazioni sul file (equivalente a/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fedeltà per la copia delle directory (il valore predefinito è/DCOPY: DA), i flag di copia: D = dati, A = attributi, T = timestamp
   :::column-end:::
:::row-end:::

È consigliabile eseguire questo comando RoboCopy per ogni directory di Windows Server come destinazione, configurato con la sincronizzazione dei file in un file di Azure.

È possibile eseguire più comandi in parallelo.
Al termine di questo passaggio di RoboCopy, è possibile consentire agli utenti e alle app di accedere al server Windows come hanno fatto il dispositivo StorSimple.

Per verificare se i file sono stati lasciati, consultare i file di log Robocopy. Se si verificano problemi, nella maggior parte dei casi è possibile risolverli una volta completata la migrazione e le app e gli utenti sono stati riassegnati al server Windows. Se è necessario risolvere eventuali problemi, eseguire questa operazione prima della fase 7.

È probabile che sia necessario creare le condivisioni SMB nel server Windows in cui si trovavano i dati StorSimple prima. È possibile eseguire il caricamento in primo piano di questo passaggio e farlo in precedenza in modo da non perdere tempo, ma è necessario assicurarsi che prima di questo punto non vengano apportate modifiche ai file nel server Windows.

Se si dispone di una distribuzione DFS-N, è possibile puntare gli spazi dei nomi DFN ai nuovi percorsi delle cartelle del server. Se non si dispone di una distribuzione di DFS-N e l'appliance 8100 8600 è stata eseguita localmente con un server Windows, è possibile disconnettere il server e aggiungere il dominio al nuovo Windows Server con AFS al dominio, assegnargli lo stesso nome del server precedente e gli stessi nomi di condivisione, quindi il taglio al nuovo server rimane trasparente per gli utenti , criteri di gruppo o script.

## <a name="phase-7-deprovision"></a>Fase 7: deprovisioning

Durante l'ultima fase è stata eseguita l'iterazione di più cloni di volume e, infine, è stato possibile ridurre l'accesso degli utenti al nuovo Windows Server dopo aver portato StorSimple Appliance offline.

È ora possibile iniziare a eseguire il deprovisioning delle risorse non necessarie.
Prima di iniziare, è consigliabile osservare la nuova distribuzione di Sincronizzazione file di Azure in produzione, per un po'. Che consente di risolvere eventuali problemi che possono verificarsi.

Una volta soddisfatta la distribuzione di AFS per almeno alcuni giorni, è possibile iniziare a eseguire il deprovisioning delle risorse nell'ordine seguente:

1. Spegnere la macchina virtuale di Azure usata per spostare i dati dai cloni dei volumi alle condivisioni file di Azure tramite sincronizzazione file.
2. Passare alla risorsa del servizio di sincronizzazione archiviazione in Azure e annullare la registrazione della macchina virtuale di Azure. Che lo rimuove da tutti i gruppi di sincronizzazione.

    > [!WARNING]
    > **Assicurarsi di scegliere il computer corretto.** La VM cloud è stata disattivata, ovvero dovrebbe essere visualizzata come l'unico server offline nell'elenco di server registrati. Non è necessario selezionare l'istanza locale di Windows Server in questo passaggio. in questo modo verrà annullata la registrazione.

3. Eliminare la macchina virtuale di Azure e le relative risorse.
4. Disabilitare l'appliance StorSimple virtuale 8020.
5. Eseguire il deprovisioning di tutte le risorse di StorSimple in Azure.
6. Scollegare il dispositivo fisico StorSimple dalla data center.

La migrazione è stata completata.

## <a name="next-steps"></a>Passaggi successivi

Acquisire familiarità con Sincronizzazione file di Azure. Soprattutto con la flessibilità dei criteri di suddivisione in livelli nel cloud.

Se viene visualizzato nella portale di Azure o negli eventi precedenti, che alcuni file non sono sincronizzati in modo permanente, consultare la guida alla risoluzione dei problemi per i passaggi necessari per risolvere questi problemi.

* [Panoramica di Sincronizzazione file di Azure: aka.ms/AFS](https://aka.ms/AFS)
* [Suddivisione in livelli cloud](storage-sync-cloud-tiering.md) 
* [Guida alla risoluzione dei problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md)
