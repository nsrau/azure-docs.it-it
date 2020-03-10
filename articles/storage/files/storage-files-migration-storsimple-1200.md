---
title: StorSimple 1200 migrazione a Sincronizzazione file di Azure
description: Informazioni su come eseguire la migrazione di un'appliance virtuale StorSimple serie 1200 a Sincronizzazione file di Azure.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 6863e7f8ef8e2f263cda824fd13186dc7b035454
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943604"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 migrazione a Sincronizzazione file di Azure

StorSimple serie 1200 è un'appliance virtuale eseguita in un data center locale. È possibile eseguire la migrazione dei dati da questo Appliance a un ambiente Sincronizzazione file di Azure. Sincronizzazione file di Azure è il servizio di Azure a lungo termine predefinito e strategico a cui è possibile eseguire la migrazione delle appliance StorSimple.

La serie StorSimple 1200 raggiungerà la [fine del ciclo di vita](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) nel 2022 dicembre.  È importante iniziare a pianificare la migrazione appena possibile. Questo articolo fornisce i passaggi necessari per la Knowledge base e le migrazioni per una migrazione corretta a Sincronizzazione file di Azure. 

## <a name="azure-file-sync"></a>Sincronizzazione file di Azure

> [!IMPORTANT]
> Microsoft si impegna a supportare i clienti nella migrazione. Inviare tramite posta elettronica AzureFilesMigration@microsoft. com per un piano di migrazione personalizzato, nonché per assistenza durante la migrazione.

Sincronizzazione file di Azure è un servizio cloud Microsoft basato su due componenti principali:

* Sincronizzazione di file e suddivisione in livelli nel cloud.
* Condivisioni file come archiviazione nativa in Azure a cui è possibile accedere tramite più protocolli, ad esempio SMB e REST di file. Una condivisione file di Azure è paragonabile a una condivisione file in un server Windows, che può essere montata in modo nativo come unità di rete. Supporta importanti aspetti di fedeltà dei file, ad esempio attributi, autorizzazioni e timestamp. A differenza di StorSimple, non è necessaria alcuna applicazione/servizio per interpretare i file e le cartelle archiviati nel cloud. L'approccio ideale e più flessibile per archiviare i dati file server per utilizzo generico, oltre ad alcuni dati dell'applicazione, nel cloud.

Questo articolo è incentrato sulla procedura di migrazione. Se prima della migrazione si desidera ottenere altre informazioni su Sincronizzazione file di Azure, è consigliabile usare gli articoli seguenti:

