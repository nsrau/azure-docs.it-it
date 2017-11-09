---
title: Il server di Backup di Azure protegge lo stato del sistema ed esegue il ripristino bare metal | Microsoft Docs
description: Utilizzare il server di Backup di Azure per eseguire il backup dello stato del sistema e fornire la protezione del ripristino bare metal.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
keywords: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.targetplatform: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: markgal,masaran
ms.openlocfilehash: 30f70a702d7d9a3e1196c04096708c035e406607
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Eseguire il backup dello stato del sistema e il ripristino bare metal con il server di Backup di Azure

Il server di Backup di Azure esegue il backup dello stato del sistema e offre la protezione del ripristino bare metal.

*   **Backup dello stato del sistema**: viene eseguito il backup dei file del sistema operativo, in modo da consentire il ripristino quando un computer viene avviato, ma i file di sistema e il registro vengono persi. Un backup dello stato del sistema include:
    * Membro di dominio: file di avvio, database di registrazione della classe COM+, registro
    * Controller di dominio: file di avvio di Windows Server Active Directory (NTDS), database di registrazione della classe COM+, registro, volume di sistema (SYSVOL)
    * Computer che esegue servizi cluster: metadati del server di cluster
    * Computer che esegue servizi certificati: dati del certificato
* **Backup bare metal**: esegue il backup dei file del sistema operativo e di tutti i dati su volumi critici (tranne i dati utente). Per definizione, un backup bare metal include un backup dello stato del sistema. Fornisce protezione quando un computer non si avvia ed è necessario ripristinare tutto.

Nella tabella seguente sono riepilogati gli elementi di cui è possibile eseguire il backup e il ripristino. Per informazioni dettagliate sulle versioni delle app che è possibile proteggere con il ripristino dello stato del sistema e bare metal, vedere [Di quali elemento esegue il backup il server di Backup di Azure?](backup-mabs-protection-matrix.md).

|Backup|Problema|Ripristino dal backup del server di Backup di Azure|Ripristino dal backup dello stato del sistema|Ripristino bare metal|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dati di file**<br /><br />Backup dei dati regolare<br /><br />Ripristino bare metal/backup dello stato del sistema|Dati di file persi|S|N|N|
|**Dati di file**<br /><br />Backup del server di Backup di Azure dei dati di file<br /><br />Ripristino bare metal/backup dello stato del sistema|Sistema operativo perso o danneggiato|N|S|S|
|**Dati di file**<br /><br />Backup del server di Backup di Azure dei dati di file<br /><br />Ripristino bare metal/backup dello stato del sistema|Server perso (volumi di dati intatti)|N|N|S|
|**Dati di file**<br /><br />Backup del server di Backup di Azure dei dati di file<br /><br />Ripristino bare metal/backup dello stato del sistema|Server perso (volumi di dati persi)|S|No|Sì (ripristino bare metal, seguito dal ripristino regolare dei dati di file di cui è stato eseguito il backup)|
|**Dati di SharePoint**:<br /><br />Backup del server di Backup di Azure dei dati della farm<br /><br />Ripristino bare metal/backup dello stato del sistema|Sito, elenchi, elementi elenco, documenti persi|S|N|N|
|**Dati di SharePoint**:<br /><br />Backup del server di Backup di Azure dei dati della farm<br /><br />Ripristino bare metal/backup dello stato del sistema|Sistema operativo perso o danneggiato|N|S|S|
|**Dati di SharePoint**:<br /><br />Backup del server di Backup di Azure dei dati della farm<br /><br />Ripristino bare metal/backup dello stato del sistema|Ripristino di emergenza|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Backup del server di Backup di Azure dell'host o guest Hyper-V<br /><br />Ripristino bare metal/backup dello stato del sistema di host|Macchine virtuali perse|S|N|N|
|Hyper-V<br /><br />Backup del server di Backup di Azure dell'host o guest Hyper-V<br /><br />Ripristino bare metal/backup dello stato del sistema di host|Sistema operativo perso o danneggiato|N|S|S|
|Hyper-V<br /><br />Backup del server di Backup di Azure dell'host o guest Hyper-V<br /><br />Ripristino bare metal/backup dello stato del sistema di host|Host Hyper-V perso (macchine virtuali intatte)|N|N|S|
|Hyper-V<br /><br />Backup del server di Backup di Azure dell'host o guest Hyper-V<br /><br />Ripristino bare metal/backup dello stato del sistema di host|Host Hyper-V perso (macchine virtuali perse)|N|N|S<br /><br />Ripristino bare metal, seguito da ripristino regolare del server di Backup di Azure|
|SQL Server/Exchange<br /><br />Backup dell'app del server di Backup di Azure<br /><br />Ripristino bare metal/backup dello stato del sistema|Dati di app persi|S|N|N|
|SQL Server/Exchange<br /><br />Backup dell'app del server di Backup di Azure<br /><br />Ripristino bare metal/backup dello stato del sistema|Sistema operativo perso o danneggiato|N|y|S|
|SQL Server/Exchange<br /><br />Backup dell'app del server di Backup di Azure<br /><br />Ripristino bare metal/backup dello stato del sistema|Server perso (database/log delle transazioni intatti)|N|N|S|
|SQL Server/Exchange<br /><br />Backup dell'app del server di Backup di Azure<br /><br />Ripristino bare metal/backup dello stato del sistema|Server perso (database/log delle transazioni persi)|N|N|S<br /><br />Ripristino bare metal, seguito da ripristino regolare del server di Backup di Azure|

