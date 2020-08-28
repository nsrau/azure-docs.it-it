---
title: Eseguire il backup di una farm di SharePoint con Azure Stack
description: Usare il server di Backup di Azure per eseguire il backup e ripristinare i dati di SharePoint in Azure Stack. In questo articolo vengono fornite le informazioni per configurare la farm di SharePoint in modo da archiviare in Azure i dati desiderati. È possibile ripristinare i dati SharePoint protetti dal disco o da Azure.
ms.topic: conceptual
ms.date: 06/07/2020
ms.openlocfilehash: 7319cf064ab2bab20e4140f8a208be843df7fa71
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017971"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Eseguire il backup di una farm di SharePoint con Azure Stack

Per eseguire il backup di una farm di SharePoint in Azure con Azure Stack usare il server di Backup di Microsoft Azure (MABS) come per eseguire il backup di altre origini dati. Backup di Azure offre flessibilità nella pianificazione di backup per creare punti di backup quotidiani, settimanali, mensili o annuali e offre diverse opzioni in termini di criteri di conservazione per i vari intervalli di backup. Offre inoltre la possibilità di archiviare copie dei dischi locali per obiettivi di tempi di ripristino (RTO) rapidi e di archiviare copie in Azure per una conservazione economicamente conveniente e a lungo termine.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versioni supportate di SharePoint e relativi scenari di protezione

Backup di Azure per MABS supporta gli scenari seguenti:

| Carico di lavoro | Version | Distribuzione di SharePoint | Protezione e ripristino |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |SharePoint distribuito come macchina virtuale Azure Stack <br> -------------- <br> SQL AlwaysOn | Proteggere le opzioni di ripristino di farm di SharePoint: farm di ripristino, database, e file o voce di elenco dai punti di ripristino del disco.  Farm e ripristino dei database dai punti di ripristino di Azure. |

## <a name="before-you-start"></a>Prima di iniziare

È necessario verificare alcuni aspetti prima di eseguire il backup di una farm di SharePoint in Azure.

### <a name="whats-not-supported"></a>Attività non supportate

* Il server di Backup di Microsoft Azure protegge una farm di SharePoint, ma non protegge gli indici di ricerca e i database di servizio dell'applicazione. Occorre configurare separatamente la protezione di questi database.

* Il server di Backup di Microsoft Azure non esegue il backup dei database di SQL Server SharePoint ospitati in condivisioni di file server di scalabilità orizzontale.

### <a name="prerequisites"></a>Prerequisiti