* [Sincronizzazione file di Azure-Panoramica](https://aka.ms/AFS "Panoramica")
* [Guida alla distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Obiettivi della migrazione

L'obiettivo è garantire l'integrità dei dati di produzione e garantire la disponibilità. Il secondo richiede il mantenimento del tempo di inattività minimo, in modo che possa rientrare o solo leggermente più di una normale finestra di manutenzione.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200-percorso di migrazione per Sincronizzazione file di Azure

Per eseguire un agente di Sincronizzazione file di Azure, è necessario un server Windows locale. Windows Server può essere almeno un server 2012R2, ma idealmente è Windows Server 2019.

Sono disponibili numerosi percorsi di migrazione alternativi, che creerebbe troppo tempo per documentare tutti gli articoli e per illustrare il motivo per cui i rischi o gli svantaggi sul percorso si consigliano come procedura consigliata in questo articolo.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Panoramica della route di migrazione dei passaggi più avanti in questo articolo.":::

L'immagine precedente illustra i passaggi che corrispondono alle sezioni di questo articolo.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Passaggio 1: effettuare il provisioning di Windows Server e dell'archiviazione locali

1. Creare una macchina virtuale di Windows Server 2019, almeno 2012R2, come macchina virtuale o server fisico. È supportato anche un cluster di failover di Windows Server.
2. Effettuare il provisioning o aggiungere l'archiviazione collegata direttamente (DAS rispetto a NAS, che non è supportata). Le dimensioni dell'archiviazione di Windows Server devono essere maggiori o uguali a quelle della capacità disponibile dell'appliance StorSimple 1200 virtuale.

### <a name="step-2-configure-your-windows-server-storage"></a>Passaggio 2: configurare l'archiviazione di Windows Server

In questo passaggio viene mappata la struttura di archiviazione di StorSimple (volumi e condivisioni) alla struttura di archiviazione di Windows Server.
Se si prevede di apportare modifiche alla struttura di archiviazione, vale a dire il numero di volumi, l'associazione di cartelle di dati ai volumi o la struttura di sottocartelle sopra o sotto le condivisioni SMB/NFS correnti, ora è il momento di prendere in considerazione queste modifiche.
La modifica della struttura di file e cartelle dopo la configurazione di Sincronizzazione file di Azure, è complessa e dovrebbe essere evitata.
Questo articolo presuppone che si stia eseguendo il mapping di 1:1, pertanto è necessario tenere in considerazione le modifiche al mapping quando si seguono i passaggi descritti in questo articolo.

* Nessuno dei dati di produzione dovrebbe finire nel volume di sistema di Windows Server. La suddivisione in livelli cloud non è supportata nei volumi di sistema. Questa funzionalità è tuttavia necessaria per la migrazione e per le operazioni continue come sostituzione StorSimple.
* Eseguire il provisioning dello stesso numero di volumi nel server Windows come nel dispositivo virtuale StorSimple 1200.
* Configurare tutti i ruoli, le funzionalità e le impostazioni di Windows Server necessari. È consigliabile optare per gli aggiornamenti di Windows Server per rendere il sistema operativo sicuro e aggiornato. Analogamente, è consigliabile optare per Microsoft Update per rendere aggiornate le applicazioni Microsoft, incluso l'agente di Sincronizzazione file di Azure.
* Non configurare cartelle o condivisioni prima di leggere i passaggi seguenti.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Passaggio 3: distribuire la prima risorsa cloud Sincronizzazione file di Azure

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Passaggio 4: associare la struttura del volume e della cartella locale a Sincronizzazione file di Azure e risorse di condivisione file di Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Passaggio 5: provisioning di condivisioni file di Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Passaggio 6: configurare le cartelle di destinazione di Windows Server

Nei passaggi precedenti sono stati presi in considerazione tutti gli aspetti che determineranno i componenti delle topologie di sincronizzazione. A questo punto, è possibile preparare il server per la ricezione di file per il caricamento.

Creare **tutte le** cartelle che sincronizzano ognuna nella propria condivisione file di Azure.
È importante seguire la struttura di cartelle documentata in precedenza. Se, ad esempio, si è deciso di sincronizzare più condivisioni SMB locali in un'unica condivisione file di Azure, è necessario inserirle in una cartella radice comune del volume. Creare ora la cartella radice di destinazione nel volume.

Il numero di condivisioni file di Azure di cui è stato effettuato il provisioning deve corrispondere al numero di cartelle create in questo passaggio e al numero di volumi che verranno sincronizzati a livello di radice.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Passaggio 7: distribuire l'agente di Sincronizzazione file di Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Passaggio 8: configurare la sincronizzazione

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Assicurarsi di attivare la suddivisione in livelli nel cloud.** Questa operazione è necessaria se il server locale non dispone di spazio sufficiente per archiviare le dimensioni totali dei dati nella risorsa di archiviazione cloud di StorSimple. Impostare i criteri di suddivisione in livelli, temporaneamente per la migrazione, sullo spazio disponibile nel volume del 99%.

Ripetere i passaggi per la creazione del gruppo di sincronizzazione e l'aggiunta della cartella del server corrispondente come endpoint server per tutte le condivisioni file o i percorsi server di Azure che devono essere configurati per la sincronizzazione.

### <a name="step-9-copy-your-files"></a>Passaggio 9: copiare i file

L'approccio di migrazione di base è RoboCopy dall'appliance virtuale StorSimple al server Windows e Sincronizzazione file di Azure alle condivisioni file di Azure.

Eseguire la prima copia locale nella cartella di destinazione di Windows Server:

* Identificare la prima posizione nell'appliance StorSimple virtuale.
* Identificare la cartella corrispondente nel server Windows in cui è già stata configurata Sincronizzazione file di Azure.
* Avviare la copia con RoboCopy

Il comando RoboCopy seguente richiamerà i file dall'archiviazione di Azure StorSimple alla StorSimple locale e li sposterà nella cartella di destinazione di Windows Server. Windows Server lo sincronizza con le condivisioni file di Azure. Con l'esaurimento del volume locale di Windows Server, la suddivisione in livelli cloud verrà avviata e i file di livello che sono già stati sincronizzati correttamente. La suddivisione in livelli cloud genera spazio sufficiente per continuare la copia dal dispositivo virtuale StorSimple. La suddivisione in livelli nel cloud viene controllata una volta all'ora per vedere cosa è stato sincronizzato e liberare spazio su disco per raggiungere lo spazio disponibile nel volume del 99%.

```console
Robocopy /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Sfondo:

:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Consente a di eseguire questo comando RoboCopy più volte, in sequenza nella stessa destinazione/destinazione. Identifica gli elementi che sono stati copiati in precedenza e li omette. Verranno elaborate solo le modifiche, le aggiunte e le "*eliminazioni*", che si sono verificate dall'ultima esecuzione. Se il comando non è stato eseguito in precedenza, non viene omesso nulla. Si tratta di un'ottima opzione per le posizioni di origine che vengono ancora utilizzate e modificate attivamente.
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

Quando si esegue il comando RoboCopy per la prima volta, gli utenti e le applicazioni continuano ad accedere ai file e alle cartelle StorSimple e potenzialmente modificarli. È possibile che RoboCopy abbia elaborato una directory, passi alla successiva, quindi un utente nel percorso di origine (StorSimple) aggiunge, modifica o Elimina un file che ora non verrà elaborato in questa esecuzione di RoboCopy corrente. Questo è un problema.

Il primo passaggio consiste nel riportare la maggior parte dei dati in locale al server Windows ed eseguire il backup nel cloud tramite Sincronizzazione file di Azure. Questa operazione può richiedere molto tempo, a seconda di:

* larghezza di banda di download
* velocità di richiamo del servizio cloud StorSimple
* larghezza di banda di caricamento
* il numero di elementi (file e cartelle) che devono essere elaborati da uno dei due servizi

Una volta completata l'esecuzione iniziale, eseguire di nuovo il comando.

La seconda volta che verrà completata più velocemente, perché è necessario solo trasferire le modifiche apportate dall'ultima esecuzione. Queste modifiche sono probabilmente locali per la StorSimple, perché sono già recenti. Questo riduce ulteriormente il tempo perché la necessità di richiamare il cloud è ridotta. Durante la seconda esecuzione, tuttavia, le nuove modifiche possono accumularsi.

Ripetere questo processo fino a quando non si è soddisfatti che la quantità di tempo necessaria per il completamento è un tempo di inattività accettabile.

Quando si considera il tempo di inattività accettabile e si è pronti a portare offline il percorso StorSimple, procedere ora: ad esempio, rimuovere la condivisione SMB in modo che nessun utente possa accedere alla cartella o eseguire qualsiasi altro passaggio appropriato che impedisce la modifica del contenuto in questa cartella in StorSimple.

Eseguire un ultimo round RoboCopy. Questa operazione rileverà tutte le modifiche che potrebbero essere state perse.
Il tempo necessario per il passaggio finale dipende dalla velocità dell'analisi RoboCopy. È possibile stimare l'intervallo di tempo (uguale al tempo di inattività) misurando il tempo impiegato per l'esecuzione precedente.

Creare una condivisione nella cartella di Windows Server ed eventualmente modificare la distribuzione di DFS-N in modo che punti a essa. Assicurarsi di impostare le stesse autorizzazioni a livello di condivisione della condivisione SMB StorSimple.

È stata completata la migrazione di una condivisione o di un gruppo di condivisioni in una radice o un volume comune. (A seconda di cosa è stato eseguito il mapping e si è deciso di accedere alla stessa condivisione file di Azure).

È possibile provare a eseguire alcune di queste copie in parallelo. Si consiglia di elaborare l'ambito di una condivisione file di Azure alla volta.

> [!WARNING]
> Dopo aver spostato tutti i dati da StorSimple a Windows Server e aver completato la migrazione: tornare a ***tutti*** i gruppi di sincronizzazione nell'portale di Azure e modificare il valore percentuale spazio disponibile del volume del cloud in modo che sia più adatto per l'utilizzo della cache, ad indicare il 20%. 

I criteri di spazio libero del volume di suddivisione in livelli nel cloud agiscono a livello di volume con potenzialmente più endpoint server sincronizzati. Se si dimentica di regolare lo spazio libero su un solo endpoint server, la sincronizzazione continuerà ad applicare la regola più restrittiva e tenterà di mantenere il 99% di spazio libero su disco, rendendo la cache locale non funziona come previsto. A meno che non si tratti dell'obiettivo di avere solo lo spazio dei nomi per un volume che contiene solo i dati di archiviazione a cui si accede raramente.

## <a name="troubleshoot"></a>Risolvere problemi

Il problema più probabile in cui è possibile eseguire è che il comando RoboCopy ha esito negativo con *"volume full"* sul lato server di Windows. In tal caso, la velocità di download è probabilmente migliore rispetto alla velocità di caricamento. La suddivisione in livelli nel cloud funziona una volta ogni ora per l'evacuazione del contenuto dal disco locale di Windows Server, sincronizzato.

Consentire la sincronizzazione dello stato di avanzamento e la suddivisione in livelli nel cloud liberare spazio su disco. È possibile osservare che in Esplora file in Windows Server.

Quando Windows Server dispone di sufficiente capacità disponibile, la riesecuzione del comando risolverà il problema. Niente si interrompe quando si entra in questa situazione ed è possibile procedere con fiducia. L'unica conseguenza è l'inconveniente di eseguire di nuovo il comando.

È anche possibile eseguire altri Sincronizzazione file di Azure problemi.
Come è improbabile, in tal caso, esaminare il **collegamento sincronizzazione file di Azure Guida alla risoluzione dei problemi**.

## <a name="relevant-links"></a>Collegamenti pertinenti

Contenuto della migrazione:

* [Guida alla migrazione di StorSimple serie 8000](storage-files-migration-storsimple-8000.md)

Contenuto Sincronizzazione file di Azure:

* [Panoramica di AFS](https://aka.ms/AFS)
* [Guida alla distribuzione di AFS](storage-files-deployment-guide.md)
* [Risoluzione dei problemi di AFS](storage-sync-files-troubleshoot.md)
