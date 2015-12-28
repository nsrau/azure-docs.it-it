<properties
	pageTitle="Farm di SharePoint Server 2013 fase 5 | Microsoft Azure"
	description="Creare un gruppo di disponibilità e aggiungervi i database di SharePoint nella fase 5 della farm di SharePoint Server 2013 in Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Carico di lavoro di farm intranet di SharePoint Fase 5: Creare il gruppo di disponibilità e aggiungere i database SharePoint.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

In questa fase finale della distribuzione di una farm di SharePoint 2013 solo intranet con i gruppi di disponibilità AlwaysOn di SQL Server nei servizi di infrastruttura di Azure, si crea un nuovo gruppo di disponibilità AlwaysOn e si aggiungono i database della farm di SharePoint.

Per conoscere tutte le fasi, vedere [Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Creare il gruppo di disponibilità e aggiungere i database

SharePoint consente di creare diversi database nell'ambito della configurazione iniziale. Tali database devono essere preparati attenendosi ai seguenti passaggi:

1.	Eseguire un backup completo e un backup del log delle transazioni del database nella macchina principale.
2.	Ripristinare l'intero backup del log nel computer di backup.

Una volta che i database sono stati sottoposti a backup e ripristino, possono essere aggiunti al gruppo di disponibilità. SQL Server consente solo ai database, che sono stati sottoposti a backup (almeno una volta) e ripristinati in un altro computer, di far parte del gruppo.

### Condividere le cartelle di backup

Per abilitare il backup e il ripristino, assicurarsi che i file di backup (.bak) siano accessibili dalla macchina virtuale del server SQL secondario. Utilizzare la procedura seguente:

1.	Accedere al computer host SQL Server principale come **[domain]\\sp\_farm\_db**.
2.	Passare al disco F:\\.
3.	Fare click con il pulsante destro del mouse sulla cartella **Backup**, quindi scegliere **Condividi con** e fare clic su **Utenti specifici**.
4.	Nella finestra di dialogo **condivisione File** digitare **[domain]\\sqlservice**, quindi fare clic su **Aggiungi**.
5.	Scegliere la colonna **Livello di autorizzazione** relativa al nome account **sqlservice**, quindi fare clic su **Lettura/scrittura**.
6.	Fare clic su **Condividi** e quindi su **Operazione completata**.

Eseguire la procedura sopra riportata nell'host SQL Server secondario, ad eccezione del fatto che all'account sqlservice viene assegnata l'autorizzazione in **lettura** per la cartella F:\\Backup creata nella fase 5.

### Eseguire il backup e ripristinare un database

Le procedure seguenti devono essere ripetute per ogni database che deve essere aggiunto al gruppo di disponibilità. Alcuni database di SharePoint 2013 non supportano i gruppi di disponibilità AlwaysOn di SQL Server. Per ulteriori informazioni, vedere [Opzioni di disponibilità elevata e di ripristino di emergenza supportate per i database SharePoint](http://technet.microsoft.com/library/jj841106.aspx).

Utilizzare questi passaggi per il backup del database:

1.	Dalla schermata Start del computer SQL Server principale, digitare**SQL Studio** quindi fare clic su **SQL Server Management Studio**.
2.	Fare clic su **Connect**.
3.	Nel riquadro sinistro, espandere il nodo **Database**.
4.	Fare doppio clic su un database per eseguire il backup, scegliere **Attività**, quindi fare clic su **Backup**.
5.	Nella **Destinazione** fare clic su **Rimuovi** per rimuovere il percorso predefinito per il file di backup.
6.	Fare clic su **Aggiungi**. In **Nome File** digitare **\\ [machineName]\\backup[databaseName].bak**, dove machineName è il nome del computer SQL server principale e databaseName è il nome del database. Fare clic su **OK**, quindi fare clic su **OK** dopo che è stato visualizzato il messaggio relativo al completamento del backup.
7.	Nel riquadro sinistro,fare clic con il pulsante del mouse destro ** [databaseName]**, scegliere **Attività**, quindi fare clic su **Backup**.
8.	In **Tipo di Backup**, selezionare **Log delle transazioni**, quindi fare clic su **OK** per due volte.
9.	Mantenere aperta la sessione Desktop remoto.

Utilizzare questi passaggi per il ripristino del database:

1.	Accedere al computer SQL Server secondario come **[domainName]\\sp\_farm\_db**.
2.	Nella schermata Start digitare **SQL Studio**, quindi fare clic su **SQL Server Management Studio**.
3.	Fare clic su **Connect**.
4.	Nel riquadro sinistro, fare clic con il pulsante destro del mouse su **Database**, quindi fare clic su **Ripristina database**.
5.	Nella sezione **Origine** selezionare **Dispositivo** e fare clic sul pulsante dei puntini di sospensione (...)
6.	In **Seleziona dispositivi di backup**, fare clic su **Aggiungi**.
7.	In **Percorso del file di Backup**, digitare **\\[machineName]\\backup**, premere Invio, selezionare **[databaseName].bak**, e poi fare clic su **OK** per due volte. A questo punto dovrebbero essere visualizzati il backup completo e il backup del log nella sezione **Set di Backup da ripristinare**.
8.	In **Selezione pagina**, fare clic su **Opzioni**. Nella sezione **Opzioni di ripristino** in **Stato di recupero**, selezionare **RESTORE WITH NORECOVERY**, quindi fare clic su **OK**.
9.	Quando richiesto, fare clic su **OK**.

### Creare un set di disponibilità

Dopo che è stato preparato almeno un database (tramite il metodo di backup e ripristino), è possibile creare un gruppo di disponibilità.

1.	Tornare alla sessione Desktop remoto del computer SQL Server principale.
2.	In **SQL Server Management Studio**, nel riquadro sinistro, fare clic con il pulsante destro del mouse su **Disponibilità elevata AlwaysOn**, quindi fare clic su **Creazione guidata nuovo Gruppo di disponibilità**.
3.	Nella pagina **Introduzione**, fare clic su **Avanti**.
4.	Nella pagina **Specifica nome del gruppo di disponibilità**, digitare il nome del gruppo di disponibilità in **Nome gruppo di disponibilità** (esempio: AG1), quindi fare clic su **Avanti**.
5.	Nella pagina **Selezionare database**, selezionare i database della farm di SharePoint di cui è stato eseguito il backup e fare clic su **Avanti**. Questi database soddisfano i prerequisiti per un gruppo di disponibilità in quanto è stato eseguito almeno un backup completo nella replica principale utilizzata.
6.	Nella pagina **Specifica repliche**, fare clic su **Aggiungi replica**.
7.	In **Connetti al Server**, digitare il nome del computer SQL Server secondario, quindi fare clic su **Connetti**.
8.	Nella pagina **Specifica repliche** l'host SQL Server secondario è elencato in **Repliche di disponibilità**. Per entrambe le istanze, impostare i valori delle opzioni seguenti:

Ruolo iniziale | Opzione | Valore
--- | --- | ---
Primaria | Failover automatico (fino a 2) | Selezionato
Secondaria | Failover automatico (fino a 2) | Selezionato
Primaria | Commit sincrono (fino a 3) | Selezionato
Secondaria | Commit sincrono (fino a 3) | Selezionato
Primaria | Secondario leggibile | Sì
Secondaria | Secondario leggibile | Sì

9.	Fare clic su **Avanti**.  
10.	Nella pagina **Seleziona sincronizzazione dati iniziale**, fare clic su **Solo join**, quindi su **Avanti**. La sincronizzazione dei dati viene eseguita manualmente eseguendo i backup completi e delle transazioni nel server principale e ripristinandola nel backup. È possibile scegliere di selezionare **Completo** per permettere la sincronizzazione dei dati automatica. Tuttavia, la sincronizzazione non è consigliata per database di grandi dimensioni presenti in alcune organizzazioni.  
11.	Nella pagina di **Conferma**, fare clic su **Avanti**. Poiché non è configurato un listener del gruppo di disponibilità viene visualizzato un avviso in cui viene indicata la mancanza di un listener.
12.	Nella pagina **Riepilogo** fare clic su **Fine**. Al termine della procedura guidata, controllare la pagina **Risultati**per verificare che il gruppo di disponibilità sia stato creato correttamente. In tal caso, fare clic su **Chiudi** per uscire dalla procedura guidata.
13.	Dalla schermata Start digitare **Failover**, quindi fare clic su **Gestione cluster di failover**. Nel riquadro sinistro, aprire il nome del cluster e fare clic su **Ruoli**. Un nuovo ruolo con il nome del gruppo di disponibilità deve essere presente.  

È stato configurato correttamente un gruppo di disponibilità AlwaysOn di SQL Server per i database di SharePoint.

## Configurare un listener per il gruppo di disponibilità AlwaysOn

Facoltativamente, è possibile creare una configurazione del listener per il gruppo di disponibilità AlwaysOn. Per la procedura vedere [Esercitazione: Configurazione del listener per i gruppi di disponibilità AlwaysOn](https://msdn.microsoft.com/library/dn425027.aspx). È necessario creare solo un singolo listener e configurarlo affinché utilizzi un indirizzo IP virtuale di un'istanza di bilanciamento del carico interno.

Una volta creato il listener, è necessario configurare tutte le macchine virtuali di SharePoint per l'utilizzo del listener, anziché il nome del primo server del cluster SQL. Anziché utilizzare i nomi, configurare le macchine virtuali di SharePoint per l'utilizzo di un alias SQL. Per informazioni dettagliate e procedure, vedere [Alias SQL per SharePoint](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx).

Per ulteriori informazioni su SharePoint con gruppi di disponibilità di SQL Server AlwaysOn, vedere [Configurare gruppi di disponibilità AlwaysOn di SQL Server 2012 per SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).


## Passaggio successivo

- [Configurare](https://technet.microsoft.com/library/ee836142.aspx) la farm di SharePoint.

<!---HONumber=AcomDC_1217_2015-->