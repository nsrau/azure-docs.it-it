---
title: Protezione del ripristino dello stato del sistema e del ripristino bare metal
description: Usare server di Backup di Azure per eseguire il backup dello stato del sistema e offrire la protezione del ripristino bare metal (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021623"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Eseguire il backup dello stato del sistema e il ripristino in bare metal usando server di Backup di Azure

Il server di Backup di Azure esegue il backup dello stato del sistema e offre la protezione del ripristino bare metal.

* **Backup dello stato del sistema**: esegue il backup dei file del sistema operativo. Questo backup consente di eseguire il ripristino all'avvio di un computer, ma i file di sistema e il registro di sistema andranno persi. Un backup dello stato del sistema include gli elementi seguenti:
  * Membro del dominio: file di avvio, database di registrazione della classe COM+, Registro di sistema
  * Controller di dominio: file di avvio di Windows Server Active Directory (NTDS), database di registrazione della classe COM+, registro, volume di sistema (SYSVOL)
  * Computer che esegue servizi cluster: metadati del server di cluster
  * Computer che esegue servizi certificati: dati del certificato
* **Backup bare metal**: esegue il backup dei file del sistema operativo e di tutti i dati nei volumi critici, eccetto i dati utente. Per definizione, un backup bare metal include un backup dello stato del sistema. Fornisce protezione quando un computer non si avvia ed è necessario ripristinare tutto.

Nella tabella seguente sono riepilogati gli elementi di cui è possibile eseguire il backup e il ripristino. Per informazioni sulle versioni di app che lo stato del sistema e BMR possono proteggere, vedere [cosa server di backup di Azure Backup?](backup-mabs-protection-matrix.md).

|Backup|Problema|Ripristino dal backup del server di Backup di Azure|Ripristino dal backup dello stato del sistema|Ripristino bare metal|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dati di file**<br /><br />Backup dei dati normale<br /><br />BMR/Backup dello stato del sistema|Perdita dei dati dei file|S|N|N|
|**Dati di file**<br /><br />Backup del server di Backup di Azure dei dati di file<br /><br />BMR/Backup dello stato del sistema|Perdita o danneggiamento del sistema operativo|N|S|S|
|**Dati di file**<br /><br />Backup del server di Backup di Azure dei dati di file<br /><br />BMR/Backup dello stato del sistema|Server perso (volumi di dati intatti)|N|N|S|
|**Dati di file**<br /><br />Backup del server di Backup di Azure dei dati di file<br /><br />BMR/Backup dello stato del sistema|Perdita di un server (volumi dati persi)|S|N|S<br /><br />BMR, seguito da un ripristino regolare dei dati dei file di cui è stato eseguito il backup|
|**Dati di SharePoint**<br /><br />Backup del server di Backup di Azure dei dati della farm<br /><br />BMR/Backup dello stato del sistema|Sito, elenchi, elementi elenco, documenti persi|S|N|N|
|**Dati di SharePoint**<br /><br />Backup del server di Backup di Azure dei dati della farm<br /><br />BMR/Backup dello stato del sistema|Perdita o danneggiamento del sistema operativo|N|S|S|
|**Dati di SharePoint**<br /><br />Backup del server di Backup di Azure dei dati della farm<br /><br />BMR/Backup dello stato del sistema|Ripristino di emergenza|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Backup del server di Backup di Azure dell'host o guest Hyper-V<br /><br />BMR/Backup dello stato del sistema dell’host|Perdita di macchina virtuale|S|N|N|
|Hyper-V<br /><br />Backup del server di Backup di Azure dell'host o guest Hyper-V<br /><br />BMR/Backup dello stato del sistema dell’host|Perdita o danneggiamento del sistema operativo|N|S|S|
|Hyper-V<br /><br />Backup del server di Backup di Azure dell'host o guest Hyper-V<br /><br />BMR/Backup dello stato del sistema dell’host|Perdita di un host Hyper-V (macchine virtuali intatte)|N|N|S|
|Hyper-V<br /><br />Backup del server di Backup di Azure dell'host o guest Hyper-V<br /><br />BMR/Backup dello stato del sistema dell’host|Perdita di un host Hyper-V (macchine virtuali perse)|N|N|S<br /><br />Ripristino bare metal, seguito da ripristino regolare del server di Backup di Azure|
|SQL Server/Exchange<br /><br />Backup dell'app del server di Backup di Azure<br /><br />BMR/Backup dello stato del sistema|Perdita di dati delle app|S|N|N|
|SQL Server/Exchange<br /><br />Backup dell'app del server di Backup di Azure<br /><br />BMR/Backup dello stato del sistema|Perdita o danneggiamento del sistema operativo|N|S|S|
|SQL Server/Exchange<br /><br />Backup dell'app del server di Backup di Azure<br /><br />BMR/Backup dello stato del sistema|Perdita di un server (database e log delle transazioni intatti)|N|N|S|
|SQL Server/Exchange<br /><br />Backup dell'app del server di Backup di Azure<br /><br />BMR/Backup dello stato del sistema|Perdita di un server (database e log delle transazioni persi)|N|N|S<br /><br />Ripristino bare metal, seguito da ripristino regolare del server di Backup di Azure|

## <a name="how-system-state-backup-works"></a>Funzionamento del backup dello stato del sistema

Quando viene eseguito un backup dello stato del sistema, il server di Backup comunica con Windows Server Backup per richiedere un backup dello stato del sistema del server. Per impostazione predefinita, il server di Backup e Windows Server Backup utilizzano l'unità con più spazio libero. Le informazioni su questa unità vengono salvate nel file di *PSDataSourceConfig.xml* .

È possibile personalizzare l'unità utilizzata dal server di backup per il backup dello stato del sistema:

1. Nel server protetto, passare a *C:\Programmi\Microsoft Data Protection Manager\MABS\Datasources*.
1. Aprire il file di *PSDataSourceConfig.xml* per la modifica.
1. Modifica il valore \<FilesToProtect\> per la lettera di unità.
1. Salvare e chiudere il file.

Se un gruppo protezione dati è impostato in modo da proteggere lo stato del sistema del computer, eseguire una verifica di coerenza. Se viene generato un avviso, selezionare **modifica gruppo protezione** dati nell'avviso e quindi completare le pagine della procedura guidata. Esegui quindi un'altra verifica coerenza.

Se il server di protezione è in un cluster, è possibile che un'unità cluster venga selezionata come l'unità con maggiore spazio libero. Se la proprietà di tale unità viene spostata in un altro nodo e viene eseguito un backup dello stato del sistema, l'unità non sarà disponibile e il backup non riuscirà. In questo scenario, modificare *PSDataSourceConfig.xml* in modo che punti a un'unità locale.

Successivamente, Windows Server Backup crea una cartella denominata *WindowsImageBackup* nella radice della cartella Restore. Mentre Windows Server Backup crea il backup, tutti i dati vengono posizionati in questa cartella. Al termine del backup, il file viene trasferito nel computer del server di backup. Tenere presente quanto segue:

* Questa cartella e il relativo contenuto non vengono eliminati al termine del backup o del trasferimento. Il modo migliore per considerare questo problema è che lo spazio viene riservato per la successiva esecuzione di un backup.
* La cartella viene creata per ogni backup. L'indicatore di data e ora riflette l'ora dell'ultimo backup dello stato del sistema.

## <a name="how-bmr-backup-works"></a>Funzionamento del backup di BMR

Per il ripristino bare metal (che include un backup dello stato del sistema), il processo di backup viene salvato direttamente in una condivisione nel computer del server di Backup. Non viene salvato in una cartella nel server protetto.

Il server di backup chiama Windows Server Backup e condivide il volume di replica per il backup bare metal. In questo caso, non è necessario Windows Server Backup utilizzare l'unità con maggiore spazio libero. Utilizza invece la condivisione creata per il processo.

Al termine del backup, il file viene trasferito nel computer del server di backup. I log vengono archiviati in *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Prerequisiti e limiti

* Il ripristino bare metal non è supportato per i computer con Windows Server 2003 o che eseguono un sistema operativo client.

* Non è possibile proteggere BMR e lo stato del sistema per lo stesso computer in gruppi protezione dati diversi.

* Un computer server di Backup non può proteggere sé stesso per il ripristino bare metal.

* La protezione a breve termine su nastro (da disco a nastro o D2T) non è supportata per BMR. È supportata l'archiviazione a lungo termine su nastro (da disco a disco a nastro o D2D2T).

* Per la protezione del ripristino bare metal, Windows Server Backup deve essere installato nel computer protetto.

* Per la protezione BMR, diversamente dalla protezione dello stato del sistema, il server di backup non ha requisiti di spazio nel computer protetto. Windows Server Backup trasferisce direttamente i backup nel computer del server di Backup. Il processo di trasferimento del backup non viene visualizzato nella vista **Processi** del server di Backup.

* Il server di Backup riserva 30 GB di spazio nel volume di replica per il ripristino bare metal. È possibile modificare l'assegnazione di spazio nella pagina **allocazione dischi** della procedura guidata modifica gruppo protezione dati. In alternativa, è possibile usare il Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation cmdlet di PowerShell. Nel volume del punto di ripristino, la protezione del ripristino bare metal richiede circa 6 GB per una conservazione di cinque giorni.
  * Non è possibile ridurre le dimensioni del volume di replica a meno di 15 GB.
  * Il server di backup non calcola le dimensioni dell'origine dati del ripristino bare metal. Presume 30 GB per tutti i server. Modificare il valore in base alle dimensioni dei backup bare metal previsti nell'ambiente in uso. È possibile calcolare approssimativamente le dimensioni di un backup BMR come somma dello spazio utilizzato in tutti i volumi critici. Volumi critici = volume di avvio + volume di sistema + volume che ospita i dati dello stato del sistema, ad esempio Active Directory.

* Se si passa dalla protezione dello stato del sistema alla protezione BMR, la protezione BMR richiede meno spazio sul *volume del punto di ripristino*. Tuttavia, lo spazio aggiuntivo nel volume non viene recuperato. È possibile ridurre manualmente le dimensioni del volume nella pagina **modifica allocazione dischi** della procedura guidata modifica gruppo protezione dati. In alternativa, è possibile usare il Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation cmdlet di PowerShell.

    Se si passa dalla protezione dello stato del sistema alla protezione BMR, la protezione BMR richiede più spazio sul *volume di replica*. Il volume viene esteso automaticamente. Se si desidera modificare le allocazioni dello spazio predefinite, utilizzare il cmdlet Modify-DiskAllocation PowerShell.

* Se si passa dalla protezione BMR alla protezione dello stato del sistema, è necessario più spazio sul volume del punto di ripristino. È possibile che il server di Backup tenti di aumentare automaticamente il volume. Se lo spazio disponibile nel pool di archiviazione non è sufficiente, si verificherà un errore.

    Se si passa dalla protezione BMR alla protezione dello stato del sistema, è necessario disporre di spazio nel computer protetto. È necessario lo spazio perché la protezione stato del sistema scrive la replica nel computer locale e quindi trasferisce la replica nel computer del server di backup.

## <a name="before-you-begin"></a>Prima di iniziare

1. **Distribuire il server di Backup di Azure**. Verificare che il server di Backup sia distribuito correttamente. Per altre informazioni, vedere:
    * [Requisiti di sistema per il server di Backup di Azure](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matrice di protezione del server di Backup](backup-mabs-protection-matrix.md)

1. **Configurare l'archiviazione**. È possibile archiviare i dati di backup su disco, su nastro e nel cloud con Azure. Per altre informazioni, vedere [Preparare l'archiviazione dei dati](/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Configurare l'agente protezione**. Installare l'agente protezione nel computer di cui eseguire il backup. Per altre informazioni, vedere [Distribuire l'agente protezione DPM](/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Backup dello stato del sistema e bare metal

Per eseguire il backup dello stato del sistema e bare metal:

1. Per aprire la creazione guidata gruppo protezione dati crea, nel server di backup console di amministrazione selezionare azioni di **protezione**  >  **Actions**  >  **Crea gruppo protezione** dati.

1. Nella pagina **Selezione tipo di gruppo protezione dati** selezionare **Server**, quindi **Avanti**.

1. Nella pagina **Selezione membri del gruppo** espandere il computer, quindi selezionare **Ripristino bare metal** o **Stato del sistema**.

    Si noti che non è possibile proteggere il ripristino bare metal e lo stato del sistema per lo stesso computer in gruppi diversi. Inoltre, quando si seleziona il ripristino bare metal, lo stato del sistema viene abilitato automaticamente. Per altre informazioni, vedere [Distribuire gruppi di protezione](/system-center/dpm/create-dpm-protection-groups).

1. Nella pagina **Selezione metodo protezione dati** scegliere la modalità di gestione del backup a breve termine e del backup a lungo termine.

    Per prima cosa il backup a breve termine è sempre su disco, con l'opzione di eseguire il backup dal disco in Azure usando backup di Azure (a breve o a lungo termine). Un'alternativa al backup a lungo termine nel cloud consiste nella configurazione del backup a lungo termine su un dispositivo a nastro o una libreria di nastri autonoma connessa al server di Backup.

1. Nella pagina **Seleziona obiettivi Short-Term** scegliere come eseguire il backup nell'archiviazione a breve termine su disco:
    * In periodo di **mantenimento** dati scegliere per quanto tempo mantenere i dati su disco.
    * Per **frequenza di sincronizzazione** scegliere con quale frequenza eseguire un backup incrementale su disco. Se non si vuole impostare un intervallo di backup, è possibile selezionare **immediatamente prima di un punto di ripristino**. Il server di backup eseguirà un backup completo rapido appena prima di ogni punto di ripristino pianificato.

1. Se si desidera archiviare i dati su nastro per l'archiviazione a lungo termine, nella pagina **impostazione obiettivi Long-Term** scegliere per quanto tempo conservare i dati del nastro (da 1 a 99 anni).
    1. Per **frequenza di backup**, scegliere la frequenza di esecuzione del backup su nastro. La frequenza è basata sull'intervallo di mantenimento dati selezionato:
        * Quando il periodo di mantenimento dati è compreso tra 1 e 99 anni, è possibile eseguire il backup giornaliero, settimanale, bisettimanale, mensile, trimestrale, semestrale o annuale.
        * Quando il periodo di mantenimento dati è compreso tra 1 e 11 mesi, è possibile eseguire il backup giornaliero, settimanale, bisettimanale o mensile.
        * Quando il periodo di mantenimento dati è compreso tra 1 e 4 settimane, è possibile eseguire il backup giornaliero o settimanale.

    1. Nella pagina **Selezione dettagli libreria e nastro** selezionare il nastro e la libreria da utilizzare. Scegliere anche se i dati devono essere compressi e crittografati.

1. Nella pagina **Verifica allocazione dischi** verificare lo spazio su disco del pool di archiviazione disponibile per il gruppo protezione dati.

    * Il valore **Dimensioni totali dei dati** è la dimensione dei dati di cui eseguire il backup.
    * Il valore **Spazio su disco per il provisioning nel server di Backup di Azure** è lo spazio consigliato dal server di Backup per il gruppo protezione dati. Il server di backup usa queste impostazioni per scegliere il volume di backup ideale. È possibile modificare le opzioni del volume di backup nei **Dettagli di allocazione dei dischi**.
    * Per i carichi di lavoro, nel menu a discesa selezionare l'archiviazione preferita. Le modifiche cambiano i valori di **Totale spazio di archiviazione** e **Free Storage** (Spazio di archiviazione disponibile) nel riquadro **Spazio di archiviazione su disco disponibile**. Lo spazio sottoposto a provisioning è la quantità di spazio di archiviazione che il server di backup suggerisce di aggiungere al volume per garantire backup uniformi.

1. Nella pagina **scelta del metodo** per la creazione della replica selezionare la modalità di gestione della replica iniziale dei dati completi.

    Se si sceglie di eseguire la replica in rete, è consigliabile scegliere un orario di scarso traffico. Per grandi quantità di dati o condizioni di rete non ottimali, tenere in considerazione la replica dei dati offline mediante supporti rimovibili.

1. Nella pagina **Scegli opzioni di verifica coerenza** selezionare come automatizzare le verifiche della coerenza.

    È possibile scegliere di eseguire una verifica solo quando i dati di replica diventano incoerenti o in base a una pianificazione. Se non si vuole configurare la verifica coerenza automatica, è possibile eseguire una verifica manuale in qualsiasi momento. Per eseguire una verifica manuale, nell'area **Protezione** della console di amministrazione del server di Backup fare clic con il pulsante destro del mouse sul gruppo protezione dati e quindi selezionare **Esegui verifica coerenza**.

1. Se si sceglie di eseguire il backup nel cloud usando backup di Azure, nella pagina **specificare i dati per la protezione dati online** selezionare i carichi di lavoro di cui si vuole eseguire il backup in Azure.

1. Nella pagina **specificare la pianificazione dei backup online** selezionare la frequenza con cui eseguire il backup incrementale in Azure.

    È possibile pianificare l'esecuzione di backup ogni giorno, settimana, mese e anno. È anche possibile selezionare la data e l'ora in cui devono essere eseguiti i backup. I backup possono essere eseguiti fino a due volte al giorno. Ogni volta che viene eseguito un backup, viene creato un punto di ripristino dei dati in Azure dalla copia dei dati di backup archiviati nel disco del server di backup.

1. Nella pagina **specificare i criteri di conservazione online** selezionare la modalità di conservazione dei punti di ripristino creati dai backup giornalieri, settimanali, mensili e annuali in Azure.

1. Nella pagina **Scegliere la replica online** selezionare la modalità di esecuzione della replica completa iniziale dei dati.

    È possibile eseguire la replica in rete o eseguire il backup offline (seeding offline). Un backup offline usa la funzionalità di importazione di Azure. Per altre informazioni, vedere [Flusso di lavoro del backup offline in Backup di Azure](offline-backup-azure-data-box.md).

1. Nella pagina **Riepilogo** esaminare le impostazioni. Dopo aver selezionato **Crea gruppo**, viene eseguita la replica iniziale dei dati. Al termine della replica dei dati, nella pagina **stato** lo stato del gruppo protezione dati sarà **OK**. I backup vengono eseguiti in base alle impostazioni del gruppo protezione dati.

## <a name="recover-system-state-or-bmr"></a>Ripristinare lo stato del sistema o BMR

È possibile eseguire un ripristino bare metal o dello stato del sistema in un percorso di rete. Se è stato eseguito il backup di BMR, utilizzare ambiente ripristino Windows (WinRE) per avviare il sistema e connetterlo alla rete. Usare quindi Windows Server Backup dal percorso di rete. Se è stato eseguito il backup dello stato del sistema, è sufficiente usare Windows Server Backup per eseguire il ripristino dal percorso di rete.

### <a name="restore-bmr"></a>Eseguire un ripristino bare metal

Per eseguire il ripristino nel computer del server di backup:

1. Nel riquadro **ripristino** trovare il computer che si desidera ripristinare. Quindi selezionare **ripristino bare metal**.

1. I punti di ripristino disponibili sono indicati in grassetto nel calendario. Selezionare la data e l'ora del punto di ripristino da utilizzare.

1. Nella pagina  **Selezione tipo di ripristino** selezionare **copia in una cartella di rete**.

1. Nella pagina **specifica destinazione** selezionare la destinazione per i dati copiati.

    Tenere presente che la destinazione deve disporre di spazio sufficiente per i dati. Si consiglia di creare una nuova cartella per la destinazione.

1. Nella pagina **Specifica opzioni di ripristino** selezionare le impostazioni di sicurezza. Scegliere quindi se utilizzare snapshot hardware basati su SAN (Storage Area Network) per un ripristino più rapido. Questa opzione è disponibile solo se:
    * Si dispone di una rete SAN che fornisce questa funzionalità.
    * È possibile creare e suddividere un clone per renderlo scrivibile.
    * Il computer protetto e il computer del server di backup sono connessi alla stessa rete.

1. Configurare le opzioni di notifica.
1. Nella pagina **Conferma** selezionare **Ripristina**.

Per configurare il percorso di condivisione:

1. Nel percorso di ripristino passare alla cartella contenente il backup.

1. Condividere la cartella di un livello superiore a *WindowsImageBackup* in modo che la radice della cartella condivisa sia la cartella *WindowsImageBackup* .

    Se non si condivide questa cartella, Restore non troverà il backup. Per connettersi usando WinRE, è necessaria una condivisione a cui è possibile accedere in WinRE con l'indirizzo IP e le credenziali corretti.

Per ripristinare il sistema:

1. Avviare il computer in cui si vuole ripristinare l'immagine usando il DVD di Windows per il sistema che si sta ripristinando.

1. Nella prima pagina verificare le impostazioni relative alla lingua e alle impostazioni locali. Nella pagina **Installa** selezionare **Ripristina il computer**.

1. Nella pagina **Opzioni ripristino di sistema** selezionare **Ripristina il computer utilizzando un'immagine del sistema creata in precedenza**.

1. Nella pagina **selezionare un backup dell'immagine del sistema** selezionare **selezionare un'immagine**  >  del sistema ricerca **avanzata**  >  **per un'immagine del sistema in rete**. Se viene visualizzato un avviso, selezionare **Sì**. Passare al percorso della condivisione, immettere le credenziali e quindi selezionare il punto di ripristino. Il sistema esegue l'analisi dei backup specifici disponibili nel punto di ripristino. Selezionare il punto di ripristino da utilizzare.

1. Nella pagina **scegliere la modalità di ripristino del backup** selezionare **Formatta e partiziona i dischi**. Nella pagina successiva verificare le impostazioni.

1. Per iniziare il ripristino selezionare **Fine**. È necessario un riavvio.

### <a name="restore-system-state"></a>Ripristinare lo stato del sistema

Per eseguire il ripristino nel server di backup:

1. Nel riquadro **Ripristino** trovare il computer da ripristinare e quindi selezionare **Ripristino bare metal**.

1. I punti di ripristino disponibili sono indicati in grassetto nel calendario. Selezionare la data e l'ora del punto di ripristino da utilizzare.

1. Nella pagina **Selezione tipo di ripristino** selezionare **copia in una cartella di rete**.

1. Nella pagina **specifica destinazione** selezionare la posizione in cui copiare i dati.

    Tenere presente che la destinazione selezionata deve disporre di spazio sufficiente per i dati. Si consiglia di creare una nuova cartella per la destinazione.

1. Nella pagina **Specifica opzioni di ripristino** selezionare le impostazioni di sicurezza. Quindi scegliere se usare snapshot dell'hardware basati su SAN, per un ripristino più rapido. Questa opzione è disponibile solo se:
    * Si dispone di una rete SAN che fornisce questa funzionalità.
    * È possibile creare e suddividere un clone per renderlo scrivibile.
    * Il computer protetto e il server di backup sono connessi alla stessa rete.

1. Configurare le opzioni di notifica.
1. Nella pagina **Conferma** selezionare **Ripristina**.

Per eseguire Windows Server Backup:

1. Selezionare **azioni**  >  **Ripristina**  >  **il server**  >  **successivo**.

1. Selezionare **Un altro server**, selezionare la pagina **Impostazione tipo di percorso** e quindi selezionare **Cartella condivisa remota**. Immettere il percorso della cartella che contiene il punto di ripristino.

1. Nella pagina **Selezione tipo di ripristino** selezionare **Stato del sistema**.

1. Nella pagina **Selezione percorso per il ripristino dello stato del sistema** selezionare **Percorso originale**.

1. Nella pagina **Conferma** selezionare **Ripristina**.

1. Dopo il ripristino, riavviare il server.

È anche possibile eseguire il ripristino dello stato del sistema da un prompt dei comandi:

1. Avviare Windows Server Backup nel computer che si desidera ripristinare.

1. Per ottenere l'identificatore di versione, a un prompt dei comandi immettere: 

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Utilizzare l'identificatore di versione per avviare il ripristino dello stato del sistema. Al prompt dei comandi immettere:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Confermare che si desidera avviare il ripristino. È possibile visualizzare il processo nella finestra del prompt dei comandi. Viene creato un log di ripristino.

1. Dopo il ripristino, riavviare il server.
