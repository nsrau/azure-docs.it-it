---
title: Eseguire la migrazione dei dati da un dispositivo StorSimple serie 5000/7000 a un dispositivo serie 8000 | Microsoft Docs
description: "Presenta una panoramica e i prerequisiti della funzionalità di migrazione."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/11/2017
ms.author: alkohli
ms.openlocfilehash: 36df62c4b01c623702707d39c6af59f4752ee6e0
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Eseguire la migrazione dei dati da un dispositivo StorSimple serie 5000/7000 a un dispositivo serie 8000

> [!IMPORTANT]
> - La migrazione è attualmente un'operazione assistita. Se si intende eseguire la migrazione dei dati da un dispositivo StorSimple serie 5000/7000 a un dispositivo serie 8000, è necessario pianificare l'operazione con il supporto tecnico Microsoft, che provvederà quindi ad abilitare la sottoscrizione per la migrazione. Per altre informazioni, vedere come [aprire un ticket di supporto](storsimple-8000-contact-microsoft-support.md).
> - Dopo l'invio della richiesta di servizio, può essere necessaria qualche settimana per eseguire il piano di migrazione e avviare effettivamente la migrazione.
> - Prima di contattare il supporto tecnico Microsoft, esaminare i [prerequisiti per la migrazione](#migration-prerequisites) indicati nell'articolo e verificare che siano soddisfatti.

## <a name="overview"></a>Panoramica

Questo articolo presenta una panoramica della funzionalità che consente ai clienti di dispositivi StorSimple serie 5000/7000 di eseguire la migrazione dei dati in un dispositivo fisico StorSimple serie 8000 o un'appliance cloud 8010/8020. È inoltre incluso un collegamento a un documento scaricabile contenente una procedura dettagliata con i passaggi necessari per la migrazione dei dati da un dispositivo legacy serie 5000/7000 a un dispositivo fisico serie 8000 o un'appliance cloud.

Questo articolo è applicabile sia al dispositivo serie 8000 locale sia all'appliance cloud StorSimple.


## <a name="migration-feature-versus-host-side-migration"></a>Confronto tra la funzionalità di migrazione e la migrazione sul lato host

È possibile spostare i dati usando la funzionalità di migrazione o eseguendo una migrazione sul lato host. Questa sezione descrive le caratteristiche delle due soluzioni e i relativi vantaggi e svantaggi. Usare queste informazioni per individuare la soluzione da adottare per eseguire la migrazione dei dati.

La funzionalità di migrazione simula un processo di ripristino di emergenza dal dispositivo serie 5000/7000 al dispositivo serie 8000 e consente di eseguire la migrazione dei dati dal formato della serie 5000/7000 a quello della serie 8000 in Azure. Il processo di migrazione viene avviato tramite lo strumento di migrazione StorSimple, che avvia il download e la conversione dei metadati di backup sul dispositivo serie 8000 e quindi usa il backup più recente per esporre i volumi sul dispositivo.

|      | Vantaggi                                                                                                                                     |Svantaggi                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Il processo di migrazione mantiene la cronologia dei backup che sono stati eseguiti sul dispositivo serie 5000/7000.                                               | Quando gli utenti provano ad accedere ai dati, il processo di migrazione scarica i dati da Azure con il conseguente addebito dei costi di download.                                     |
| 2.   | Non viene eseguita la migrazione di dati sul lato host.                                                                                                     | Questo processo comporta un tempo di inattività tra l'avvio del backup e il backup più recente visibile sul dispositivo serie 8000. Questo tempo può essere stimato tramite lo strumento di migrazione. |
| 3.   | Questo processo consente di mantenere tutti i criteri, i modelli di larghezza di banda, la crittografia e le altre impostazioni nei dispositivi serie 8000.                      | Con l'accesso utente vengono riattivati solo i dati accessibili agli utenti e non l'intero set di dati.                                                  |
| 4.   | Questo processo richiede più tempo per convertire tutti i backup precedenti in Azure. L'operazione viene eseguita in modo asincrono senza conseguenze sulla produzione. | La migrazione può essere eseguita solo al livello della configurazione cloud  e quindi non separatamente per i singoli volumi della configurazione.                       |

Una migrazione sul lato host consente di configurare il dispositivo serie 8000 in modo indipendente e di copiare i dati dal dispositivo serie 5000/7000 al dispositivo serie 8000. Il processo equivale alla migrazione dei dati da un dispositivo di archiviazione a un altro. Per la copia dei dati vengono usati vari strumenti, come Diskboss e robocopy.

|      | Vantaggi                                                                                                                      |Svantaggi                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | La migrazione può essere eseguita in maniera graduale, su un volume alla volta.                                               | I backup precedenti, creati sul dispositivo serie 5000/7000, non sono disponibili sul dispositivo serie 8000.                                                                                                       |
| 2.   | Consente di consolidamento dei dati in un unico account di archiviazione in Azure.                                                       | Il primo backup nel cloud per il dispositivo serie 8000 richiede più tempo perché è necessario eseguire il backup di tutti i dati del dispositivo in Azure.                                                                     |
| 3.   | Dopo la migrazione, tutti i dati sono archiviati in locale nell'appliance. Non si verificano problemi di latenza durante l'accesso ai dati. | L'utilizzo delle risorse di archiviazione di Azure aumenta finché i dati non vengono eliminati dal dispositivo serie 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Se il dispositivo serie 5000/7000 contiene una grande quantità di dati, durante la migrazione i dati devono essere scaricati da Azure. Ciò comporta un addebito di costi e problemi di latenza dovuti al download dei dati da Azure. |

Questo articolo illustra solo la funzionalità di migrazione da un dispositivo serie 5000/7000 a un dispositivo serie 8000. Per altre informazioni relative alla migrazione sul lato host, vedere [Migration from other storage devices](http://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating Data to StorSimple Volumes_09-02-15.pdf) (Migrazione da altri dispositivi di archiviazione).

## <a name="migration-prerequisites"></a>Prerequisiti per la migrazione

Di seguito sono elencati i prerequisiti per la migrazione di un dispositivo legacy serie 5000 o 7000 a un dispositivo StorSimple serie 8000.

> [!IMPORTANT]
> Prima di inviare una richiesta di servizio al supporto tecnico Microsoft, esaminare i prerequisiti per migrazione e verificare che siano soddisfatti.

### <a name="for-the-50007000-series-device-source"></a>Per il dispositivo serie 5000/7000 (origine)

Prima di iniziare la migrazione, verificare quanto segue:

* Si ha un dispositivo di origine serie 5000 o 7000. Il dispositivo può essere attivo o inattivo.

    > [!IMPORTANT]
    > È consigliabile disporre dell'accesso seriale al dispositivo durante il processo di migrazione. In caso di problemi con il dispositivo, l'accesso seriale può facilitarne la risoluzione.

* Il dispositivo di origine serie 5000 o 7000 è in esecuzione software versione v2.1.1.518 o versione successiva. Le versioni precedenti non sono supportate.
* Per verificare la versione sul dispositivo serie 5000 o 7000, controllare l'angolo in alto a destra dell'interfaccia utente Web, in cui è visualizzato il numero di versione del software in esecuzione. Per la migrazione, il dispositivo serie 5000 o 7000 deve eseguire la versione 2.1.1.518.

    ![Controllare la versione del software sul dispositivo legacy](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Se la periferica in tempo reale non è in esecuzione v2.1.1.518 o versione successiva, aggiornare il sistema alla versione minima richiesta. Per istruzioni dettagliate, vedere [Upgrade your system to v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518) (Aggiornare il sistema alla versione 2.1.1.518).
    * Se il dispositivo esegue la versione 2.1.1.518, accedere all'interfaccia utente Web per verificare se sono presenti notifiche relative a errori di ripristino del registro. In caso affermativo, eseguire il ripristino del registro. Per altre informazioni, vedere come [eseguire il ripristino del registro](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Se si ha un dispositivo inattivo che non eseguiva la versione 2.1.1.518, procedere a un failover in un dispositivo sostitutivo che esegue tale versione. Per istruzioni dettagliate, vedere le informazioni sul ripristino di emergenza del dispositivo StorSimple serie 5000/7000.
    * Creare una copia di backup dei dati del dispositivo eseguendo uno snapshot cloud.
    * Verificare se sul dispositivo di origine sono in esecuzione altri processi di backup attivi, inclusi quelli sull'host di StorSimple Data Protection Console. Attendere che i processi in corso siano completati.


### <a name="for-the-8000-series-physical-device-target"></a>Per il dispositivo fisico serie 8000 (destinazione)

Prima di iniziare la migrazione, verificare quanto segue:

* Il dispositivo di destinazione serie 8000 è registrato e in esecuzione. Per altre informazioni, vedere come [distribuire il dispositivo StorSimple con il servizio StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* Nel dispositivo serie 8000 è installato il software più recente, StorSimple 8000 Series Update 5, ed è in esecuzione la versione 6.3.9600.17845 o successiva. Se nel dispositivo non sono installati gli aggiornamenti più recenti, prima di procedere alla migrazione è necessario installarli. Per altre informazioni, vedere come [installare l'aggiornamento più recente nel dispositivo StorSimple serie 8000](storsimple-8000-install-update-5.md).
* La sottoscrizione di Azure è abilitata per la migrazione. In caso contrario, contattare il supporto tecnico Microsoft per abilitarla per la migrazione.

### <a name="for-the-80108020-cloud-appliance-target"></a>Per l'appliance cloud 8010/8020 (destinazione)

Prima di iniziare la migrazione, verificare quanto segue:

* L'appliance cloud di destinazione è registrata e in esecuzione. Per altre informazioni, vedere come [distribuire e gestire un'appliance cloud StorSimple](storsimple-8000-cloud-appliance-u2.md).
* L'appliance cloud esegue il software StorSimple 8000 Series Update 5 più recente, versione 6.3.9600.17845. In caso contrario, creare una nuova appliance cloud con Update 5 prima di procedere alla migrazione. Per altre informazioni, vedere come [creare un'appliance cloud 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Per il computer che esegue lo strumento di migrazione StorSimple

Lo strumento di migrazione StorSimple è uno strumento basato su interfaccia utente che consente di eseguire la migrazione dei dati da un dispositivo StorSimple serie 5000/7000 a un dispositivo serie 8000. Per installare lo strumento di migrazione StorSimple, usare un computer che soddisfa i requisiti seguenti.

Nel computer è disponibile la connettività Internet e:

* È in esecuzione il sistema operativo seguente:
    * Windows 10.
    * Windows Server 2012 R2 (o versione successiva) per installare lo strumento di migrazione StorSimple.
* È installato .NET Framework 4.5.2.
* Sono disponibili almeno 5 GB di spazio per installare e usare lo strumento.

> [!TIP]
> Se il dispositivo StorSimple è connesso a un host Windows Server, è possibile installare lo strumento di migrazione nel computer host.

#### <a name="to-install-storsimple-migration-tool"></a>Per installare lo strumento di migrazione StorSimple

Per installare lo strumento di migrazione StorSimple nel computer, seguire questa procedura.

1. Copiare la cartella _StorSimple8000SeriesMigrationTool_ nel computer Windows. Verificare che nell'unità in cui si copia il software sia disponibile spazio sufficiente.

    Aprire il file di configurazione dello strumento _StorSimple8000SeriesMigrationTool.exe.config_ nella cartella. Di seguito è riportato il frammento del file.
    
    ```
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Modificare i valori corrispondenti alle chiavi e sostituirli con i valori seguenti:

    * `UserName`: nome utente per accedere al portale di Azure.
    * `SubscriptionName and SubscriptionId`: nome e ID della sottoscrizione di Azure. Nella pagina di destinazione del servizio Gestione dispositivi StorSimple, in **Generale**, fare clic su **Proprietà**. Copiare il nome e l'ID della sottoscrizione associati al servizio.
    * `ResourceName`: nome del servizio Gestione dispositivi StorSimple nel portale di Azure, come visualizzato nelle proprietà del servizio.
    * `ResourceGroup`: nome del gruppo di risorse associato al servizio Gestione dispositivi StorSimple nel portale di Azure, come visualizzato nelle proprietà del servizio.
    ![Verificare le proprietà del servizio per il dispositivo di destinazione](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`: ID del tenant di Azure Active Directory nel portale di Azure. Accedere a Microsoft Azure come amministratore. Nel portale di Microsoft Azure fare clic su **Azure Active Directory**. In **Gestione** fare clic su **Proprietà**. L'ID del tenant è visualizzato nella casella **ID directory**.
    ![Verificare l'ID del tenant per Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Salvare le modifiche apportate al file di configurazione.
4.  Eseguire _StorSimple8000SeriesMigrationTool.exe_ per avviare lo strumento. Quando vengono richieste le credenziali, specificare quelle associate alla propria sottoscrizione nel portale di Azure. 
5.  Verrà visualizzata l'interfaccia utente dello strumento di migrazione StorSimple.
  

## <a name="next-steps"></a>Passaggi successivi
Scaricare il documento con le istruzioni dettagliate su come [eseguire la migrazione da un dispositivo StorSimple serie 5000/7000 a un dispositivo serie 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