## <a name="how-system-state-backup-works"></a>Funzionamento del backup dello stato del sistema

Quando viene eseguito un backup dello stato del sistema, il server di Backup comunica con Windows Server Backup per richiedere un backup dello stato del sistema del server. Per impostazione predefinita, il server di Backup e Windows Server Backup utilizzano l'unità con più spazio libero. Le informazioni su questa unità vengono salvate nel file PSDataSourceConfig.xml. Si tratta dell'unità utilizzata da Windows Server Backup per i backup.

È possibile personalizzare l'unità usata dal server di Backup per il backup dello stato del sistema. Nel server protetto, andare a C:\Programmi\Microsoft Data Protection Manager\MABS\Datasources. Aprire il file PSDataSourceConfig.xml per la modifica. Modificare il valore \<FilesToProtect\> per la lettera dell'unità. Salvare e chiudere il file. Se un gruppo protezione dati è impostato per proteggere lo stato del sistema del computer, eseguire una verifica di coerenza. Se viene generato un avviso, selezionare **Modifica gruppo protezione dati** nell'avviso, quindi completare la procedura guidata. Eseguire quindi un'altra verifica di coerenza.

Si noti che se il server di protezione è in un cluster, è possibile che un'unità cluster venga selezionata come unità con più spazio libero. Se la proprietà di tale unità è passata a un altro nodo e si esegue il backup dello stato di sistema, l'unità non è disponibile e il backup ha esito negativo. In questo scenario, modificare PSDataSourceConfig.xml in modo che punti a un'unità locale.

Successivamente, Windows Server Backup crea una cartella denominata WindowsImageBackup nella radice della cartella di ripristino. Mentre Windows Server Backup crea il backup, tutti i dati vengono posizionati in questa cartella. Al termine del backup, il file viene trasferito nel computer del server di Backup. Tenere presente quanto segue:

* Questa cartella e il relativo contenuto non vengono eliminati al termine del backup o del trasferimento. Per comprendere questa condizione, pensare che lo spazio viene riservato per il completamento del backup successivo.
* La cartella viene creata ogni volta che viene eseguito un backup. L'indicatore di data e ora riflette l'ora dell'ultimo backup dello stato del sistema.

## <a name="bmr-backup"></a>Backup bare metal

Per il ripristino bare metal (che include un backup dello stato del sistema), il processo di backup viene salvato direttamente in una condivisione nel computer del server di Backup. Non viene salvato in una cartella nel server protetto.

Il server di backup chiama Windows Server Backup e condivide il volume di replica per il backup bare metal. In questo caso, non indica a Windows Server Backup di usare l'unità con più spazio libero. Utilizza invece la condivisione creata per il processo.

Al termine del backup, il file viene trasferito nel computer del server di Backup. I log vengono archiviati in C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Prerequisiti e limitazioni

-   Il ripristino bare metal non è supportato per i computer con Windows Server 2003 o che eseguono un sistema operativo client.

