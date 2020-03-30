---
title: Migrazione NAS locale in Sincronizzazione file di AzureOn-premises NAS migration to Azure File Sync
description: Informazioni su come eseguire la migrazione di file da un percorso NAS (Network Attached Storage) locale a una distribuzione cloud ibrida con Sincronizzazione file di Azure e condivisioni file di Azure.Learn how to migrate files from an on-premises Network Attached Storage (NAS) location to a hybrid cloud deployment with Azure File Sync and Azure file shares.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247351"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Eseguire la migrazione da NAS (Network Attached Storage) a una distribuzione cloud ibrida con Sincronizzazione file di AzureMigrate from Network Attached Storage (NAS) to a hybrid cloud deployment with Azure File Sync

Sincronizzazione file di Azure funziona su percorsi DAS (Direct Attached Storage) e non supporta la sincronizzazione con i percorsi NAS (Network Attached Storage).
Questo fatto rende necessaria una migrazione dei file e questo articolo guida l'utente attraverso la pianificazione e l'esecuzione di tale migrazione.

## <a name="migration-goals"></a>Obiettivi della migrazione

L'obiettivo è spostare le condivisioni disponibili nell'appliance NAS in un server Windows Server. Usare quindi Sincronizzazione file di Azure per una distribuzione cloud ibrida. Questa migrazione deve essere eseguita in modo da garantire l'integrità dei dati di produzione e la disponibilità durante la migrazione. Quest'ultimo richiede di mantenere i tempi di inattività al minimo, in modo che possa adattarsi o superare solo leggermente le finestre di manutenzione regolari.

## <a name="migration-overview"></a>Panoramica della migrazione