Prima di continuare, assicurarsi che tutti i [prerequisiti per l'uso di Backup di Microsoft Azure](backup-azure-dpm-introduction.md#prerequisites-and-limitations) per proteggere i carichi di lavoro siano stati soddisfatti. Alcune attività per i prerequisiti includono: creare insiemi di credenziali di backup, scaricare credenziali di insiemi di credenziali, installare l'agente di Backup di Azure e registrare il server di Backup di Azure con l'insieme di credenziali.

Prerequisiti e limitazioni aggiuntivi:

* Per impostazione predefinita, quando si protegge SharePoint, tutti i database del contenuto (e i database SharePoint_Config e SharePoint_AdminContent*) vengono protetti. Se si intende aggiungere personalizzazioni, ad esempio indici di ricerca, modelli o database del servizio applicazioni, oppure il servizio profili utente, è necessario configurarli per la protezione separatamente. Assicurarsi di abilitare la protezione per tutte le cartelle che includono questi tipi di funzionalità o file di personalizzazione.

* Non è possibile proteggere i database di SharePoint come origini dati SQL Server. È possibile ripristinare singoli database da un backup della farm.

* Poiché il server di Backup di Microsoft Azure viene eseguito come **sistema locale**, per eseguire il backup dei database di SQL Server, deve avere privilegi sysadmin sull'account per SQL Server. Nell'istanza di SQL Server di cui si desidera eseguire il backup, impostare NT AUTHORITY\SYSTEM su **sysadmin**.

* Per ogni 10 milioni di elementi nella farm, deve essere almeno disponibili 2 GB di spazio nel volume in cui si trova la cartella di MABS. Questo spazio è necessario per la generazione del catalogo. Per consentire l'uso del server di Backup di Microsoft Azure per eseguire uno specifico ripristino di elementi, ad esempio raccolte siti, siti, elenchi, raccolte documenti, cartelle, singoli documenti ed elementi elenco, la generazione del catalogo crea un elenco degli URL inclusi in ogni database del contenuto. È possibile visualizzare l'elenco degli URL nel pannello degli elementi ripristinabili dell'area delle attività di ripristino della console di amministrazione del server di Backup di Microsoft Azure.

* Nella farm di SharePoint, se sono disponibili database SQL Server configurati con gli alias di SQL Server, installare i componenti del client di SQL Server nel server Web front-end che verrà protetto dal server di Backup di Microsoft Azure.

* La protezione degli elementi dell'archivio applicazioni non è supportata con SharePoint 2013.

* Il server di Backup di Microsoft Azure non supporta la protezione di FILESTREAM remoto. FILESTREAM deve far parte del database.

## <a name="configure-backup"></a>Configurare il backup

Per eseguire il backup della farm di SharePoint, configurare la protezione per SharePoint utilizzando ConfigureSharePoint. exe e quindi creare un gruppo protezione dati nel server di Backup di Microsoft Azure.

1. **Eseguire ConfigureSharePoint.exe** - Questo strumento consente di configurare il servizio VSS writer di SharePoint \(WSS\) e fornisce l'agente protezione con le credenziali per la farm di SharePoint. Dopo aver distribuito l'agente protezione, il file ConfigureSharePoint.exe si trova nella cartella `<MABS Installation Path\>\bin` del server Web front\-end.  Se si dispone di più server Web front-end, è necessario installarlo solo su uno di essi. Eseguire come indicato di seguito:

    * Nel server Web front-end, a un prompt dei comandi passare a `\<MABS installation location\>\\bin\\` ed eseguire `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]`, dove:

        * **EnableSharePointProtection** abilita la protezione della farm di SharePoint, abilita VSS writer e registra l'identità dell'applicazione DCOM WssCmdletsWrapper per l'esecuzione come utente le cui credenziali vengono immesse con questa opzione. Questo account deve essere un amministratore della farm e anche un amministratore locale sul server Web front\-end.

        * **EnableSPSearchProtection** abilita la protezione della ricerca WSS 3.0 SP usando la chiave del Registro di sistema SharePointSearchEnumerationEnabled in HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ sul server Web front\-end e registra l'identità dell'applicazione DCOM WssCmdletsWrapper per l'esecuzione come utente le cui credenziali vengono immesse con questa opzione. Questo account deve essere un amministratore della farm e anche un amministratore locale sul server Web front\-end.

        * **ResolveAllSQLAliases** mostra tutti gli alias riportati da SharePoint VSS writer e li risolve nell'istanza di SQL Server corrispondente. Inoltre, vengono visualizzati i relativi nomi di istanza risolti. Se viene eseguito il mirroring dei server, viene visualizzato anche il server oggetto di tale operazione. Segnala tutti gli alias che non sono in corso di risoluzione in un'istanza di SQL Server.

        * **SetTempPath** imposta le variabili di ambiente TEMP e TMP in corrispondenza del percorso specificato. Il ripristino a livello di elemento ha esito negativo se riguarda una raccolta siti, un sito, un elenco o un elemento e se non è presente spazio sufficiente nella cartella temporanea dell'amministratore della farm. L'opzione consente di modificare il percorso della cartella dei file temporanei al fine di indicare un volume dotato di spazio sufficiente per archiviare la raccolta siti o il sito oggetto del ripristino.

    * Immettere le credenziali di amministratore della farm. Questo account deve essere un membro del gruppo degli amministratori locale nel server front-end Web. Se l'amministratore della farm non è un amministratore locale, concedere le autorizzazioni seguenti sul server front-end Web:

        * Concedere al gruppo **WSS_Admin_WPG** il controllo completo sulla cartella del Server di Backup di Microsoft Azure (`%Program Files%\Data Protection Manager\DPM\`).

        * Concedere al gruppo **WSS_Admin_WPG** l'accesso in lettura alla chiave del Registro di sistema del Server di Backup di Microsoft Azure (`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager`).

        Dopo aver eseguito ConfigureSharePoint.exe, è necessario ripetere l'operazione se si verifica un cambiamento nelle credenziali di amministratore di farm SharePoint.

1. Per creare un nuovo gruppo protezione dati, fare clic su **Protezione** > **Azioni** > **Crea gruppo protezione dati** per aprire la procedura guidata **Crea nuovo gruppo protezione dati** nella console del server di Backup di Microsoft Azure.

1. In **Selezione tipo di gruppo protezione dati** selezionare **Server**.

1. In **Selezione membri del gruppo** espandere il server che include il ruolo Web front-end. Se è presente più di un server Web front-end, selezionare quello in cui è stato installato ConfigureSharePoint.exe.

    Se si espande il server SharePoint, il server di Backup di Microsoft Azure esegue una query su VSS per capire quali dati possono essere protetti dal server.  Se il database di SharePoint è remoto, il server di Backup di Microsoft Azure vi si connette. Se le origini dati di SharePoint non vengono visualizzate, verificare che VSS writer sia in esecuzione nel server SharePoint e in qualsiasi istanza remota di SQL Server e che l'agente del server di Backup di Microsoft Azure sia installato sia nel server SharePoint che in SQL Server in remoto. Assicurarsi inoltre che i database di SharePoint non siano protetti altrove come database SQL Server.

1. In **Seleziona metodo protezione dati** selezionare come gestire i backup a breve e a lungo termine. Il backup a breve \- termine è sempre su disco, con l'opzione di eseguire il backup dal disco al cloud di Azure con backup di Azure a \( breve o a lungo \- termine \) .

1. In **Selezione obiettivi a breve termine** selezionare come si desidera eseguire il backup per l'archiviazione a breve termine su disco.   Per **Intervallo conservazione** specificare per quanto tempo si intende conservare i dati su disco. Per **Frequenza di sincronizzazione** specificare la frequenza con cui eseguire un backup incrementale su disco. Se non si vuole impostare un intervallo di backup, è possibile selezionare Immediatamente prima di un punto di ripristino in modo da consentire al server di Backup di Microsoft Azure di eseguire un backup completo rapido subito prima di ogni punto di ripristino pianificato.

1. Nella pagina Verifica allocazione dischi esaminare lo spazio su disco del pool di archiviazione allocato per il gruppo protezione dati.

    Le **dimensioni dati totali** sono le dimensioni dei dati di cui si esegue il backup e lo **spazio su disco per il provisioning nel server di Backup di Microsoft Azure** è lo spazio consigliato dal server per il gruppo protezione dati. Il server di Backup di Microsoft Azure sceglie il volume di backup in base alle impostazioni. Tuttavia è possibile modificare le opzioni del volume di backup nei **dettagli di allocazione del disco**. Per i carichi di lavoro, selezionare l'archiviazione preferita nel menu a discesa. Le modifiche modificano i valori per **Totale spazio di archiviazione** e **Spazio libero di archiviazione** nel riquadro **Spazio di archiviazione su disco disponibile**. Per spazio con provisioning insufficiente si intende la quantità di spazio di archiviazione che il server di Backup di Microsoft Azure suggerisce di aggiungere al volume per continuare a eseguire backup uniformi in futuro.

1. In **Scelta del metodo per la creazione della replica** selezionare come gestire la replica dei dati completa iniziale.  Se si seleziona l'opzione per eseguire la replica sulla rete, si consiglia di scegliere un'ora non di punta. Per grandi quantità di dati o condizioni della rete non ottimali, tenere in considerazione la replica dei dati offline con i supporti rimovibili.

1. Nella pagina **Scelta opzioni di verifica coerenza** scegliere in che modo automatizzare le verifiche coerenza. È possibile fare in modo che una verifica venga eseguita solo quando i dati della replica diventano incoerenti o in base a una pianificazione. Se non si vuole configurare la verifica coerenza automatica, è possibile eseguire in qualsiasi momento una verifica manuale facendo clic con il pulsante destro del mouse sul gruppo protezione dati nell'area **Protezione** della console del server di Backup di Microsoft Azure e scegliendo **Esegui verifica coerenza**.

1. Se si è scelto di eseguire il backup nel cloud con Backup di Azure, nella pagina **Specifica i dati da proteggere online** verificare che siano selezionati i carichi di lavoro di cui eseguire il backup in Azure.

1. Nella pagina **Specificare la pianificazione dei backup online** specificare la frequenza con cui eseguire i backup incrementali in Azure. È possibile pianificare l'esecuzione di backup giornalieri, settimanali, mensili e annuali nonché la data e l'ora in cui eseguirli. È possibile eseguire i backup fino a due volte al giorno. Ogni volta che viene eseguito un backup, viene creato un punto di ripristino dei dati in Azure dalla copia dei dati sottoposti a backup archiviati nel disco del server di Backup di Microsoft Azure.

1. In **Specificare i criteri di conservazione online** è possibile specificare la modalità di conservazione in Azure dei punti di recupero creati dai backup giornalieri, settimanali, mensili e annuali.

1. In **Scegliere la replica online** specificare la modalità di esecuzione della replica completa iniziale dei dati. È possibile eseguire la replica in rete o eseguire un backup offline (seeding offline). Il backup offline usa la funzionalità di importazione di Azure. [Altre informazioni](./backup-azure-backup-import-export.md).

1. Nella pagina **Riepilogo** esaminare le impostazioni. Dopo aver fatto clic su **Crea gruppo**, viene eseguita la replica iniziale dei dati. Al termine, lo stato del gruppo protezione dati viene visualizzato come **OK** nella pagina **Stato**. Viene quindi eseguito il backup in base alle impostazioni del gruppo protezione dati.

## <a name="monitoring"></a>Monitoraggio

Dopo aver creato il gruppo protezione dati, viene eseguita la replica iniziale e il server di Backup di Microsoft Azure inizia il backup e la sincronizzazione dei dati di SharePoint. Il server di Backup di Microsoft Azure esegue il monitoraggio della sincronizzazione iniziale e dei backup successivi.  È possibile monitorare i dati di SharePoint in due modi:

* Tramite il monitoraggio predefinito del server di Backup di Microsoft Azure è possibile impostare notifiche per il monitoraggio proattivo, pubblicando avvisi e configurando le notifiche. È possibile inviare notifiche tramite posta elettronica per avvertenze e avvisi critici o informativi e per lo stato dei ripristini di cui è stata creata un'istanza.

* Se si usa Operations Manager, è possibile pubblicare gli avvisi in modo centralizzato.

### <a name="set-up-monitoring-notifications"></a>Impostare le notifiche di monitoraggio

1. Nella console di amministrazione del server di Backup di Microsoft Azure fare clic su **Monitoraggio** > **Azione** > **Opzioni**.

2. Fare clic su **Server SMTP**, digitare il nome del server, la porta e l'indirizzo e-mail da cui verranno inviate le notifiche. L'indirizzo deve essere valido.

3. Nell'area **Server SMTP autenticato** digitare un nome utente e una password. Il nome utente e la password devono corrispondere al nome dell'account di dominio della persona il cui indirizzo "Da" è stato specificato nel passaggio precedente. In caso contrario, il recapito delle notifiche ha esito negativo.

4. Per verificare le impostazioni del server SMTP, fare clic su **Invia messaggio di posta elettronica di prova**, digitare l'indirizzo e-mail di destinazione per l'invio del messaggio di prova dal server di Backup di Microsoft Azure e quindi fare clic su **OK**. Fare clic su **Opzioni** > **Notifiche** e selezionare i tipi di avvisi per cui inviare notifica ai destinatari. In **Destinatari** digitare l'indirizzo e-mail di ogni destinatario a cui inviare copie delle notifiche.

### <a name="publish-operations-manager-alerts"></a>Pubblicare avvisi di Operations Manager

1. Nella console di amministrazione del server di Backup di Microsoft Azure fare clic su **Monitoraggio** > **Azione** > **Opzioni** > **Pubblicazione avvisi** > **Pubblica avvisi attivi**

2. Dopo aver abilitato **Pubblicazione avvisi**, tutti gli avvisi del server di Backup di Microsoft Azure esistenti che potrebbero richiedere l'intervento dell'utente vengono pubblicati nel registro eventi **MABS Alerts** (Avvisi server di Backup di Microsoft Azure). L'agente di Operations Manager installato nel server MAB pubblica quindi questi avvisi nel Operations Manager e continua ad aggiornare la console Man via che vengono generati nuovi avvisi.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Ripristinare un elemento di SharePoint dal disco tramite MABS

Nell'esempio seguente, l' *elemento di SharePoint da ripristinare* è stato accidentalmente eliminato e deve essere ripristinato.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Aprire la **console di amministrazione del server di Backup di Microsoft Azure**. Tutte le farm di SharePoint protette dal server di Backup di Microsoft Azure vengono visualizzate nella scheda **Protezione**.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Per iniziare a ripristinare l'elemento, selezionare la scheda **Ripristino** .

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. È possibile eseguire ricerche in SharePoint relative all' *elemento di SharePoint da ripristinare* tramite una ricerca con caratteri jolly all'interno di un intervallo di punti di ripristino.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selezionare il punto di ripristino appropriato dai risultati della ricerca, fare clic con il pulsante destro del mouse sull'elemento e selezionare **Ripristina**.
5. È inoltre possibile scorrere i vari punti di ripristino e selezionare un database o un elemento da recuperare. Selezionare **Data > Tempo di ripristino**, quindi selezionare l'elemento corretto da **Database > Farm SharePoint > Punto di ripristino > Elemento**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Fare clic con il pulsante destro del mouse sull'elemento e selezionare **Ripristina** per aprire il **Ripristino guidato**. Fare clic su **Avanti**.

    ![Verifica selezione per ripristino](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selezionare il tipo di ripristino che si vuole eseguire, quindi fare clic su **Avanti**.

    ![Tipo di ripristino](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > In questo esempio, la selezione di **Ripristina originale** ripristina l'elemento nel sito originale di SharePoint.
   >
   >
8. Selezionare il **processo di ripristino** che si vuole usare.

   * Selezionare **Ripristina senza utilizzare una farm di ripristino** se la farm di SharePoint non è stata modificata ed è uguale al punto di ripristino da ripristinare.
   * Selezionare **Ripristina utilizzando una farm di ripristino** se la farm di SharePoint è stato modificata dopo che il punto di ripristino è stato creato.

     ![processo di ripristino](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Specificare un percorso di istanza di gestione temporanea di SQL Server per ripristinare temporaneamente il database e specificare una condivisione di file di gestione temporanea in MABS e nel server che esegue SharePoint per ripristinare l'elemento.

    ![Staging Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MAB connette il database del contenuto che ospita l'elemento di SharePoint all'istanza di SQL Server temporanea. MABS ripristina l'elemento dal database del contenuto e lo aggiunge al percorso di file di gestione temporanea di MABS. L'elemento recuperato che si trova nel percorso di gestione temporanea deve ora essere esportato nel percorso di gestione temporaneo della farm di SharePoint.

    ![Gestione temporanea Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selezionare **Specifica opzioni di ripristino**e applicare le impostazioni di sicurezza per la farm di SharePoint o applicare le impostazioni di sicurezza del punto di ripristino. Fare clic su **Avanti**.

    ![Opzioni di ripristino](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > È possibile scegliere di limitare l'utilizzo della larghezza di banda di rete. Consente di ridurre l'impatto sul server di produzione durante l'orario di produzione.
    >
    >
11. Esaminare le informazioni di riepilogo e fare clic su **Ripristina** per avviare il ripristino del file.

    ![Riepilogo di ripristino](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Selezionare la scheda **Monitoraggio** nella **Console di amministrazione MABS** per visualizzare lo **Stato** del ripristino.

    ![Stato di ripristino](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > A questo punto è possibile ripristinare il file. È possibile aggiornare il sito di SharePoint per controllare il file ripristinato.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>Ripristinare un database di SharePoint da Azure tramite il server di Backup di Microsoft Azure

1. Per ripristinare un database del contenuto di SharePoint, scorrere i vari punti di ripristino (come illustrato in precedenza) e selezionare il punto di ripristino che si vuole ripristinare.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Fare doppio clic sul punto di ripristino di SharePoint per visualizzare le informazioni del catalogo di SharePoint disponibili.

   > [!NOTE]
   > Poiché la farm di SharePoint è protetta per la conservazione a lungo termine in Azure, non sono disponibili informazioni sul catalogo (metadati) nel server di Backup di Microsoft Azure. Di conseguenza, ogni volta che si vuole ripristinare un database del contenuto di SharePoint temporizzato, si deve ricatalogare la farm di SharePoint.
   >
   >
3. Fare clic su **Ricatalogazione**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Viene visualizzata la finestra di stato di **Ricatalogazione cloud** .

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Dopo aver completato la catalogazione, viene visualizzato lo stato *Operazione completata*. Fare clic su **Close**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Fare clic sull'oggetto di SharePoint visualizzato nella scheda **Ripristino** di MABS per ottenere la struttura del database del contenuto. Fare clic con il pulsante destro del mouse sull'elemento e scegliere **Ripristina**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. A questo punto seguire i passaggi di ripristino illustrati precedentemente in questo articolo per ripristinare il database del contenuto di SharePoint dal disco.

## <a name="switching-the-front-end-web-server"></a>Cambio del server Web front-end

Se si dispone di più server Web front-end e si intende cambiare il server utilizzato dal server di Backup di Microsoft Azure per proteggere la farm, seguire le istruzioni:

Nella procedura seguente viene utilizzato l'esempio di una server farm con due server Web front-end, *Server1* e *Server2*. Il server di Backup di Microsoft Azure usa *Server1* per proteggere la farm. Cambiare il server Web front-end usato dal server di Backup di Microsoft Azure per *Server2* in modo che sia possibile rimuovere *Server1* dalla farm.

> [!NOTE]
> Se il server Web front-end utilizzato dal server di Backup di Microsoft Azure per proteggere la farm non è disponibile, utilizzare la procedura seguente per modificare il server Web front-end iniziando dal passaggio 4.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>Per modificare il server Web front-end usato dal server di Backup di Microsoft Azure per proteggere la farm

1. Arrestare il servizio SharePoint VSS writer in *Server1* eseguendo il comando seguente al prompt dei comandi:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. In *Server1* aprire l'editor del Registro di sistema e selezionare la chiave seguente:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. Controllare tutti i valori elencati nella sottochiave VssAccessControl. Se una voce contiene dati di valore pari a 0 e un altro VSS writer è in esecuzione con le credenziali dell'account associate, impostare i dati di valore su 1.

1. Installare un agente protezione in *Server2*.

   > [!WARNING]
   > È possibile cambiare server front-end Web solo se entrambi i server si trovano nello stesso dominio.

1. In *Server2*, al prompt dei comandi, impostare la directory su `_MABS installation location_\bin\` ed eseguire **ConfigureSharepoint**. Per ulteriori informazioni su ConfigureSharePoint, vedere [Configurare il backup](#configure-backup).

1. Selezionare il gruppo protezione dati a cui appartiene la server farm, quindi fare clic su **Modifica gruppo protezione dati**.

1. Nella procedura guidata Modifica gruppo, nella pagina **Selezione membri del gruppo**, espandere *Server2* e selezionare la server farm, quindi completare la procedura guidata.

   Viene avviata una verifica coerenza.

1. Se è stato eseguito il passaggio 6, è ora possibile rimuovere il volume dal gruppo protezione dati.

## <a name="next-steps"></a>Passaggi successivi

* Vedere l'articolo [Eseguire il backup di file e applicazioni in Azure Stack](backup-mabs-files-applications-azure-stack.md).
* Vedere l'articolo [Backup SQL Server on Azure Stack](backup-mabs-sql-azure-stack.md) (Eseguire il backup di SQL Server in Azure Stack).