-   Non è possibile proteggere il ripristino bare metal e lo stato del sistema per lo stesso computer in diversi gruppi protezione dati.

-   Un computer server di Backup non può proteggere sé stesso per il ripristino bare metal.

-   La protezione a breve termine su nastro (da disco a nastro o D2T) non è supportata per il ripristino bare metal. È supportata l'archiviazione a lungo termine su nastro (da disco a disco a nastro o D2D2T).

-   Per la protezione del ripristino bare metal, Windows Server Backup deve essere installato nel computer protetto.

-   Per la protezione del ripristino bare metal, a differenza della protezione dello stato del sistema, il server di Backup non ha requisiti di spazio nel computer protetto. Windows Server Backup trasferisce direttamente i backup nel computer del server di Backup. Il processo di trasferimento del backup non viene visualizzato nella vista **Processi** del server di Backup.

-   Il server di Backup riserva 30 GB di spazio nel volume di replica per il ripristino bare metal. È possibile modificare questo valore nella pagina **Allocazione dischi** della procedura guidata Modifica gruppo protezione dati o tramite i cmdlet di PowerShell Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation. Nel volume del punto di ripristino, la protezione del ripristino bare metal richiede circa 6 GB per una conservazione di cinque giorni.
    * Si noti che è possibile ridurre le dimensioni del volume di replica a meno di 15 GB.
    * Il server di backup non calcola le dimensioni dell'origine dati del ripristino bare metal. Presume 30 GB per tutti i server. Modificare il valore in base alle dimensioni dei backup bare metal previsti nell'ambiente in uso. Le dimensioni di un backup bare metal possono essere calcolate approssimativamente come la somma dello spazio utilizzato in tutti i volumi critici. Volumi critici = volume di avvio + volume di sistema + volume che ospita i dati dello stato del sistema, ad esempio Active Directory.

-   Se si passa dalla protezione dello stato del sistema alla protezione del ripristino bare metal, quest'ultima richiede meno spazio sul *volume del punto di ripristino*. Tuttavia, lo spazio aggiuntivo sul volume non viene recuperato. È possibile ridurre manualmente le dimensioni del volume nella pagina **Modifica allocazione dischi** della procedura guidata Modifica gruppo protezione dati o tramite i cmdlet di PowerShell Get-DatasourceDiskAllocation e Set-DatasourceDiskAllocation.

    Se si passa dalla protezione dello stato del sistema alla protezione del ripristino bare metal, quest'ultima richiede più spazio sul *volume di replica*. Il volume viene esteso automaticamente. Se si desidera modificare le allocazioni di spazio predefinite, usare il cmdlet PowerShell Modify-DiskAllocation.

-   Se si passa dalla protezione del ripristino bare metal alla protezione dello stato del sistema, è necessario più spazio sul volume del punto di ripristino. È possibile che il server di Backup tenti di aumentare automaticamente il volume. Se lo spazio nel pool di archiviazione è insufficiente, si verifica un errore.

    Se si passa dalla protezione del ripristino bare metal alla protezione dello stato del sistema, è necessario spazio sul computer protetto. Ciò si deve al fatto che la protezione dello stato del sistema scrive la replica nel computer locale, quindi la trasferisce nel computer del server di Backup.

## <a name="before-you-begin"></a>Prima di iniziare

