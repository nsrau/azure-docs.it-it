<properties
	pageTitle="Protezione del server DPM/Backup di Azure di una farm di SharePoint in Azure | Microsoft Azure"
	description="Questo articolo offre una panoramica sulla protezione del server DPM/Backup di Azure di una farm di SharePoint in Azure"
	services="backup"
	documentationCenter=""
	authors="nkolli1"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/06/2015"
	ms.author="giridham;jimpark"/>


# Eseguire il backup di una farm di SharePoint in Azure
Il backup di una farm di SharePoint in Azure si esegue tramite System Center Data Protection Manager (DPM) in modo analogo al backup delle altre origini dati. Backup di Azure offre flessibilità nella pianificazione di backup per creare punti di backup quotidiani, settimanali, mensili o annuali e offre diverse opzioni in termini di criteri di conservazione per i vari intervalli di backup. DPM offre la capacità di archiviare copie dei dischi locali per obiettivi di tempi di ripristino (RTO) rapidi e di archiviare copie in Azure per una conservazione economica a lungo termine.

## Versioni supportate di SharePoint e relativi scenari di protezione
Azure Backup per DPM supporta gli scenari seguenti.

| Carico di lavoro | Versione | Distribuzione di SharePoint | Tipo di distribuzione di DPM | DPM - System Center 2012 R2 | Protezione e ripristino |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| SharePoint | SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 | SharePoint distribuito come un server fisico o macchina virtuale Hyper-V o VmWare<br> -------------- <br>SQL AlwaysOn | Server fisico o macchina virtuale Hyper-V in locale | Supporta backup in Azure dall'aggiornamento cumulativo 5 | Proteggere le opzioni di ripristino di farm di SharePoint: farm di ripristino, database, e file o voce di elenco dai punti di ripristino del disco. Farm e ripristino dei database dai punti di ripristino di Azure. |

## Prima di iniziare
È necessario verificare alcuni aspetti prima di eseguire il backup di una farm di SharePoint in Azure.