Come indicato [nell'articolo Panoramica](storage-files-migration-overview.md)della migrazione di File di Azure, è importante usare lo strumento e l'approccio di copia corretti. L'appliance NAS espone condivisioni SMB direttamente sulla rete locale. RoboCopy, incorporato in Windows Server, è il modo migliore per spostare i file in questo scenario di migrazione.

- Fase 1: Identificare il numero di condivisioni file di [Azure necessariePhase](#phase-1-identify-how-many-azure-file-shares-you-need) 1: Identify how many Azure file shares you need
- Fase 2: [Effettuare il provisioning di un Windows Server locale adattoPhase 2: Provision a suitable Windows Server on-premises](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fase 3: [Distribuire la risorsa cloud di Sincronizzazione file](#phase-3-deploy-the-azure-file-sync-cloud-resource) di AzurePhase 3: Deploy the Azure File Sync cloud resource
- Fase 4: Distribuire le risorse di archiviazione di [AzurePhase](#phase-4-deploy-azure-storage-resources) 4: Deploy Azure storage resources
- Fase 5: [Distribuire l'agente](#phase-5-deploy-the-azure-file-sync-agent) di Sincronizzazione file di AzurePhase 5: Deploy the Azure File Sync agent
- Fase 6: [Configurare Sincronizzazione file](#phase-6-configure-azure-file-sync-on-the-windows-server) di Azure in Windows ServerPhase 6: Configure Azure File Sync on the Windows Server
- Fase 7: [RoboCopyPhase](#phase-7-robocopy) 7: RoboCopy
- Fase 8: [taglio da parte dell'utente](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: Identificare il numero di condivisioni file di Azure necessariePhase 1: Identify how many Azure file shares you need

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fase 2: Effettuare il provisioning di un Windows Server locale adattoPhase 2: Provision a suitable Windows Server on-premises

* Creare un Windows Server 2019 - almeno 2012R2 - come macchina virtuale o server fisico. È supportato anche un cluster di failover di Windows Server.A Windows Server fail-over cluster is also supported.
* Effettuare il provisioning o aggiungere DAS (Direct Attached Storage) rispetto al NAS, che non è supportato.

    La quantità di spazio di archiviazione di cui si esegue il provisioning può essere inferiore a quella attualmente in uso nell'appliance NAS, se si usa la funzionalità di suddivisione in livelli cloud di Sincronizzazione file di Azure.The amount of storage you provision can be smaller than quello you are currently using on nas appliance, if you use Azure File Syncs [cloud tiering](storage-sync-cloud-tiering.md) feature.
    Tuttavia, quando si copiano i file dallo spazio NAS più grande al volume di Windows Server più piccolo in una fase successiva, sarà necessario lavorare in batch:

    1. Spostare un set di file che si adatta al disco
    2. lasciare che la sincronizzazione dei file e il tiering cloud si impegnino
    3. quando viene creato più spazio libero sul volume, procedere con il batch di file successivo. 
    
    È possibile evitare questo approccio di invio in batch eseguendo il provisioning dello spazio equivalente in Windows Server occupato dai file nell'appliance NAS. Prendere in considerazione la deduplicazione su NAS / Windows. Se non si vuole eseguire in modo permanente il commit di questa quantità elevata di spazio di archiviazione in Windows Server, è possibile ridurre le dimensioni del volume dopo la migrazione e prima di modificare i criteri di suddivisione in livelli cloud. In questo modo viene creata una cache locale più piccola delle condivisioni file di Azure.That creates a smaller on-premises cache of your Azure file shares.

La configurazione delle risorse (calcolo e RAM) di Windows Server distribuita dipende principalmente dal numero di elementi (file e cartelle) che verranno sincronizzati. Si consiglia di utilizzare una configurazione con prestazioni più elevate in caso di problemi.

[Informazioni su come ridimensionare un Windows Server in base al numero di elementi (file e cartelle) da sincronizzare.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> L'articolo collegato in precedenza presenta una tabella con un intervallo per la memoria del server (RAM). È possibile orientarsi verso il numero più piccolo per il server, ma si prevede che la sincronizzazione iniziale può richiedere molto più tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: Distribuire la risorsa cloud di Sincronizzazione file di AzurePhase 3: Deploy the Azure File Sync cloud resource

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Distribuire le risorse di archiviazione di AzurePhase 4: Deploy Azure storage resources

In questa fase, consultare la tabella di mapping della fase 1 e usarla per eseguire il provisioning del numero corretto di account di archiviazione e condivisioni file di Azure al loro interno.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: Distribuire l'agente di Sincronizzazione file di AzurePhase 5: Deploy the Azure File Sync agent

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fase 6: Configurare Sincronizzazione file di Azure in Windows ServerPhase 6: Configure Azure File Sync on the Windows Server

Per questo processo, il server Windows Server locale registrato deve essere pronto e connesso a Internet.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Il tiering cloud è la funzionalità AFS che consente al server locale di disporre di una capacità di archiviazione inferiore a quella archiviata nel cloud, ma dello spazio dei nomi completo è disponibile. Anche i dati interessanti localmente vengono memorizzati nella cache locale per ottenere prestazioni di accesso rapido. La suddivisione in livelli cloud è una funzionalità facoltativa per ogni "endpoint server" di Sincronizzazione file di Azure.Cloud tiering is an optional feature per Azure File Sync "server endpoint".

> [!WARNING]
> Se è stato eseguito il provisioning di una risorsa di archiviazione nei volumi server Windows rispetto ai dati utilizzati nell'appliance NAS, la suddivisione in livelli cloud è obbligatoria. Se non si attiva la suddivisione in livelli cloud, il server non libererà spazio per archiviare tutti i file. Impostare i criteri di suddivisione in livelli, temporaneamente per la migrazione, al 99% di spazio libero del volume. Assicurarsi di tornare alle impostazioni di suddivisione in livelli cloud al termine della migrazione e impostarlo su un livello utile più a lungo termine.

Ripetere i passaggi per la creazione e l'aggiunta del gruppo di sincronizzazione come endpoint server per tutte le condivisioni file/percorsi del server di Azure che devono essere configurate per la sincronizzazione.

Dopo la creazione di tutti gli endpoint server, la sincronizzazione funziona. È possibile creare un file di test e visualizzarlo sincronizzare dal percorso del server alla condivisione file di Azure connessa (come descritto dall'endpoint cloud nel gruppo di sincronizzazione).

Entrambi i percorsi, le cartelle del server e le condivisioni file di Azure sono altrimenti vuoti e in attesa di dati in entrambe le posizioni. Nel passaggio successivo si inizierà a copiare i file in Windows Server per Sincronizzazione file di Azure per spostarli nel cloud. Nel caso in cui sia stata abilitata la suddivisione in livelli cloud, il server inizierà a livellire i file, in caso di esaurimento della capacità nei volumi locali.

## <a name="phase-7-robocopy"></a>Fase 7: RoboCopyPhase 7: RoboCopy

L'approccio di migrazione di base è RoboCopy dall'appliance NAS a Windows Server e Sincronizzazione file di Azure con le condivisioni file di Azure.The basic migration approach is a RoboCopy from your NAS appliance to your Windows Server, and Azure File Sync to Azure file shares.

Eseguire la prima copia locale nella cartella di destinazione di Windows Server:

* Identificare la prima posizione sull'appliance NAS.
* Identificare la cartella corrispondente in Windows Server, in cui è già configurata La sincronizzazione file di Azure.Identify the matching folder on the Windows Server, that already have Azure File Sync configured on it.
* Avviare la copia utilizzando RoboCopy

Il comando RoboCopy seguente copierà i file dall'archivio NAS alla cartella di destinazione di Windows Server. Windows Server lo sincronizzerà con le condivisioni file di Azure. 

Se è stato eseguito il provisioning di una risorsa di archiviazione in Windows Server inferiore a quella dell'installazione dei file nell'appliance NAS, è stato configurato il tiering cloud. Man mano che il volume di Windows Server locale diventa pieno, [la suddivisione](storage-sync-cloud-tiering.md) in livelli cloud verrà avviata e i file di livello già sincronizzati correttamente. La suddivisione in livelli cloud genererà spazio sufficiente per continuare la copia dall'appliance NAS. Controlli di suddivisione in livelli cloud una volta all'ora per vedere cosa è stato sincronizzato e per liberare spazio su disco per raggiungere lo spazio libero del volume del 99%.
È possibile che RoboCopy sposti i file più velocemente di quanto sia possibile eseguire la sincronizzazione con il cloud e il livello locale, esaurendo così lo spazio su disco locale. RoboCopy avrà esito negativo. Si consiglia di utilizzare le condivisioni in una sequenza che lo impedisce. Ad esempio, non avviare i processi RoboCopy per tutte le condivisioni contemporaneamente o spostare solo le condivisioni che rientrano nella quantità corrente di spazio libero in Windows Server, per citarne alcune.

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
      /UNILOG:\<nome file\>
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
      Consente di eseguire questo comando RoboCopy più volte, in sequenza sulla stessa destinazione / destinazione. Identifica ciò che è stato copiato prima e lo omette. Verranno elaborate solo le modifiche, le aggiunte e le "*eliminazioni*", che si sono verificate dall'ultima esecuzione. Se il comando non è stato eseguito prima, non viene omesso nulla. Il flag */MIR* è un'opzione eccellente per i percorsi di origine che sono ancora utilizzati attivamente e modificati.
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

## <a name="phase-8-user-cut-over"></a>Fase 8: taglio da parte dell'utente

Quando si esegue il comando RoboCopy per la prima volta, gli utenti e le applicazioni accedono ancora ai file sul NAS e potenzialmente li modificano. È possibile, che RoboCopy ha elaborato una directory, passa alla successiva e quindi un utente sul percorso di origine (NAS) aggiunge, modifica o elimina un file che non verrà elaborato in questa corsa RoboCopy corrente. Si tratta di un comportamento previsto.

The first run is about moving the bulk of the data to your Windows Server and into the cloud via Azure File Sync. Questa prima copia può richiedere molto tempo, a seconda di:This first copy can take a long time, depending on:

* la larghezza di banda per il download
* la larghezza di banda di caricamento
* la velocità della rete locale e il numero di come il numero di thread RoboCopy corrisponde
* il numero di elementi (file e cartelle), che devono essere elaborati da RoboCopy e Azure File Sync

Al termine dell'esecuzione iniziale, eseguire nuovamente il comando.

La seconda volta terminerà più velocemente, perché deve solo trasportare le modifiche che si sono verificati dall'ultima esecuzione. Durante questa seconda esecuzione, ancora, nuove modifiche possono accumularsi.

Ripetere questo processo finché non si è certi che la quantità di tempo necessaria per completare una RoboCopy per un percorso specifico si trovi all'interno di una finestra accettabile per i tempi di inattività.

Quando si considera accettabile il tempo di inattività e si è pronti a disconnettere la posizione NAS: per portare offline l'accesso utente, è possibile modificare gli ACL nella radice della condivisione in modo che gli utenti non possano più accedere alla posizione o eseguire qualsiasi altro passaggio appropriato che impedisce la modifica del contenuto in questa cartella sul NAS.

Eseguire un ultimo Round RoboCopy. Prenderà tutte le modifiche, che potrebbero essere state perse.
La durata di questo passaggio finale dipende dalla velocità della scansione RoboCopy. È possibile stimare il tempo (che è uguale al tempo di inattività) misurando la durata dell'esecuzione precedente.

Creare una condivisione nella cartella Windows Server ed eventualmente modificare la distribuzione DFS-N in modo che punti a essa. Assicurarsi di impostare le stesse autorizzazioni a livello di condivisione della condivisione SMB NAS. Se si dispone di un NAS aggiunto a un dominio di classe enterprise, i SID utente corrisponderanno automaticamente in quanto gli utenti esistono in Active Directory e RoboCopy copia i file e i metadati con la massima fedeltà. Se sono stati utilizzati utenti locali sul NAS, è necessario ricreare questi utenti come utenti locali di Windows Server ed eseguire il mapping dei SID esistenti, RoboCopy spostato a Windows Server ai SID dei nuovi utenti locali di Windows Server.

La migrazione di una condivisione/gruppo di condivisioni in una radice o in un volume comune è stata completata. (A seconda del mapping dalla fase 1)

È possibile provare a eseguire alcune di queste copie in parallelo. È consigliabile elaborare l'ambito di una condivisione file di Azure alla volta.

> [!WARNING]
> Dopo aver spostato tutti i dati dal NAS a Windows Server e completata la migrazione: tornare a ***tutti i*** gruppi di sincronizzazione nel portale di Azure e regolare il valore percentuale di spazio libero del volume cloud su un valore più adatto per l'utilizzo della cache, ad esempio il 20%. 

I criteri di spazio libero del volume di livelli cloud agiscono a livello di volume con potenzialmente più endpoint server sincronizzati da esso. Se si dimentica di regolare lo spazio libero anche su un endpoint server, la sincronizzazione continuerà ad applicare la regola più restrittiva e tenterà di mantenere il 99% di spazio libero su disco, rendendo la cache locale non performante come previsto. A meno che l'obiettivo non abbia lo spazio dei nomi solo per un volume che contiene solo dati di archiviazione raramente accessibili e si riserva il resto dello spazio di archiviazione per un altro scenario.

## <a name="troubleshoot"></a>Risolvere i problemi

Il problema più probabile che si può incorrere in, è che il comando RoboCopy ha esito negativo con *"Volume full"* sul lato Windows Server. La suddivisione in livelli cloud agisce una volta ogni ora per evacuare il contenuto dal disco di Windows Server locale, sincronizzato. Il suo obiettivo è quello di raggiungere il 99% di spazio libero sul volume.

Consentire lo stato di avanzamento della sincronizzazione e la suddivisione in livelli cloud per liberare spazio su disco. È possibile osservare che in Esplora File sul vostro Windows Server.

Quando Windows Server dispone di capacità disponibile sufficiente, rieseguire il comando risolverà il problema. Nulla si rompe quando si entra in questa situazione e si può andare avanti con fiducia. L'inconveniente di eseguire nuovamente il comando è l'unica conseguenza.

Controllare il collegamento nella sezione seguente per la risoluzione dei problemi di sincronizzazione file di Azure.Check the link in the following section for troubleshooting Azure File Sync issues.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altri altri informazioni sulle condivisioni file di Azure e su Sincronizzazione file di Azure.There is more to discover about Azure file shares and Azure File Sync. Gli articoli seguenti consentono di comprendere le opzioni avanzate, le procedure consigliate e contengono anche informazioni sulla risoluzione dei problemi. Questi articoli si collegano alla documentazione relativa alla [condivisione file](storage-files-introduction.md) di Azure in base alle esigenze.

* [Panoramica di AFS](https://aka.ms/AFS)
* [Guida alla distribuzione di AFS](storage-files-deployment-guide.md)
* [Risoluzione dei problemi AFS](storage-sync-files-troubleshoot.md)
