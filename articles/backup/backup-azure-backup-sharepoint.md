<properties
	pageTitle="Protezione DPM di una farm di SharePoint in Azure | Microsoft Azure"
	description="In questo articolo viene fornita una panoramica della protezione DPM di una farm di SharePoint in Azure"
	services="backup"
	documentationCenter=""
	authors="SamirMehta"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/14/2015" ms.author="sammehta"; "jimpark"/>


# Backup di una farm di SharePoint in Azure
Eseguire il backup di una farm di SharePoint in Azure mediante System Center Data Protection Manager (DPM) in modo pressoché analogo a quanto avviene per altre origini dati. Azure offre flessibilità nella pianificazione di backup per creare intervalli di backup quotidiani, settimanali, mensili o annuali e offre diverse opzioni in termini di criteri di conservazione per i vari intervalli di backup. DPM fornisce la capacità di archiviare copie disco locali per obiettivi di tempi di ripristino (RTO) più rapidi e in Azure per una destinazione di conservazione a lungo termine ed economicamente conveniente.

## Versioni supportate di SharePoint e relativi scenari di protezione
Azure Backup per DPM supporta gli scenari seguenti.

| Carico di lavoro | Versione | Distribuzione di SharePoint | Tipo di distribuzione di DPM | DPM - System Center 2012 R2 | Protezione e ripristino |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| SharePoint | SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 | SharePoint distribuito come un server fisico o macchina virtuale Hyper-V o VmWare<br> -------------- <br>SQL AlwaysOn | Server fisico o macchina virtuale Hyper-V in locale | Supporta backup di Azure dall'aggiornamento cumulativo 5 | Proteggere il ripristino della farm di SharePoint: farm, database, file o voce di elenco dalla farm su disco e dal ripristino e il database da Azure |

## Prima di iniziare
Esistono alcune cose che è necessario verificare prima di effettuare il backup di una farm di SharePoint in Azure.