### Prerequisiti
Prima di procedere, assicurarsi che tutti i [prerequisiti per l'uso di Backup di Microsoft Azure ](backup-azure-dpm-introduction.md#prerequisites) per la protezione dei carichi di lavoro siano stati soddisfatti. Alcune attività per i prerequisiti includono: creare insiemi di credenziali di backup, scaricare credenziali di insiemi di credenziali, installare l'agente di Backup di Azure e registrare il server di Backup di Azure o DPM con l'insieme di credenziali.

### Agente DPM
L'agente di DPM deve essere installato nel server che esegue SharePoint, nei server che eseguono SQL Server e in tutti gli altri server che fanno parte della farm di SharePoint. Per altre informazioni sull'installazione dell'agente di protezione, vedere [Installazione dell'agente di protezione](https://technet.microsoft.com/library/hh758034(v=sc.12).aspx). L'unica eccezione è che l'agente viene installato in un server Web front-end (WFE) solo. DPM ha bisogno che l'agente sia installato in un solo server WFE e agisca come punto di ingresso per la protezione.

### Una farm di SharePoint
Per ogni 10 milioni di elementi nella farm, deve essere almeno disponibili 2 GB di spazio nel volume in cui si trova la cartella di DPM. Questo spazio è necessario per la generazione del catalogo. Affinché DPM ripristini elementi specifici, ad esempio raccolte siti, siti, elenchi, raccolte documenti, cartelle, singoli documenti ed elementi di elenchi, la generazione del catalogo crea un elenco degli URL inclusi in ogni database del contenuto. È possibile visualizzare l'elenco degli URL nel pannello degli elementi ripristinabili dell'area delle attività di **ripristino** della Console amministrazione DPM.

### SQL Server
DPM viene eseguito come account LocalSystem. Per eseguire il backup dei database di SQL Server, DPM deve avere privilegi sysadmin sull'account per il server che esegue SQL Server. Impostare NT AUTHORITY\\SYSTEM a *sysadmin* nel server che esegue SQL Server prima di eseguire il backup.

Se la farm di SharePoint ha un database di SQL Server configurato con alias di SQL Server, installare i componenti del client di SQL Server nel server Web front-end da proteggere con DPM.

### SharePoint Server
Mentre le prestazioni dipendono da molti fattori, ad esempio la dimensione della farm di SharePoint, in linea generale un solo Server DPM può proteggere una farm di SharePoint da 25 TB.

### Aggiornamento cumulativo 5 di DPM
Per avviare la protezione di una farm di SharePoint in Azure, è necessario installare l'aggiornamento cumulativo 5 di DPM o versioni successive. L'aggiornamento cumulativo 5 offre la possibilità di proteggere una farm di SharePoint in Azure se è stata configurata tramite SQL AlwaysOn. Per altre informazioni, vedere il post di blog che introduce l'[aggiornamento cumulativo 5 di DPM](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### Attività non supportate
- DPM protegge una farm di SharePoint, ma non protegge gli indici di ricerca e i database di servizio dell'applicazione. Occorre configurare separatamente la protezione di questi database.
- DPM non esegue il backup dei database di SQL Server SharePoint ospitati in condivisioni di file server di scalabilità orizzontale.

## Configurare la protezione di SharePoint
Prima di usare DPM per proteggere SharePoint, è necessario configurare il servizio VSS Writer di SharePoint (servizio WSS Writer) tramite **ConfigureSharePoint.exe**.

È possibile trovare **ConfigureSharePoint.exe** nella cartella [percorso di installazione di DPM]\\bin nel server Web front-end. Questo strumento fornisce all'agente protezione le credenziali per la farm di SharePoint. È possibile eseguirlo in un unico server front-end Web. Se si dispone di più server WFE, selezionarne solo uno quando si configura un gruppo di protezione dati.

### Per configurare il servizio Writer VSS di SharePoint
1. Nel server WFE, al prompt dei comandi, passare a \\bin\\ [percorso di installazione di DPM]
2. Digitare ConfigureSharePoint -EnableSharePointProtection.
3. Immettere le credenziali di amministratore della farm. Questo account deve essere un membro del gruppo degli amministratori locale nel server front-end Web. Se l'amministratore della farm non è un amministratore locale, concedere le autorizzazioni seguenti sul server front-end Web:
  - Concedere il controllo completo del gruppo WSS\_Admin\_WPG sulla cartella DPM (% Program Files%\\Microsoft Data Protection Manager\\DPM).
  - Concedere l'accesso in lettura al gruppo WSS\_Admin\_WPG per la chiave del Registro di sistema di DPM (HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft Data Protection Manager).

>[AZURE.NOTE] È necessario eseguire di nuovo ConfigureSharePoint.exe ogni volta che si verifica un cambiamento nelle credenziali di amministratore di farm SharePoint.

## Eseguire il backup di una farm di SharePoint tramite DPM
Dopo aver configurato DPM e la farm di SharePoint come descritto in precedenza, è possibile proteggere SharePoint con DPM.

### Per proteggere una farm di SharePoint
1. Dalla scheda **Protezione** della Console di amministrazione DPM fare clic su **Nuovo**.![Nuova scheda Protezione](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)

2. Nella pagina **Seleziona tipo di gruppo protezione dati** della procedura guidata **Crea nuovo gruppo protezione dati**, selezionare **Server** e fare clic su **Avanti**.

    ![Seleziona tipo di gruppo di protezione dati](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)

3. Nella schermata **Seleziona membri del gruppo**, selezionare la casella di controllo del server di SharePoint che si vuole proteggere e fare clic su **Avanti**.

    ![Seleziona membri del gruppo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

    >[AZURE.NOTE] Con l'agente DPM installato, è possibile visualizzare il server della procedura guidata. DPM mostra anche la propria struttura. Poiché è stato eseguito ConfigureSharePoint.exe, DPM comunica con il servizio VSS Writer di SharePoint e con i relativi database di SQL Server e riconosce la struttura della farm di SharePoint, i database del contenuto associati e tutti gli elementi corrispondenti.

4. Nella pagina **Seleziona metodo protezione dati** digitare il nome del **Gruppo protezione dati** e selezionare i *metodi di protezione* preferiti. Fare clic su **Avanti**.

    ![Seleziona metodo protezione dati](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

    >[AZURE.NOTE] Il metodo di protezione del disco consente di soddisfare obiettivi di tempi di ripristino brevi. Azure è una soluzione di protezione economica e a lungo termine rispetto ai nastri. Per altre informazione, vedere [Usare Backup di Azure per sostituire l'infrastruttura basata su nastro](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)

5. Nella pagina **Specifica obiettivi a breve termine** selezionare l'**intervallo di conservazione** preferito e specificare la frequenza di esecuzione dei backup.

    ![Specifica obiettivi a breve termine](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

    >[AZURE.NOTE] Poiché è molto spesso richiesto il ripristino di dati con data di creazione inferiore a cinque giorni, in questo esempio è stato selezionato un periodo di conservazione sul disco di cinque giorni ed è stato specificato che il backup non venga eseguito durante orari lavorativi.

6. Esaminare lo spazio su disco del pool di archiviazione allocato per il gruppo protezione dati e fare clic su **Avanti**.

7. Per ogni gruppo di protezione dati DPM alloca spazio su disco per archiviare e gestire le repliche. A questo punto, DPM deve creare una copia dei dati selezionati. Selezionare come e quando si vuole che la replica venga creata, quindi fare clic su **Avanti**.

    ![Scelta del metodo per la creazione della replica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

    >[AZURE.NOTE] Per assicurarsi che il traffico di rete non venga influenzato, selezionare un'ora fuori dell'orario di produzione.

8. DPM garantisce l'integrità dei dati mediante l'esecuzione di verifiche della coerenza sulla replica. Sono disponibili due opzioni: È possibile definire una pianificazione per eseguire verifiche della coerenza oppure DPM può eseguire automaticamente verifiche di coerenza sulla replica quando diventa incoerente. Selezionare l'opzione preferita e fare clic su**Avanti**.

    ![Verifica coerenza](./media/backup-azure-backup-sharepoint/consistency-check.png)

9. Nella pagina **Specifica i dati da proteggere online** selezionare la farm di SharePoint che si vuole proteggere e fare clic su **Avanti**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)

10. Nella pagina **Specifica la pianificazione dei backup online** selezionare la pianificazione preferita e fare clic su **Avanti**.

    ![Online\_backup\_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    >[AZURE.NOTE] DPM esegue massimo due backup giornalieri in Azure in orari diversi. Backup di Azure può inoltre controllare la quantità di larghezza di banda WAN che può essere usata per i backup in orari normali e di punta tramite la [limitazione della larghezza di bada della rete di Backup di Azure](https://azure.microsoft.com/en-in/documentation/articles/backup-configure-vault/#enable-network-throttling).

11. In base alla pianificazione di backup selezionata, nella pagina **Specificare i criteri di mantenimento online**selezionare i criteri di conservazione per i punti di backup giornalieri, settimanali, mensili e annuali.

    ![Online\_retention\_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    >[AZURE.NOTE] DPM usa uno schema di conservazione GFS (Grandfather-Father-Son, nonno-padre-figlio) in cui è possibile scegliere criteri di conservazione diversi per punti di backup diversi.

12. Analogamente al disco, è necessario creare una replica del punto di riferimento iniziale in Azure. Selezionare l'opzione preferita per creare una copia di backup iniziale in Azure e fare clic su **Avanti**.

    ![Online\_replica](./media/backup-azure-backup-sharepoint/online-replication.png)

13. Esaminare le impostazioni selezionate nella pagina di **riepilogo** e fare clic su**Crea gruppo**. Dopo aver creato il gruppo di protezione dati, viene visualizzato un messaggio di corretto completamento.

    ![Riepilogo](./media/backup-azure-backup-sharepoint/summary.png)

## Ripristinare un elemento di SharePoint dal disco tramite DPM
Nell'esempio seguente, l'*elemento di SharePoint da ripristinare * è stato accidentalmente eliminato e deve essere ripristinato. ![DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Aprire la **Console amministrazione DPM**. Tutte le farm di SharePoint protette da DPM vengono visualizzate nella scheda **Protezione**.

    ![DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)

2. Per iniziare a ripristinare l'elemento, selezionare la scheda **Ripristino**.

    ![DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)

3. È possibile eseguire ricerche in SharePoint relative all'*elemento di SharePoint da ripristinare* tramite una ricerca con caratteri jolly all'interno di un intervallo di punti di ripristino.

    ![DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)

4. Selezionare il punto di ripristino appropriato dai risultati della ricerca, fare clic con il pulsante destro del mouse sull'elemento e selezionare **Ripristina**.

5. È inoltre possibile scorrere i vari punti di ripristino e selezionare un database o un elemento da recuperare. Selezionare **Data > Tempo di ripristino**, quindi selezionare il **Database > farm di SharePoint > Punto di ripristino > elemento** corretto.

    ![DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)

6. Fare clic con il pulsante destro del mouse sull'elemento e selezionare **Ripristina ** per aprire **Ripristino guidato**. Fare clic su **Avanti**.

    ![Verifica selezione per ripristino](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)

7. Selezionare il tipo di ripristino che si vuole eseguire, quindi fare clic su **Avanti**.

    ![Tipo di ripristino](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

    >[AZURE.NOTE] In questo esempio, la selezione di **Ripristina originale** ripristina l'elemento nel sito originale di SharePoint.

8. Selezionare il **processo di ripristino** che si vuole usare.
    - Selezionare **Ripristina senza utilizzare una farm di ripristino** se la farm di SharePoint non è stata modificata e corrisponde al punto di ripristino eseguito.
    - Selezionare**Ripristina utilizzando una farm di ripristino**se la farm di SharePoint è stato modificata dopo che il punto di ripristino è stato creato.

    ![Processo di ripristino](./media/backup-azure-backup-sharepoint/recovery-process.png)

9. Specificare un percorso di istanza di gestione temporanea di SQL Server per ripristinare temporaneamente il database e specificare una condivisione di file di gestione temporanea nel server DPM e nel server che esegue SharePoint per ripristinare l'elemento.

    ![Staging Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM collega il database del contenuto che ospita l'elemento di SharePoint all'istanza di gestione temporanea di SQL Server. Il server DPM ripristina l'elemento dal database del contenuto e lo aggiunge al percorso di file di gestione temporanea del server DPM. L'elemento recuperato che si trova nel percorso di gestione temporanea del server DPM deve ora essere esportato nel percorso di gestione temporaneo della farm di SharePoint.

    ![Gestione temporanea Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)

10. Selezionare **Specifica opzioni di ripristino** e applicare le impostazioni di sicurezza per la farm di SharePoint o applicare le impostazioni di sicurezza del punto di ripristino. Fare clic su **Avanti**.

    ![Opzioni di ripristino](./media/backup-azure-backup-sharepoint/recovery-options.png)

    >[AZURE.NOTE] È possibile scegliere di limitare l'utilizzo della larghezza di banda di rete. Consente di ridurre l'impatto sul server di produzione durante l'orario di produzione.

11. Esaminare le informazioni di riepilogo e fare clic su**Ripristina** per avviare il ripristino del file.

    ![Riepilogo di ripristino](./media/backup-azure-backup-sharepoint/recovery-summary.png)

12. Selezionare la scheda **Monitoraggio** nella scheda **Console di amministrazione DPM** per visualizzare lo **Stato** del ripristino.

    ![Stato di ripristino](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    >[AZURE.NOTE] A questo punto è possibile ripristinare il file. È possibile aggiornare il sito di SharePoint per controllare il file ripristinato.

## Ripristinare un database di SharePoint da Azure tramite DPM

1. Per ripristinare un database del contenuto di SharePoint, scorrere i vari punti di ripristino (come illustrato in precedenza) e selezionare il punto di ripristino che si vuole ripristinare.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)

2. Fare doppio clic sul punto di ripristino di SharePoint per visualizzare le informazioni del catalogo di SharePoint disponibili.

    > [AZURE.NOTE] Poiché la farm di SharePoint è protetta per la conservazione a lungo termine in Azure, non è disponibile nessuna informazione sul catalogo (metadati) nel server DPM. Di conseguenza, ogni volta che si vuole ripristinare un database del contenuto di SharePoint temporizzato, si deve ricatalogare la farm di SharePoint.

3. Fare clic su **Ricatalogazione**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Viene visualizzata la finestra di stato di **Ricatalogazione cloud**.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Dopo aver completato la catalogazione, viene visualizzato lo stato *Operazione completata*. Fare clic su **Close**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)

4. Fare clic sull'oggetto di SharePoint visualizzato nella scheda **Ripristino** di DPM per ottenere la struttura del database del contenuto. Fare clic con il pulsante destro del mouse sull'elemento e scegliere **Ripristina**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)

5. A questo punto seguire i [passaggi di ripristino illustrati precedentemente in questo articolo](#restore-a-sharepoint-item-from-disk-using-dpm) per ripristinare il database del contenuto di SharePoint dal disco.

## Domande frequenti
D: quali versioni di DPM supportano SQL Server 2014 e SQL 2012 (SP2)?<br> R: DPM 2012 R2 con aggiornamento cumulativo 4 supporta entrambi.

D: è possibile ripristinare un elemento di SharePoint nel percorso originale se SharePoint è configurato con SQL AlwaysOn (con protezione su disco)?<br> R: Sì, l'elemento può essere ripristinato nel sito originale di SharePoint.

D: è possibile ripristinare un database di SharePoint nel percorso originale se SharePoint è configurato con SQL AlwaysOn?<br> R: poiché i database di SharePoint sono configurati con SQL AlwaysOn, non possono essere modificati se non si rimuove il gruppo di disponibilità. Di conseguenza DPM non può ripristinare il database nel percorso originale. È possibile ripristinare un database di SQL Server in un'altra istanza di SQL Server.

## Passaggi successivi
- Ulteriori informazioni su Protezione DPM di SharePoint, vedere [Serie di Video - Protezione DPM di SharePoint](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
- Esaminare le [Note sulla versione di System Center 2012 - Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
- Esaminare le [Note sulla versione di Data Protection Manager in System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

<!---HONumber=AcomDC_0608_2016-->