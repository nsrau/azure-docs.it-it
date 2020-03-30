---
title: StorSimple 1200 migration to Azure File Sync
description: Informazioni su come eseguire la migrazione di un'appliance virtuale della serie StorSimple 1200 a Sincronizzazione file di Azure.Learn how to migrate a StorSimple 1200 series virtual appliance to Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502390"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 migration to Azure File Sync

La serie StorSimple 1200 è un'appliance virtuale eseguita in un data center locale. È possibile eseguire la migrazione dei dati da questa appliance a un ambiente di sincronizzazione file di Azure.It is possible to migrate the data from this appliance to an Azure File Sync environment. Sincronizzazione file di Azure è il servizio di Azure predefinito e strategico a lungo termine in cui è possibile eseguire la migrazione delle appliance StorSimple.Azure File Sync is the default and strategic azure service that StorSimple appliances can be migrated to.

La serie StorSimple 1200 raggiungerà la [fine del ciclo di vita](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) nel dicembre 2022.  È importante iniziare a pianificare la migrazione il prima possibile. Questo articolo fornisce le conoscenze di base necessarie e i passaggi di migrazione per una corretta migrazione a Sincronizzazione file di Azure.This article provides the necessary background knowledge and migrations steps for a successful migration to Azure File Sync. 

## <a name="azure-file-sync"></a>Sincronizzazione file di Azure

> [!IMPORTANT]
> Microsoft si impegna ad assistere i clienti nella migrazione. E-mail AzureFilesMigration@microsoft .com per un piano di migrazione personalizzato e assistenza durante la migrazione.

Sincronizzazione file di Azure è un servizio cloud Microsoft basato su due componenti principali:Azure File Sync is a Microsoft cloud service, based on two main components:

* Sincronizzazione dei file e suddivisione in livelli nel cloud.
* Condivisioni di file come archiviazione nativa in Azure, a cui è possibile accedere tramite più protocolli, ad esempio SMB e REST di file. Una condivisione file di Azure è paragonabile a una condivisione file in un Windows Server, che è possibile montare in modo nativo come unità di rete. Supporta importanti aspetti di fedeltà dei file come attributi, autorizzazioni e timestamp. A differenza di StorSimple, non è necessaria alcuna applicazione/servizio per interpretare i file e le cartelle archiviati nel cloud. L'approccio ideale e più flessibile per archiviare i dati del file server generico e alcuni dati dell'applicazione nel cloud.

Questo articolo è incentrato sui passaggi di migrazione. Se prima di eseguire la migrazione si desidera ottenere altre informazioni su Sincronizzazione file di Azure, è consigliabile eseguire le procedure seguenti:If before migrating you're learn more about Azure File Sync, we recommend the following articles:

* [Sincronizzazione file di Azure - Panoramica](https://aka.ms/AFS "Panoramica")
* [Sincronizzazione file di Azure - guida alla distribuzioneAzure File Sync - deployment guide](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Obiettivi della migrazione

L'obiettivo è garantire l'integrità dei dati di produzione e garantire la disponibilità. Quest'ultimo richiede di mantenere i tempi di inattività al minimo, in modo che possa adattarsi o superare solo leggermente le finestre di manutenzione regolari.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200 migration path to Azure File Sync

Per eseguire un agente di Sincronizzazione file di Azure è necessario un Windows Server locale. Windows Server può essere almeno un server 2012R2 ma idealmente è un Windows Server 2019.

Esistono numerosi percorsi di migrazione alternativi e creerebbe troppo a lungo di un articolo per documentarli tutti e illustrare perché sono in grado di sostenere rischi o svantaggi lungo il percorso che si consiglia come procedura consigliata in questo articolo.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Panoramica del percorso di migrazione dei passaggi più avanti in questo articolo.":::

L'immagine precedente illustra i passaggi che corrispondono alle sezioni di questo articolo.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Passaggio 1: Effettuare il provisioning di Windows Server e archiviazione localiStep 1: Provision your on-premises Windows Server and storage

1. Creare un Windows Server 2019 - almeno 2012R2 - come macchina virtuale o server fisico. È supportato anche un cluster di failover di Windows Server.A Windows Server fail-over cluster is also supported.
2. Effettuare il provisioning o aggiungere DAS (Direct Attached Storage) rispetto al NAS, che non è supportato. La dimensione dell'archiviazione di Windows Server deve essere uguale o maggiore della capacità disponibile dell'appliance virtuale StorSimple 1200.

### <a name="step-2-configure-your-windows-server-storage"></a>Passaggio 2: Configurare l'archiviazione di Windows ServerStep 2: Configure your Windows Server storage

In questo passaggio viene eseguito il mapping della struttura di archiviazione StorSimple (volumi e condivisioni) alla struttura di archiviazione di Windows Server.In this step, you map your StorSimple storage structure (volumes and shares) to your Windows Server storage structure.
Se si prevede di apportare modifiche alla struttura di archiviazione, ovvero il numero di volumi, l'associazione delle cartelle di dati ai volumi o la struttura di sottocartelle sopra o sotto le condivisioni SMB/NFS correnti, è giunto il momento di prendere in considerazione tali modifiche.
La modifica della struttura di file e cartelle dopo la configurazione di Sincronizzazione file di Azure è complessa e deve essere evitata.
In questo articolo si presuppone che si sta mappando 1:1, pertanto è necessario prendere in considerazione le modifiche di mapping quando si esegue la procedura descritta in questo articolo.

* Nessuno dei dati di produzione deve finire nel volume di sistema di Windows Server. La suddivisione in livelli cloud non è supportata nei volumi di sistema. Tuttavia, questa funzionalità è necessaria per la migrazione e le operazioni continue come sostituzione StorSimple.However, this feature is required for the migration as well as continuous operations as a StorSimple replacement.
* Effettuare il provisioning dello stesso numero di volumi nell'appliance virtuale StorSimple 1200 nell'appliance virtuale StorSimple 1200.
* Configurare i ruoli, le funzionalità e le impostazioni di Windows Server necessari. Ti consigliamo di optare per gli aggiornamenti di Windows Server per mantenere il tuo sistema operativo sicuro e aggiornato. Analogamente, è consigliabile scegliere Microsoft Update per mantenere aggiornate le applicazioni Microsoft, incluso l'agente di Sincronizzazione file di Azure.Similarly, we recommend opting into Microsoft Update to keep Microsoft applications up to date, including the Azure File Sync agent.
* Non configurare alcuna cartella o condivisione prima di leggere i passaggi seguenti.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Passaggio 3: Distribuire la prima risorsa cloud di Sincronizzazione file di AzureStep 3: Deploy the first Azure File Sync cloud resource

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Passaggio 4: Associare il volume locale e la struttura di cartelle alle risorse di Sincronizzazione file di Azure e Condivisione file di AzureStep 4: Match your local volume and folder structure to Azure File Sync and Azure file share resources

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Passaggio 5: Effettuare il provisioning delle condivisioni file di AzureStep 5: Provision Azure file shares

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Passaggio 6: Configurare le cartelle di destinazione di Windows ServerStep 6: Configure Windows Server target folders

Nei passaggi precedenti sono stati considerati tutti gli aspetti che determineranno i componenti delle topologie di sincronizzazione. È giunto il momento di preparare il server per ricevere i file per il caricamento.

Creare **tutte le** cartelle che verranno sincronizzate con la propria condivisione file di Azure.Create all folders, that will sync each to its own Azure file share.
È importante seguire la struttura di cartelle documentata in precedenza. Se, ad esempio, si è deciso di sincronizzare più condivisioni SMB locali in un'unica condivisione file di Azure, è necessario inserirle in una cartella radice comune nel volume. Creare questa cartella radice di destinazione sul volume ora.

Il numero di condivisioni file di Azure di cui è stato eseguito il provisioning deve corrispondere al numero di cartelle create in questo passaggio, ovvero al numero di volumi che verranno sincronizzati a livello di radice.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Passaggio 7: Distribuire l'agente di Sincronizzazione file di AzureStep 7: Deploy the Azure File Sync agent

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Passaggio 8: Configurare la sincronizzazioneStep 8: Configure sync

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Assicurati di attivare la suddivisione in livelli cloud!** Questa operazione è necessaria se il server locale non dispone di spazio sufficiente per archiviare le dimensioni totali dei dati nell'archiviazione cloud StorSimple. Impostare i criteri di suddivisione in livelli, temporaneamente per la migrazione, al 99% di spazio libero del volume.

Ripetere i passaggi per la creazione e l'aggiunta del gruppo di sincronizzazione come endpoint server per tutte le condivisioni file/percorsi del server di Azure che devono essere configurate per la sincronizzazione.

### <a name="step-9-copy-your-files"></a>Passaggio 9: Copiare i file

L'approccio di migrazione di base è RoboCopy dall'appliance virtuale StorSimple a Windows Server e Sincronizzazione file di Azure con le condivisioni file di Azure.The basic migration approach is a RoboCopy from your StorSimple virtual appliance to your Windows Server, and Azure File Sync to Azure file shares.

Eseguire la prima copia locale nella cartella di destinazione di Windows Server:

* Identificare la prima posizione nell'appliance StorSimple virtuale.
* Identificare la cartella corrispondente in Windows Server, in cui è già configurata La sincronizzazione file di Azure.Identify the matching folder on the Windows Server, that already have Azure File Sync configured on it.
* Avviare la copia utilizzando RoboCopy

Il comando RoboCopy seguente richiamerà i file dall'archivio di Azure StorSimple all'archivio StorSimple locale e quindi li sposterà nella cartella di destinazione di Windows Server.The following RoboCopy command will recall files from your StorSimple Azure storage to your local StorSimple and then move them over to the Windows Server target folder. Windows Server lo sincronizzerà con le condivisioni file di Azure. Man mano che il volume di Windows Server locale diventa pieno, la suddivisione in livelli cloud verrà avviata e i file di livello già sincronizzati correttamente. La suddivisione in livelli cloud genererà spazio sufficiente per continuare la copia dall'appliance virtuale StorSimple.Cloud tiering will generate enough space to continue the copy from the StorSimple virtual appliance. Controlli di suddivisione in livelli cloud una volta all'ora per vedere cosa è stato sincronizzato e per liberare spazio su disco per raggiungere lo spazio libero del volume del 99%.

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
      Consente di eseguire questo comando RoboCopy più volte, in sequenza sulla stessa destinazione / destinazione. Identifica ciò che è stato copiato prima e lo omette. Verranno elaborate solo le modifiche, le aggiunte e le "*eliminazioni*", che si sono verificate dall'ultima esecuzione. Se il comando non è stato eseguito prima, non viene omesso nulla. Questa è un'opzione eccellente per le ubicazioni di origine che sono ancora attivamente utilizzate e modificate.
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

Quando si esegue il comando RoboCopy per la prima volta, gli utenti e le applicazioni accedono ancora ai file e alle cartelle StorSimple e potenzialmente lo modificano. È possibile, che RoboCopy ha elaborato una directory, passa alla successiva e quindi un utente nel percorso di origine (StorSimple) aggiunge, modifica o elimina un file che non verrà elaborato in questa corsa RoboCopy corrente. Va bene così.

The first run is about moving the bulk of the data back to on-premises, over to your Windows Server and backup into the cloud via Azure File Sync. Questa operazione può richiedere molto tempo, a seconda di:This can take a long time, depending on:

* la larghezza di banda per il download
* la velocità di richiamo del servizio cloud StorSimple
* la larghezza di banda di caricamento
* numero di elementi (file e cartelle), che devono essere elaborati da entrambi i servizi

Al termine dell'esecuzione iniziale, eseguire nuovamente il comando.

La seconda volta terminerà più velocemente, perché deve solo trasportare le modifiche che si sono verificati dall'ultima esecuzione. Tali modifiche sono probabilmente locali al StorSimple già, perché sono recenti. Ciò riduce ulteriormente il tempo perché la necessità di richiamare dal cloud è ridotta. Durante questa seconda esecuzione, ancora, nuove modifiche possono accumularsi.

Ripetere questo processo fino a quando non si è certi che la quantità di tempo necessaria per il completamento è un tempo di inattività accettabile.

Quando si considera accettabile il tempo di inattività e si è pronti a portare la posizione StorSimple offline, quindi farlo ora: Ad esempio, rimuovere la condivisione SMB in modo che nessun utente possa accedere alla cartella o eseguire qualsiasi altro passaggio appropriato che impedisca la modifica del contenuto in questo sulla cartella StorSimple.

Eseguire un ultimo Round RoboCopy. Questo raccoglierà tutte le modifiche, che potrebbero essere state perse.
La durata di questo passaggio finale dipende dalla velocità della scansione RoboCopy. È possibile stimare il tempo (che è uguale al tempo di inattività) misurando la durata dell'esecuzione precedente.

Creare una condivisione nella cartella Windows Server ed eventualmente modificare la distribuzione DFS-N in modo che punti a essa. Assicurarsi di impostare le stesse autorizzazioni a livello di condivisione della condivisione SMB StorSimple.

La migrazione di una condivisione/gruppo di condivisioni in una radice o in un volume comune è stata completata. A seconda di ciò che è stato eseguito il mapping e ha deciso che era necessario passare alla stessa condivisione file di Azure.

È possibile provare a eseguire alcune di queste copie in parallelo. È consigliabile elaborare l'ambito di una condivisione file di Azure alla volta.

> [!WARNING]
> Dopo aver spostato tutti i dati da StorSimple a Windows Server e completata la migrazione: tornare a ***tutti i*** gruppi di sincronizzazione nel portale di Azure e regolare il valore percentuale di spazio libero del volume di livelli cloud su un valore più adatto per l'utilizzo della cache, ad esempio il 20%. 

I criteri di spazio libero del volume di livelli cloud agiscono a livello di volume con potenzialmente più endpoint server sincronizzati da esso. Se si dimentica di regolare lo spazio libero anche su un endpoint server, la sincronizzazione continuerà ad applicare la regola più restrittiva e tenterà di mantenere il 99% di spazio libero su disco, rendendo la cache locale non performante come previsto. A meno che l'obiettivo non abbia lo spazio dei nomi solo per un volume che contiene solo dati di archiviazione raramente accessibili.

## <a name="troubleshoot"></a>Risolvere i problemi

Il problema più probabile che si può incorrere in, è che il comando RoboCopy ha esito negativo con *"Volume full"* sul lato Windows Server. Se questo è il caso, allora la velocità di download è probabilmente migliore della velocità di caricamento. La suddivisione in livelli cloud agisce una volta ogni ora per evacuare il contenuto dal disco di Windows Server locale, sincronizzato.

Consentire lo stato di avanzamento della sincronizzazione e la suddivisione in livelli cloud per liberare spazio su disco. È possibile osservare che in Esplora File sul vostro Windows Server.

Quando Windows Server dispone di capacità disponibile sufficiente, rieseguire il comando risolverà il problema. Nulla si rompe quando si entra in questa situazione e si può andare avanti con fiducia. L'inconveniente di eseguire nuovamente il comando è l'unica conseguenza.

È anche possibile eseguire altri problemi di sincronizzazione file di Azure.You can also run into other Azure File Sync issues.
Per quanto improbabile possano essere, in questo caso, dare un'occhiata alla guida alla **risoluzione dei problemi**di LINK Azure File Sync .

## <a name="relevant-links"></a>Collegamenti pertinenti

Contenuto della migrazione:

* [Guida alla migrazione della serie StorSimple 8000](storage-files-migration-storsimple-8000.md)

Contenuto di Sincronizzazione file di Azure:Azure File Sync content:

* [Panoramica di AFS](https://aka.ms/AFS)
* [Guida alla distribuzione di AFS](storage-files-deployment-guide.md)
* [Risoluzione dei problemi AFS](storage-sync-files-troubleshoot.md)