### Prerequisiti
Prima di procedere, assicurarsi che tutti i [Prerequisiti ](backup-azure-dpm-introduction.md#prerequisites) per l'utilizzo di Microsoft Azure Backup per proteggere i carichi di lavoro siano stati soddisfatti. I prerequisiti si applicano ad attività, ad esempio: la creazione di un archivio di backup, il download delle credenziali dell'insieme di credenziali, l'installazione dell'agente di Backup di Azure e la registrazione del server con l'insieme di credenziali.

### Agente DPM
Nel server SharePoint, SQL Server e qualsiasi altro server che faccia parte della farm di SharePoint, è necessario installare l'agente DPM. Per ulteriori informazioni sulla configurazione dell'agente di protezione, vedere [Configurazione dell'agente di protezione](https://technet.microsoft.com/library/hh758039.aspx). L'unica eccezione è solo quella di installare l'agente in un singolo server Web front-End (WFE). Per DPM è necessario solo l'agente in un server front-end Web che funga da punto di ingresso per la protezione.

### Una farm di SharePoint
Per ogni 10 milioni di elementi nella farm, deve essere almeno disponibili 2 GB di spazio nel volume in cui si trova la cartella di DPM. Questo spazio è necessario per la generazione del catalogo. Per DPM ripristinare elementi specifici (raccolte siti, siti, elenchi, raccolte documenti, cartelle, singoli documenti e gli elementi dell'elenco), la generazione del catalogo consente di creare un elenco degli URL inclusi all'interno di ogni database dei contenuti. Nel pannello elementi ripristinabili nell'area relativa alle attività di ripristino della Console di amministrazione DPM, è possibile visualizzare l'elenco di URL.

### SQL Server
DPM viene eseguito con l'account di sistema locale e per eseguire il backup dei database di SQL Server richiede privilegi di amministratore di sistema sull'account per SQL server. Impostare NT AUTHORITY\\SYSTEM su *amministratore di sistema* nel sistema SQL Server di cui si desidera eseguire il backup.

Nella farm di SharePoint, se si dispone di database di SQL Server configurati con gli alias di SQL Server, installare i componenti client di SQL Server nel server Web front-end protetto da DPM.

### SharePoint Server
Mentre le prestazioni dipendono da molti fattori quali la dimensione della farm di SharePoint, in linea generale un Server DPM è utilizzabile per proteggere una farm di SharePoint da 25 TB.

### Aggiornamento cumulativo 5 di DPM
Per iniziare a proteggere una farm di SharePoint in Azure, è necessario installare DPM aggiornamento cumulativo 5 o versione successiva. L'aggiornamento cumulativo 5 offre la possibilità di proteggere una farm di SharePoint (configurata con SQL AlwaysOn) in Azure. Per ulteriori informazioni vedere le informazioni relative all'[l'installazione di DPM aggiornamento cumulativo 5](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### Cosa non è supportato
1. La protezione di una farm di SharePoint non consente di proteggere gli indici di ricerca o i database del servizio di applicazione. Occorre configurare separatamente la protezione di questi database.
2. DPM non supporta il backup di database SQL SharePoint ospitati in condivisioni di file server di scalabilità orizzontale.

## Configurare la protezione di SharePoint
È necessario configurare il servizio Writer VSS di SharePoint (servizio WSS Writer) mediante **ConfigureSharePoint.exe** prima di poter proteggere SharePoint con DPM.

È possibile trovare **ConfigureSharePoint.exe** nella cartella \\bin [percorso di installazione di DPM] sul server Web front-end. Questo strumento fornisce all'agente protezione le credenziali per la farm di SharePoint. È possibile eseguirlo in un unico server front-end Web. Se si dispone di più server front-end Web, selezionarne solo uno quando si configura un gruppo protezione dati.

### Per configurare il servizio Writer VSS di SharePoint
1. Nel server WFE, al prompt dei comandi, passare a \\bin\\ [percorso di installazione di DPM]
2. Eseguire ConfigureSharePoint -EnableSharePointProtection
3. Immettere le credenziali di amministratore della farm. Questo account deve essere un membro del gruppo degli amministratori locale nel server front-end Web. Se l'amministratore della farm non è un amministratore locale, concedere le autorizzazioni seguenti sul server front-end Web:
  - Concedere il controllo completo del gruppo WSS\_Admin\_WPG sulla cartella DPM (% Program Files%\\Microsoft Data Protection Manager\\DPM).
  - Concedere l'accesso in lettura al gruppo WSS\_Admin\_WPG per la chiave del Registro di sistema di DPM (HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft Data Protection Manager).

>[AZURE.NOTE]È necessario eseguire di nuovo ConfigureSharePoint.exe ogni volta che si verifica un cambiamento nelle credenziali di amministratore di farm SharePoint.

## Eseguire il backup di una farm di SharePoint con DPM
Dopo aver configurato DPM e la farm di SharePoint come descritto in precedenza, SharePoint può essere protetto da DPM.

### Per proteggere una farm di SharePoint
1. Dalla scheda **Protezione** della Console amministrazione DPM fare clic su **Nuovo**.![Nuova scheda Protezione](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)

2. Nella schermata **Selezione tipo di gruppo di protezione** della procedura guidata **Crea nuovo gruppo protezione dati**, selezionare **Server** e fare clic su **Avanti**.

    ![Seleziona tipo di gruppo di protezione dati](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)

3. Nella schermata **Seleziona membri del gruppo**, selezionare la casella di controllo per il server di SharePoint che si desidera proteggere e fare clic su **Avanti**

    ![Seleziona membri del gruppo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

    >[AZURE.NOTE]Con l'agente DPM installato, è possibile visualizzare il server della procedura guidata. DPM mostra anche la propria struttura. Poiché è stato eseguito ConfigureSharePoint.exe, DPM comunica con il Writer VSS SharePoint e relativi database SQL corrispondenti e riconosce la struttura di farm di SharePoint (i database del contenuto associati e tutti gli elementi corrispondenti).

4. Nella schermata **Seleziona metodo protezione dati** immettere il nome del *Gruppo protezione dati* e selezionare i *metodi di protezione* preferiti. Fare clic su **Avanti**.

    ![Seleziona metodo protezione dati](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

    >[AZURE.NOTE]La scelta della protezione disco consente di soddisfare gli obiettivi di tempo di ripristino brevi. Azure è una destinazione di protezione a lungo termine conveniente rispetto ai nastri. Per altre informazioni, vedere questo [articolo](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/).

5. Nella schermata **Specifica obiettivi a breve termine** selezionare il *periodo di mantenimento* preferito e identificare quando si desidera la frequenza di backup.

    ![Specifica obiettivi a breve termine](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

    >[AZURE.NOTE]Poiché il ripristino è spesso richiesto la maggior parte delle volte per i dati con data di creazione inferiore a cinque giorni, viene selezionato un periodo di mantenimento di 5 giorni su disco e viene garantito che il backup si verifica durante le ore non di produzione, per questo esempio.

6. Esaminare lo spazio su disco del pool archiviazione allocato per il gruppo protezione dati e fare clic su **Avanti**.

7. Per ogni gruppo di protezione dati DPM alloca spazio su disco per archiviare e gestire le repliche. A questo punto, DPM deve creare una copia dei dati selezionati. Selezionare come e quando si desidera che la replica venga creata e fare clic su **Avanti**.

    ![Scelta del metodo per la creazione della replica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

    >[AZURE.NOTE]Per assicurarsi che il traffico di rete non venga interessato, selezionare un'ora fuori dall'orario di produzione.

8. DPM garantisce l'integrità dei dati mediante l'esecuzione di verifiche della coerenza sulla replica. Sono disponibili due opzioni: È possibile definire una pianificazione per l'esecuzione di verifiche della coerenza o consentire a DPM di eseguire verifiche della coerenza automaticamente sulla replica ogni volta che si presentano incoerenze. Selezionare l'opzione desiderata e fare clic su**Avanti**.

    ![Verifica coerenza](./media/backup-azure-backup-sharepoint/consistency-check.png)

9. Nella schermata **Specifica i dati da proteggere online**, selezionare la farm di SharePoint che si desidera proteggere e fare clic su**Avanti**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)

10. Nella schermata di **indicazione pi anificazione di Backup in linea**, selezionare la pianificazione preferita e fare clic su**Avanti**.

    ![Online\_backup\_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    >[AZURE.NOTE]DPM consente di eseguire 2 backup giornalieri in momenti diversi in Azure.

11. In base alla pianificazione di backup selezionata, scegliere la schermata per ** specificare i criteri di conservazione Online**, selezionare i criteri di conservazione per i punti di backup giornalieri, settimanali, mensili e annuali.

    ![Online\_retention\_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    >[AZURE.NOTE]DPM utilizza uno schema di conservazione GFS (Grandfather-Father-Son, nonno-padre-figlio) in cui è possibile scegliere criteri di conservazione diversi per diversi punti di backup.

12. Analogamente al disco, è necessario creare una replica del punto di riferimento iniziale in Azure. Selezionare l'opzione preferita per creare la copia di backup iniziale in Azure e fare clic su **Avanti**.

    ![Online\_replica](./media/backup-azure-backup-sharepoint/online-replication.png)

13. Esaminare le impostazioni selezionate nella pagina di **riepilogo** e fare clic su**Crea gruppo**. Dopo aver creato il gruppo protezione dati, verrà visualizzato un messaggio di corretto completamento.

    ![Riepilogo](./media/backup-azure-backup-sharepoint/summary.png)

## Ripristinare un elemento di SharePoint dal disco mediante DPM
Nell'esempio seguente, l'*elemento ripristino SharePoint* è stato accidentalmente eliminato e deve essere ripristinato. ![DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Aprire la **Console amministrazione DPM**. Tutte le farm di SharePoint protette da DPM vengono visualizzate nella scheda protezione.

    ![DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)

2. Per iniziare a ripristinare l'elemento, selezionare la scheda di **ripristino**.

    ![DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)

3. È possibile eseguire ricerche in SharePoint relative al *elemento ripristino SharePoint*mediante una ricerca con caratteri jolly in base all'interno di un intervallo di punti di ripristino.

    ![DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)

4. Selezionare il punto di ripristino appropriato dai risultati della ricerca, fare clic con il pulsante destro del mouse e selezionare **Ripristinar**

5. È inoltre possibile spostarsi tra vari punti di ripristino e selezionare un database o un elemento da recuperare. Selezionare **Data > Tempo di recupero**, quindi selezionare il **database corretto > Farm di SharePoint > Punto di ripristino > Elemento**.

    ![DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)

6. Fare clic con il pulsante destro del mouse sull'elemento e selezionare **Ripristina **per aprire il **Ripristino guidato**. Fare clic su **Avanti**.

    ![Verifica selezione per ripristino](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)

7. Selezionare il tipo di ripristino che si desidera eseguire e fare clic su**Avanti**.

    ![Tipo di ripristino](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

    >[AZURE.NOTE]Esempio sopra riportato mostra l'elemento di ripristino nel sito di SharePoint originale.

8. Selezionare il **processo di ripristino** che si desidera utilizzare.
    - Selezionare **Ripristina senza una farm di ripristino** se la farm di SharePoint non è stata modificata e corrisponde al punto di ripristino ripristinato.
    - Selezionare**ripristinare utilizzando una farm di ripristino**se la farm di SharePoint è stato modificato dopo il punto di ripristino è stato creato.

    ![Processo di ripristino](./media/backup-azure-backup-sharepoint/recovery-process.png)

9. Forniscono una posizione di istanza SQL per recuperare il database temporaneamente e una condivisione di file di gestione temporanea nel Server DPM e SharePoint server per ripristinare l'elemento.

    ![Staging Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    Data Protection Manager collega il database del contenuto, che include l'elemento di SharePoint per l'istanza di SQL temporanea. Dal database del contenuto del Server DPM consente di recuperare l'elemento e lo inserisce nel percorso di file di gestione temporanea nel server DPM. L'elemento recuperato al percorso di gestione temporanea, nel server DPM deve ora essere esportati nel percorso di gestione temporaneo della farm di SharePoint.

    ![Gestione temporanea Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)

10. Selezionare **Specifica opzioni di ripristino** e applicare le impostazioni di sicurezza per la farm di SharePoint o applicare le impostazioni di sicurezza del punto di ripristino. Fare clic su **Avanti**.

    ![Opzioni di ripristino](./media/backup-azure-backup-sharepoint/recovery-options.png)

    >[AZURE.NOTE]È possibile scegliere di limitare l'utilizzo della larghezza di banda di rete. Consente di ridurre l'impatto sul server di produzione durante l'orario di produzione.

11. Esaminare le informazioni di riepilogo e fare clic su **Ripristina** per avviare il ripristino del file.

    ![Riepilogo di ripristino](./media/backup-azure-backup-sharepoint/recovery-summary.png)

12. Selezionare la scheda **Monitoraggio** nella scheda **Console amministrazione DPM** per visualizzare lo *stato* del ripristino.

    ![Stato di ripristino](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    >[AZURE.NOTE]A questo punto è possibile ripristinare il file. È possibile aggiornare il sito di SharePoint per controllare il file ripristinato.

## Ripristinare un database di SharePoint da Azure mediante DPM

1. Per ripristinare un database del contenuto di SharePoint, spostarsi tra vari punti di ripristino (come illustrato in precedenza) e selezionare il punto di ripristino che si desidera ripristinare.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)

2. Fare doppio clic sul punto di ripristino di SharePoint per visualizzare le informazioni del catalogo di SharePoint disponibili.

    > [AZURE.NOTE]Poiché la farm di SharePoint è protetta per la conservazione a lungo termine in Azure, nessuna informazione di catalogo (metadati) è disponibile nel Server DPM. Di conseguenza, ogni volta che sia necessario un ripristino temporizzato di un database di contenuti di SharePoint momento occorre ricatalogazione la farm di SharePoint.

3. Fare clic su **Ricatalogazione**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Viene visualizzata la finestra di stato di **ricatalogazione cloud**.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Una volta completata la catalogazione, viene visualizzato lo stato *Esito positivo*. Fare clic su **Close**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)

4. Fare clic sull'oggetto SharePoint illustrato nella scheda **Ripristino** in DPM per ottenere la struttura del database del contenuto. Fare clic con il pulsante destro del mouse su >**Ripristina**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)

5. A questo punto, seguire [i passaggi indicati sopra](#restore-a-sharepoint-item-from-disk-using-dpm) per il ripristino di un database del contenuto di Sharepoint dal disco.

## Domande frequenti
D: Quali versioni di DPM supportano SQL 2014 e SQL 2012 (SP2)<br> R: DPM 2012 R2 con aggiornamento cumulativo 4 supporta le stesse

D: È possibile ripristinare elementi di SharePoint nel percorso originale se SharePoint è configurato con SQL AlwaysOn (con protezione dati su disco)?<br> R: Sì consente di ripristinare l'elemento sul sito di SharePoint originale

D: È possibile ripristinare un database di SharePoint nel percorso originale se SharePoint è configurato con SQL AlwaysOn?<br> R: Poiché i database di SharePoint sono configurati in SQL AlwaysOn, non possono essere modificati a meno che non venga rimosso il gruppo di disponibilità (AG). DPM di conseguenza non consente il ripristino del database nel percorso originale. È possibile ripristinare un database SQL in un'altra istanza SQL.

## Passaggi successivi
- Ulteriori informazioni su Protezione DPM di SharePoint, vedere [Serie di Video - Protezione DPM di SharePoint](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
- Esaminare le [Note sulla versione di System Center 2012 - Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
- Esaminare le [Note sulla versione di Data Protection Manager in System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

<!---HONumber=August15_HO6-->