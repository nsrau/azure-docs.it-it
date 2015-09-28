<properties 
	pageTitle="Applicazione line-of-business fase 5 | Microsoft Azure" 
	description="Creare un gruppo di disponibilità e aggiungere a tale gruppo i database dell'applicazione nella fase 5 dell'applicazione line-of-business di Azure." 
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
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Carico di lavoro dell'applicazione line-of-business - Fase 5: Creare il gruppo di disponibilità e aggiungere i database dell'applicazione

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione delle risorse con il modello di distribuzione di gestione delle risorse.

In questa fase finale della distribuzione di un'applicazione line-of-business a disponibilità elevata nei servizi di infrastruttura di Azure si crea un nuovo gruppo di disponibilità AlwaysOn di SQL Server e quindi si aggiungono i database dell'applicazione.

Per informazioni su tutte le fasi, vedere [Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

## Creare il gruppo di disponibilità e aggiungere i database

Per ogni database dell'applicazione line-of-business:

1.	Eseguire un backup completo e un backup del log delle transazioni del database nella macchina virtuale primaria di SQL Server.
2.	Ripristinare l'intero backup del log nella macchina virtuale di backup di SQL Server.

Dopo che i database sono stati sottoposti a backup e ripristino, possono essere aggiunti al gruppo di disponibilità. SQL Server consente solo ai database, che sono stati sottoposti a backup (almeno una volta) e ripristinati in un altro computer, di far parte del gruppo.

### Condividere le cartelle di backup

Per abilitare il backup e il ripristino, i file di backup (con estensione bak) devono essere accessibili da server di database secondario. Usare la procedura seguente:

1.	Accedere al server di database primario come **[domain]\\sqladmin**. 
2.	Passare al disco F:\\. 
3.	Fare con il pulsante destro del mouse sulla cartella **Backup**, quindi scegliere **Condividi con** e fare clic su **Utenti specifici**.
4.	Nella finestra di dialogo **Condivisione file** digitare **[domain]\\sqlservice**, quindi fare clic su **Aggiungi**.
5.	Scegliere la colonna **Livello di autorizzazione** relativa al nome account **sqlservice**, quindi fare clic su **Lettura/scrittura**. 
6.	Fare clic su **Condividi** e quindi su **Operazione completata**.

Eseguire la procedura sopra riportata nel server di database secondario, ad eccezione del fatto che all'account sqlservice viene assegnata l'autorizzazione di **Lettura** per la cartella F:\\Backup creata nella fase 5.

### Backup e ripristino di un database

Le procedure seguenti devono essere ripetute per ogni database che deve essere aggiunto al gruppo di disponibilità.

Usare questi passaggi per il backup del database.

1.	Dalla schermata Start del server di database primario digitare **SQL Studio**, quindi fare clic su **SQL Server Management Studio**.
2.	Fare clic su **Connect**.
3.	Nel riquadro sinistro, espandere il nodo **Database**.
4.	Fare doppio clic su un database per eseguire il backup, scegliere **Attività**, quindi fare clic su **Backup**.
5.	Nella **Destinazione** fare clic su **Rimuovi** per rimuovere il percorso predefinito per il file di backup.
6.	Fare clic su **Aggiungi**. In **Nome File** digitare **\\ [machineName]\\backup[databaseName].bak**, dove **machineName** è il nome del **computer SQL server** principale e **databaseName** è il nome del database. Fare clic su **OK**, quindi fare clic su **OK** dopo che è stato visualizzato il messaggio relativo al completamento del backup.
7.	Nel riquadro sinistro,fare clic con il pulsante del mouse destro ** [databaseName]**, scegliere **Attività**, quindi fare clic su **Backup**.
8.	In **Tipo di Backup**, selezionare **Log delle transazioni**, quindi fare clic su **OK** per due volte.
9.	Mantenere aperta la sessione Desktop remoto.

Usare questi passaggi per il ripristino del database.

1.	Accedere al server di database secondario come [domainName]\\sp\_farm\_db.
2.	Nella schermata Start digitare **SQL Studio**, quindi fare clic su **SQL Server Management Studio**.
3.	Fare clic su **Connect**.
4.	Nel riquadro sinistro, fare clic con il pulsante destro del mouse su **Database**, quindi fare clic su **Ripristina database**.
5.	Nella sezione **Origine** selezionare **Dispositivo** e fare clic sul pulsante dei puntini di sospensione (...)
6.	In **Seleziona dispositivi di backup**, fare clic su **Aggiungi**.
7.	In **Percorso del file di Backup**, digitare **\\[machineName]\\backup**, premere **Invio**, selezionare **[databaseName].bak**, e poi fare clic su **OK** per due volte. A questo punto dovrebbero essere visualizzati il backup completo e il backup del log nella sezione **Set di Backup da ripristinare**.
8.	In **Selezione pagina**, fare clic su **Opzioni**. Nella sezione **Opzioni di ripristino** in **Stato di recupero**, selezionare **RESTORE WITH NORECOVERY**, quindi fare clic su **OK**. 
9.	Quando richiesto, fare clic su **OK**.

### Creare un set di disponibilità

Dopo che è stato preparato almeno un database (tramite il metodo di backup e ripristino), è possibile creare un gruppo di disponibilità.

1.	Tornare alla sessione Desktop remoto per il server di database primario.
2.	In **SQL Server Management Studio**, nel riquadro sinistro, fare clic con il pulsante destro del mouse su **Disponibilità elevata AlwaysOn**, quindi fare clic su **Creazione guidata nuovo Gruppo di disponibilità**.
3.	Nella pagina **Introduzione**, fare clic su **Avanti**. 
4.	Nella pagina **Specifica nome del gruppo di disponibilità**, digitare il nome del gruppo di disponibilità in **Nome gruppo di disponibilità** (esempio: AG1), quindi fare clic su **Avanti**.
5.	Nella pagina **Selezione database** selezionare i database per l'applicazione di cui è stato eseguito il backup, quindi fare clic su **Avanti**. Questi database soddisfano i prerequisiti per un gruppo di disponibilità perché è stato eseguito almeno un backup completo nella replica principale usata.
6.	Nella pagina **Specifica repliche**, fare clic su **Aggiungi replica**.
7.	In **Connetti al Server** digitare il nome del server di database secondario, quindi fare clic su **Connetti**. 
8.	Nella pagina **Specifica repliche** il server di database secondario è elencato in **Repliche di disponibilità**. Per entrambe le istanze, impostare i valori delle opzioni seguenti: 

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

È stato configurato correttamente un gruppo di disponibilità AlwaysOn di SQL Server per i database dell'applicazione.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase5/workload-lobapp-phase4.png)

È ora possibile iniziare l'implementazione della nuova applicazione per gli utenti Intranet.

## Configurare un listener per il gruppo di disponibilità AlwaysOn

Facoltativamente, è possibile creare una configurazione del listener per il gruppo di disponibilità AlwaysOn. Per la procedura vedere [Esercitazione: Configurazione del listener per i gruppi di disponibilità AlwaysOn](https://msdn.microsoft.com/library/dn425027.aspx). Queste istruzioni illustrano in modo dettagliato la creazione di un singolo listener (scelta consigliata) e l'uso di un indirizzo IP statico per un'istanza del servizio di bilanciamento del carico interno.

Dopo la configurazione del listener, è necessario configurare tutte le macchine virtuali del server Web in modo che usino il listener, invece del nome del primo server SQL nel cluster. Invece di usare un nuovo nome DNS e il record per il mapping all'indirizzo IP virtuale dell'istanza del servizio di bilanciamento del carico interno, configurare le macchine virtuali del server Web in modo da usare un alias SQL. Per informazioni dettagliate e procedure, vedere [Alias SQL per SharePoint](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx).

## Risorse aggiuntive

[Distribuire un'applicazione line-of-business a disponibilità elevata in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Progetti dell'architettura per applicazioni line-of-business](http://msdn.microsoft.com/dn630664)

[Configurare un'applicazione LOB basata sul Web in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: farm di SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Sept15_HO3-->