1.  **Distribuire il server di Backup di Azure**. Verificare che il server di Backup sia distribuito correttamente. Per altre informazioni, vedere:
    * [Requisiti di sistema per il server di Backup di Azure](http://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matrice di protezione del server di Backup](backup-mabs-protection-matrix.md)

2.  **Configurare l'archiviazione**. È possibile archiviare i dati di backup su disco, su nastro e nel cloud con Azure. Per altre informazioni, vedere [Preparare l'archiviazione dei dati](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Configurare l'agente protezione**. Installare l'agente protezione nel computer di cui eseguire il backup. Per altre informazioni, vedere [Distribuire l'agente protezione DPM](http://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Backup dello stato del sistema e bare metal
Configurare un gruppo protezione dati come descritto in [Distribuire gruppi protezione dati](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Si noti che non è possibile proteggere il ripristino bare metal e lo stato del sistema per lo stesso computer in gruppi diversi. Inoltre, quando si seleziona il ripristino bare metal, lo stato del sistema viene abilitato automaticamente.


1.  Per aprire la procedura guidata Crea nuovo gruppo protezione dati nella Console di amministrazione del server di Backup, selezionare **Protezione** > **Azioni** > **Crea gruppo protezione dati**.

2.  Nella pagina **Selezione tipo di gruppo protezione dati** selezionare **Server**, quindi **Avanti**.

3.  Nella pagina **Selezione membri del gruppo** espandere il computer, quindi selezionare **Ripristino bare metal** o **Stato del sistema**.

    Si noti che non è possibile proteggere il ripristino bare metal e lo stato del sistema per lo stesso computer in gruppi diversi. Inoltre, quando si seleziona il ripristino bare metal, lo stato del sistema viene abilitato automaticamente. Per altre informazioni, vedere [Distribuire gruppi di protezione](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Nella pagina **Seleziona metodo protezione dati** selezionare come gestire i backup a breve e a lungo termine. Il backup a breve termine viene sempre eseguito prima su disco, con l'opzione di eseguire il backup dal disco nel cloud di Azure utilizzando Backup di Azure (a breve o a lungo termine). Un'alternativa al backup a lungo termine nel cloud consiste nella configurazione del backup a lungo termine su un dispositivo a nastro o una libreria di nastri autonoma connessa al server di Backup.

5.  Nella pagina **Selezione obiettivi a breve termine** selezionare come si desidera eseguire il backup per l'archiviazione a breve termine su disco:
    1. Per **Intervallo conservazione** selezionare per quanto tempo si desidera conservare i dati su disco. 
    2. Per **Frequenza di sincronizzazione** selezionare la frequenza con cui eseguire un backup incrementale su disco. Se non si desidera impostare un intervallo di backup, selezionare l'opzione **Immediatamente prima di un punto di ripristino**. Il server di backup eseguirà un backup completo rapido appena prima di ogni punto di ripristino pianificato.

6.  Se si desidera archiviare i dati su nastro per l'archiviazione a lungo termine, nella pagina **Specifica obiettivi a lungo termine** selezionare per quanto tempo si desidera conservare i dati su nastro (1-99 anni). 
    1. Per **Frequenza di backup** selezionare la frequenza con cui eseguire il backup su nastro. La frequenza è basata sull'intervallo di conservazione selezionato:
        * Quando l'intervallo di conservazione è 1-99 anni, è possibile selezionare l'esecuzione di backup giornaliera, settimanale, bisettimanale, mensile, trimestrale, semestrale o annuale.
        * Quando l'intervallo di conservazione è 1-11 mesi, è possibile selezionare l'esecuzione di backup giornaliera, settimanale, bisettimanale o mensile.
        * Quando l'intervallo di conservazione è 1-4 settimane, è possibile selezionare l'esecuzione di backup giornaliera o settimanale.

    2. Nella pagina **Selezione dettagli nastro e libreria** selezionare il nastro e la libreria da utilizzare e se comprimere e crittografare i dati.

7.  Nella pagina **Verifica allocazione dischi** esaminare lo spazio su disco del pool di archiviazione allocato per il gruppo protezione dati.

    1. Il valore **Dimensioni totali dei dati** è la dimensione dei dati di cui eseguire il backup.
    2. Il valore **Spazio su disco per il provisioning nel server di Backup di Azure** è lo spazio consigliato dal server di Backup per il gruppo protezione dati. Il server di Backup sceglie il volume di backup in base alle impostazioni. Tuttavia, è possibile modificare le opzioni del volume di backup in **Dettagli allocazione del disco**. 
    3. Per i carichi di lavoro, nel menu a discesa selezionare l'archiviazione preferita. Le modifiche modificano i valori per **Totale spazio di archiviazione** e **Spazio libero di archiviazione** nel riquadro **Spazio di archiviazione su disco disponibile**. Per spazio con provisioning insufficiente si intende la quantità di spazio di archiviazione che il server di Backup consiglia di aggiungere al volume per garantire backup uniformi.

8.  Nella pagina **Scelta del metodo per la creazione della replica** selezionare come gestire la replica dei dati completa iniziale. Se si sceglie di eseguire la replica in rete, è consigliabile scegliere un orario di scarso traffico. Per grandi quantità di dati o condizioni di rete non ottimali, tenere in considerazione la replica dei dati offline mediante supporti rimovibili.

9. Nella pagina **Scelta opzioni di verifica coerenza** selezionare come automatizzare le verifiche della coerenza. È possibile scegliere di eseguire una verifica solo quando i dati di replica diventano incoerenti o in base a una pianificazione. Per non configurare verifiche della coerenza automatiche, è possibile eseguire una verifica manuale in qualsiasi momento. Per eseguire una verifica manuale, nell'area **Protezione** della console di amministrazione del server di Backup fare clic con il pulsante destro del mouse sul gruppo protezione dati e quindi selezionare **Esegui verifica coerenza**.

10. Se si è scelto di eseguire il backup nel cloud con Backup di Azure, nella pagina **Specifica i dati da proteggere online** verificare di selezionare i carichi di lavoro di cui eseguire il backup in Azure.

11. Nella pagina **Specificare la pianificazione dei backup online** selezionare la frequenza con cui eseguire i backup incrementali in Azure. È possibile pianificare l'esecuzione di backup giornalieri, settimanali, mensili e annuali e selezionare la data e l'ora in cui eseguirli. È possibile eseguire i backup fino a due volte al giorno. Ogni volta che viene eseguito un backup, viene creato un punto di ripristino dei dati in Azure dalla copia dei dati di backup archiviati nel disco del server di Backup.

12. Nella pagina **Specificare i criteri di conservazione online** selezionare la modalità di conservazione in Azure dei punti di ripristino creati dai backup giornalieri, settimanali, mensili e annuali.

13. Nella pagina **Scegliere la replica online** selezionare la modalità di esecuzione della replica completa iniziale dei dati. È possibile eseguire la replica in rete o eseguire un backup offline (seeding offline). Il backup offline utilizza la funzionalità di importazione di Azure. Per altre informazioni, vedere [Flusso di lavoro del backup offline in Backup di Azure](backup-azure-backup-import-export.md).

14. Nella pagina **Riepilogo** esaminare le impostazioni. Dopo aver selezionato **Crea gruppo**, viene eseguita la replica iniziale dei dati. Al termine della replica dei dati, nella pagina **Stato** lo stato del gruppo protezione dati è **OK**. Viene quindi eseguito il backup in base alle impostazioni del gruppo protezione dati.

## <a name="recover-system-state-or-bmr"></a>Ripristino dello stato del sistema o bare metal
È possibile eseguire il ripristino dello stato del sistema o bare metal in un percorso di rete. Se è stato eseguito un backup bare metal, utilizzare Ambiente ripristino Windows (WinRE) per l'avvio del sistema e la connessione alla rete. Utilizzare quindi Windows Server Backup per eseguire il ripristino dal percorso di rete. Se è stato eseguito un backup dello stato del sistema, utilizzare solo Windows Server Backup per eseguire il ripristino dal percorso di rete.

### <a name="restore-bmr"></a>Ripristino bare metal
Eseguire il ripristino nel computer del server di Backup:

1.  Nel riquadro **Ripristino** trovare il computer da ripristinare e quindi selezionare **Ripristino bare metal**.

2.  I punti di ripristino disponibili sono indicati in grassetto nel calendario. Selezionare la data e l'ora del punto di ripristino da utilizzare.

3.  Nella pagina **Selezione tipo di ripristino** selezionare **Copia in una cartella di rete**.

4.  Nella pagina **Specifica destinazione** selezionare il percorso in cui copiare i dati. Si ricordi che la destinazione selezionata deve disporre di spazio sufficiente. È consigliabile creare una nuova cartella.

5.  Nella pagina **Specifica opzioni di ripristino** selezionare le impostazioni di sicurezza da applicare. Selezionare quindi se si desidera utilizzare snapshot dell'hardware basati su una rete di archiviazione (SAN), per un ripristino più veloce. Questa opzione è valida solo se si dispone di una SAN con questa funzionalità disponibile e la possibilità di creare e suddividere un clone per renderlo scrivibile. Inoltre, il computer protetto e il computer del server di Backup devono essere connessi alla stessa rete.

6.  Configurare le opzioni di notifica. Nella pagina **Conferma** selezionare **Ripristina**.

Configurare il percorso di condivisione:

1.  Nel percorso di ripristino passare alla cartella contenente il backup.

2.  Condividere la cartella di un livello superiore a WindowsImageBackup in modo che la radice della cartella condivisa sia la cartella WindowsImageBackup. Se non si esegue questa operazione, il ripristino non trova il backup. Per la connessione tramite Ambiente ripristino Windows (WinRE), è necessaria una condivisione accessibile in WinRE con l'indirizzo IP e le credenziali corretti.

Ripristinare il sistema:

1.  Avviare il computer in cui ripristinare l'immagine usando il DVD di Windows per il sistema da ripristinare.

2.  Nella prima pagina verificare la lingua e le impostazioni locali. Nella pagina **Installa** selezionare **Ripristina il computer**.

3.  Nella pagina **Opzioni ripristino di sistema** selezionare **Ripristinare il computer utilizzando un'immagine del sistema creata in precedenza**.

4.  Nella pagina **Selezionare un backup dell'immagine del sistema** selezionare **Seziona un'immagine del sistema** > **Avanzate** > **Cerca immagine del sistema in rete**. Se viene visualizzato un avviso, selezionare **Sì**. Passare al percorso della condivisione, immettere le credenziali e quindi selezionare il punto di ripristino. Viene avviata la ricerca di backup specifici disponibili nel punto di ripristino. Selezionare il punto di ripristino da utilizzare.

5.  Nella pagina **Scegliere la modalità di ripristino del backup** selezionare **Formatta e partiziona i dischi**. Nella pagina successiva verificare le impostazioni. 

6.  Per iniziare il ripristino selezionare **Fine**. È necessario un riavvio.

### <a name="restore-system-state"></a>Ripristino dello stato del sistema

Eseguire il ripristino nel server di Backup:

1.  Nel riquadro **Ripristino** trovare il computer da ripristinare e quindi selezionare **Ripristino bare metal**.

2.  I punti di ripristino disponibili sono indicati in grassetto nel calendario. Selezionare la data e l'ora del punto di ripristino da utilizzare.

3.  Nella pagina **Selezione tipo di ripristino** selezionare **Copia in una cartella di rete**.

4.  Nella pagina **Specifica destinazione** selezionare il percorso in cui copiare i dati. Si ricordi che la destinazione selezionata deve disporre di spazio sufficiente. È consigliabile creare una nuova cartella.

5.  Nella pagina **Specifica opzioni di ripristino** selezionare le impostazioni di sicurezza da applicare. Selezionare quindi se si desidera utilizzare snapshot dell'hardware basati su SAN, per un ripristino più veloce. Questa opzione è valida solo se si dispone di una SAN con questa funzionalità disponibile e la possibilità di creare e suddividere un clone per renderlo scrivibile. Inoltre, il computer protetto e il computer del server di Backup devono essere connessi alla stessa rete.

6.  Configurare le opzioni di notifica. Nella pagina **Conferma** selezionare **Ripristina**.

Eseguire Windows Server Backup:

1.  Selezionare **Azioni** > **Ripristina** > **Questo server** > **Avanti**.

2.  Selezionare **Un altro server**, selezionare la pagina **Impostazione tipo di percorso** e quindi selezionare **Cartella condivisa remota**. Immettere il percorso della cartella che contiene il punto di ripristino.

3.  Nella pagina **Selezione tipo di ripristino** selezionare **Stato del sistema**. 

4. Nella pagina **Selezione percorso per il ripristino dello stato del sistema** selezionare **Percorso originale**.

5.  Nella pagina **Conferma** selezionare **Ripristina**. Dopo il ripristino, riavviare il server.

6.  È anche possibile eseguire il ripristino dello stato del sistema a un prompt dei comandi. A tale scopo, avviare Windows Server Backup nel computer da ripristinare. Per ottenere l'identificatore di versione, a un prompt dei comandi immettere: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Utilizzare l'identificatore di versione per avviare il ripristino dello stato del sistema. Al prompt dei comandi immettere: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Confermare che si desidera avviare il ripristino. È possibile visualizzare il processo nella finestra del prompt dei comandi. Viene creato un registro di ripristino. Dopo il ripristino, riavviare il server.

