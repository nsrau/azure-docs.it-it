<properties
	pageTitle="Configurare la protezione tra un sito di Hyper-V locale e Azure" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali ubicate nei server Hyper-V locali in Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="06/08/2015" 
	ms.author="raynew"/>


# Configurare la protezione tra un sito di Hyper-V locale e Azure


## Panoramica

Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. Per informazioni sui possibili scenari di distribuzione consultare [Panoramica di Site Recovery](site-recovery-overview.md).

In questo articolo viene descritto come distribuire Site Recovery per replicare le macchine virtuali che si trovano nei server Hyper-V locali che eseguono Windows Server 2012 R2. La replica dell’archiviazione di Azure viene gestita da Site Recovery. Questa distribuzione è particolarmente utile se si eseguono server Hyper-V senza che sia stato distribuito System Center Virtual Machine Manager \(VMM\).


## Informazioni sull’articolo

Nell'articolo sono riepilogati i prerequisiti di distribuzione, sono forniti consigli per la configurazione delle impostazioni di replica e l’abilitazione della protezione per le macchine virtuali. Si conclude con il test del failover, per accertarsi che tutti gli elementi funzionino come previsto.

Nel caso di problemi, inviare domande al [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Prima di iniziare

Prima di iniziare l'esercitazione, accertarsi che i seguenti prerequisiti siano soddisfatti.

### Prerequisiti di Azure

- È necessario un account [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](pricing/free-trial/).
- - Per archiviare i dati replicati, sarà necessario un account di archiviazione di Azure. Nell'account deve essere abilitata la replica geografica. Dovrà trovarsi nella stessa area dell'insieme di credenziali di Azure Site Recovery ed essere associato alla stessa sottoscrizione. Per altre informazioni, vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md).
- - È necessaria una rete virtuale di Azure in modo che le macchine virtuali replicate siano connesse a una rete dopo il failover.

## Prerequisiti di Hyper-V

- Nel sito locale di origine è necessario almeno un server che esegue Windows Server 2012 R2 con il ruolo di Hyper-V.
- Il server Hyper-V deve contenere una o più macchine virtuali
- I server Hyper-V devono essere connessi a Internet, in modo diretto o tramite proxy.

### Prerequisiti delle macchine virtuali

Le macchine da proteggere devono essere conformi ai [prerequisiti di Azure](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A).

### Prerequisiti di provider e agente

Nell’ambito della distribuzione di Azure Site Recovery verranno installati Provider e agente di Azure Site Recovery in ogni server Hyper-V in cui sono in esecuzione macchine virtuali che si desidera proteggere. Si noti che:

- È necessario eseguire le versioni più recenti di Provider e agente.
- Tutti i server Hyper-V di un insieme devono essere della stessa versione.
- Il Provider dovrà potersi connettere ad Azure Site Recovery tramite Internet. È possibile scegliere di eseguire questa operazione senza un proxy, utilizzando le impostazioni proxy configurate nel server VMM oppure utilizzando le impostazioni proxy personalizzate configurate durante l'installazione del Provider. Per utilizzare un server proxy esistente, verificare che gli URL per la connessione ad Azure siano consentiti attraverso il firewall:
	- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net 
- Per utilizzare un proxy personalizzato, configurare il server proxy prima di installare il Provider. Durante l'installazione del Provider, è necessario specificare l'indirizzo e la porta del server proxy e le credenziali che possono essere utilizzate per l'accesso.

L'immagine seguente illustra i diversi canali e porte di comunicazione usati da Azure Site Recovery per la replica e l'orchestrazione

![Topologia B2A](./media/site-recovery-hyper-v-site-to-azure/B2ATopology.png)

 
## Passaggio 1: Creare un insieme di credenziali

1. Accedere al [portale di gestione](https://portal.azure.com).


2. Espandere **Servizi dati** \> **Servizi di ripristino** e fare clic su **Insieme di credenziali di Site Recovery**.


3. Fare clic su **Crea nuovo** \> **Creazione rapida**.

4. In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In **Region** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate, vedere la sezione Disponibilità a livello geografico in [Prezzi di Azure Site Recovery](pricing/details/site-recovery/).

6. Fare clic su **Create vault**.

	![Nuovo insieme di credenziali](./media/site-recovery-hyper-v-site-to-azure/SR_HvVault.png)

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di Servizi di ripristino.


## Passaggio 2: Creare un sito Hyper-V

1. Nella pagina Servizi di ripristino fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Avvio rapido](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_QuickStartIcon.png)

2. Dall'elenco a discesa selezionare **Tra un sito Hyper-V locale e Azure**.

	![Scenario del sito di Hyper-V](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectScenario.png)

3. In **Crea un sito Hyper-V** fare clic su **Crea sito Hyper-V**. Specificare un nome per il sito e salvare.

	![Sito di Hyper-V](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateSite2.png)


## Passaggio 3: Installare Provider e agente
Installare provider e agente. Se si desidera eseguire l’installazione in un cluster Hyper-V, eseguire i passaggi da 5 a 11 in ogni nodo del cluster di failover. Una volta registrati tutti i nodi e abilitata la protezione, le macchine virtuali saranno protette anche se migrano tra i nodi del cluster di failover.

1. In **Prepara server Hyper-V**, fare clic sul file **Scarica codice di registrazione**.
2. Nella pagina **Scarica codice di registrazione** fare clic su **Scarica** accanto al nome del sito. Scaricare il codice in un percorso sicuro a cui sia possibile accedere facilmente tramite il server Hyper-V. Il codice è valido per 5 giorni dal momento in cui viene generato.

	![Registration key](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_DownloadKey2.png)

4. Fare clic su **Scaricare il Provider** per ottenere la versione più recente.
5. Eseguire il file su ciascun server Hyper-V da registrare nell'insieme di credenziali. Il file installa due componenti:
	- **Provider di Azure Site Recovery**: gestisce le comunicazioni e l’orchestrazione tra il server Hyper-V e il portale di Azure Site Recovery. 
	- **Agente di Servizi di ripristino di Azure**: gestisce il trasferimento dei dati tra le macchine virtuali in esecuzione sul server Hyper-V di origine e l'archiviazione di Azure. 
6. In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider e dell'agente verranno installati in base ai criteri indicati in Microsoft Update.

	![Microsoft Updates](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider1.png)

7. In **Installazione** specificare la posizione di installazione di Provider e agente nel server Hyper-V.

	![Posizione di installazione](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider2.png)

8. Al termine dell'installazione, proseguire con la configurazione per registrare il server nell'insieme di credenziali.

	![Installazione completata](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider3.png)


9. Nella pagina **Connessione Internet** specificare la modalità di connessione tra Provider e Azure Site Recovery. Selezionare **Usa impostazioni proxy del sistema predefinite** per usare le impostazioni di connessione a Internet predefinite configurate nel server. Se non si specifica un valore, verranno utilizzate le impostazioni predefinite.

	![Internet Settings](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider4.png)

	Si noti che:

	- Se il proxy predefinito sul server Hyper-V richiede l'autenticazione, selezionare l’opzione per l’uso di un server proxy personalizzato. Digitare i dettagli del proxy predefinito e specificare le credenziali.
	- Se si vuole usare un server proxy personalizzato, configurarlo prima di installare il provider. 
	- Gli URL seguenti dovranno essere accessibili dall'host Hyper-V
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net 
	- Consentire gli indirizzi IP descritti in [Intervalli IP dei data center di Azure](http://go.microsoft.com/fwlink/?LinkId=511094) e il protocollo HTTPS \(443\). È necessario aggiungere all'elenco di indirizzi consentiti gli IP dell'area Azure che si prevede di utilizzare e quello degli Stati Uniti occidentali.

9. Nella pagina **Impostazioni insieme di credenziali** fare clic su **Sfoglia** per selezionare il file di chiave. Specificare la sottoscrizione di Azure Site Recovery, il nome dell'insieme di credenziali e il sito Hyper-V a cui appartiene il server Hyper-V.

	![Server registration](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectKey.png)


11. La procedura di registrazione ha inizio e il server viene registrato nell'insieme di credenziali.

	![Server registration](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider6.png)

11. Al termine della registrazione, i metadati del server Hyper-V vengono recuperati da Azure Site Recovery e il server viene visualizzato nella scheda **Siti Hyper-V** della pagina **Server** all'interno dell'insieme di credenziali.

	![Server registration](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider7.png)

Si noti che se si desidera installare il provider in Server Core per Windows Server 2012 R2 o server Hyper-V 2012 R2 autonomo, è necessario effettuare le seguenti operazioni:

1. Scaricare il file di installazione del Provider e il codice di registrazione.
2. Estrarre il programma di installazione di Provider digitando:

	    C:\Windows\System32> CD C:\Program Files\Azure Site Recovery Provider
	    C:\Program Files\Azure Site Recovery Provider>AzureSiteRecoveryProvider.exe /x:. /q

3. Installare Provider digitando:

	    C:\Program Files\Azure Site Recovery Provider> setupdr.exe /i

4. Registrare il server digitando:

	    C:\Program Files\Azure Site Recovery Provider> DRConfigurator.exe /r /Credentials <Location of key> /FriendlyName <Hyper-V host name>

	- /Credentials: parametro obbligatorio che specifica la posizione in cui si trova il file del codice di registrazione.
	- /FriendlyName: parametro obbligatorio per il nome del server host Hyper-V che viene visualizzato nel portale di Azure Site Recovery.
	- Parametri facoltativi:
		- /proxyAddress <address>: indirizzo del server proxy
		- /proxyport <port>: porta del server proxy
		- /proxyUsername <username>: le credenziali se il proxy richiede l'autenticazione.
		- proxyPassword <password>

## Passaggio 4: Creare risorse di Azure

1. Nella pagina **Prepara risorse**, selezionare **Crea account di archiviazione** per creare un account di archiviazione di Azure se non ne è ancora stato creato uno. Nell'account deve essere abilitata la replica geografica. Dovrà trovarsi nella stessa area dell'insieme di credenziali di Azure Site Recovery ed essere associato alla stessa sottoscrizione.

	![Create storage account](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateResources1.png)


## Passaggio 5: Creare e configurare gruppi di protezione

I gruppi di protezione dati sono raggruppamenti logici di macchine virtuali che si desidera proteggere utilizzando le stesse impostazioni di protezione. Se si applicano specifiche impostazioni di protezione a un gruppo, queste vengono automaticamente applicate a tutte le macchine virtuali aggiunte al gruppo. 1. In **Crea e configura gruppi di protezione** fare clic su **Crea gruppo di protezione dati**. Se non risultano soddisfatti tutti i requisiti, viene visualizzato un messaggio in cui è disponibile l'opzione **Visualizza dettagli** che consente di ottenere maggiori informazioni.

2. Nella scheda **Gruppo di protezione dati**, aggiungere un gruppo di protezione. Specificare un nome, il sito Hyper-V di origine, **Azure** di destinazione, il nome della propria sottoscrizione di Azure Site Recovery e l'account di archiviazione di Azure.

	![Gruppo protezione dati](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroupCreate3.png)


2. In **Impostazioni di replica** impostare la **Frequenza di copia** per specificare la frequenza di sincronizzazione dei dati differenziali tra origine e destinazione. È possibile impostare su 30 secondi, 5 minuti o 15 minuti.
3. In **Mantieni punti di ripristino** specificare il numero di ore della cronologia di ripristino da archiviare.
4. In **Frequenza di snapshot coerenti con l’applicazione** è possibile specificare se eseguire snapshot che utilizzano il servizio Copia Shadow del volume \(VSS\) per garantire che le applicazioni siano coerenti durante la creazione dello snapshot. Per impostazione predefinita questi snapshot non vengono eseguiti. Se si imposta un valore, questo deve essere inferiore al numero di punti di ripristino aggiuntivi configurati. Questa opzione è supportata solo se nella macchina virtuale è in esecuzione un sistema operativo Windows.
5. In **Ora di inizio replica iniziale** specificare quando la replica iniziale delle macchine virtuali nel gruppo di protezione deve essere inviata in Azure.

	![Gruppo protezione dati](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroup4.png)


## Passaggio 6: Abilitare la protezione delle macchine virtuali

Aggiungere macchine virtuali a un gruppo di protezione per abilitare la protezione.

1. Nella scheda **Macchine** del gruppo di protezione, fare clic\*\* su Aggiungi macchine virtuali a gruppi di protezione per abilitare la protezione.
2. Nella pagina **Abilita protezione macchine virtuali** selezionare le macchine virtuali da proteggere. 

	![Abilitare la protezione delle macchine virtuali](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_AddVM3.png)

	Vengono avviati i processi di abilitazione della protezione, Nella scheda **Processi** è possibile monitorare l’avanzamento. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover. 
3. Dopo la configurazione della protezione è possibile:

	- Visualizzare le macchine virtuali in **Elementi protetti** \> **Gruppi protezione dati** \> *nome\_gruppoprotezione* \> **Macchine virtuali** Nella scheda **Proprietà** è possibile eseguire il drill-down fino ai dettagli della macchina.
	- Configurare le proprietà di failover per una macchina virtuale in **Elementi protetti** \> **Gruppi protezione dati** \> *nome\_gruppodiprotezione* \> **Macchine virtuali** *nome\_macchina\_virtuale* \> **Configura**. È possibile configurare:
		- **Nome**: il nome della macchina virtuale in Azure.
		- **Dimensioni**: le dimensioni della macchina virtuale di destinazione che esegue il failover.

		![Configurare le proprietà della macchina virtuale](./media/site-recovery-hyper-v-site-to-azure/VMProperties.png)
	- Configurare altre impostazioni della macchina virtuale in *Elementi protetti*\* \> **Gruppi protezione dati** \> *nome\_gruppodiprotezione* \> **Macchine virtuali** *nome\_macchina\_virtuale* \> **Configura**:

		- **Schede di rete**: il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione. 
			- Large \(A3\) e A6: 2
			- Extralarge \(A4\) e A7:
			- A9: 2
			- D3: 2
			- D4: 4
			- D13: 4
			
			Quando si modifica la dimensione di una macchina virtuale e si salvano le impostazioni, alla successiva apertura della pagina **Configura** verranno visualizzate le schede di rete. Il numero di schede per una macchina virtuale verrà determinato come segue:


			- Se il numero di schede di rete nella macchina di origine è minore o uguale al numero di schede consentite per la macchina di destinazione, la destinazione avrà lo stesso numero di schede dell’origine.
			- Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà utilizzata la dimensione di destinazione massima.
			- Ad esempio, se una macchina di origine dispone di due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda. 	
		- **Rete di Azure**: specificare la rete in cui la macchina virtuale deve eseguire il failover. Se la macchina virtuale ha più schede di rete, tutte le schede devono essere connesse alla stessa rete di Azure.
		- **Subnet**: per ogni scheda di rete nella macchina virtuale, selezionare la subnet nella rete di Azure a cui deve connettersi la macchina dopo il failover.
		- **Indirizzo IP di destinazione**: se la scheda di rete della macchina virtuale di origine è configurata per utilizzare un indirizzo IP statico, è possibile specificare l'indirizzo IP per la macchina virtuale di destinazione per assicurarsi che la macchina abbia lo stesso indirizzo IP dopo il failover. Se non si specifica un indirizzo IP, al momento del failover verrà assegnato un qualsiasi indirizzo disponibile. Se si specifica un indirizzo in uso, il failover avrà esito negativo.
		 
		![Configurare le proprietà della macchina virtuale](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_VMMultipleNic.png)
	
## Passaggio 7: Creare un piano di ripristino

Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale o un piano di ripristino che contenga una o più macchine virtuali. Per creare un piano di ripristino, [eseguire le operazioni seguenti](site-recovery-create-recovery-plans.md):

## Passaggio 8: Testare la distribuzione

È possibile eseguire un failover di test in Azure in due modi.

- Failover di test senza una rete di Azure: questo tipo di failover di test verifica che la macchina virtuale sia rilevata correttamente in Azure. La macchina virtuale non sarà connessa ad alcuna rete di Azure dopo il failover.
- Failover di test con una rete di Azure: questo tipo di failover verifica che l'intero ambiente di replica venga rilevato come previsto e che le macchine virtuali di cui viene eseguito il failover siano connesse alla rete di Azure di destinazione specificata. Per la gestione della subnet, nel failover di test la subnet della macchina virtuale di test verrà rilevata in base alla subnet della macchina virtuale di replica. Questo comportamento è diverso dalla replica normale quando la subnet della macchina virtuale di replica si basa sulla subnet della macchina virtuale di origine.

Per eseguire un failover di test per una macchina virtuale abilitata per la protezione in Azure senza specificare una rete di Azure di destinazione, non occorre preparare l'ambiente. Per eseguire un failover di test con una rete di Azure di destinazione, è necessario creare una nuova rete di Azure isolata dalla rete di Azure di produzione \(comportamento predefinito quando si crea una nuova rete in Azure\) e configurare l'infrastruttura della macchina virtuale replicata in modo che funzioni come previsto. Ad esempio, una macchina virtuale con DNS e Controller di dominio può essere replicata in Azure utilizzando Azure Site Recovery e può essere creata nella rete di test utilizzando Failover di test. Per eseguire un failover di test, attenersi alla procedura seguente:


1. Eseguire un failover di test della macchina virtuale con Controller di dominio e DNS nella stessa rete che verrà usata per il failover di test effettivo della macchina virtuale locale.
2. Prendere nota degli indirizzi IP assegnati alla macchina virtuale DNS di failover.
3. Nella rete virtuale di Azure che verrà usata per il failover, aggiungere l’indirizzo IP come indirizzo del server DNS.
4. Eseguire il failover di test della macchina virtuale locale di origine, specificando la rete di test di Azure.
5. Dopo aver verificato che il test sia stato eseguito come previsto, contrassegnare il failover di test come completato per il piano di ripristino e quindi per le macchine virtuali di Controller di dominio e DNS.

Per eseguire un failover di test, eseguire le operazioni seguenti:

1. Nella scheda **Piani di ripristino** selezionare il piano e fare clic su **Failover di test**.
2. Nella pagina **Conferma failover di test** selezionare **Nessuno** o una rete di Azure specifica. Tenere presente che se si seleziona **Nessuno**, il failover di test verifica che la macchina virtuale venga replicata correttamente in Azure ma non controlla la configurazione della rete di replica.

	![Failover di test](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_TestFailoverNoNetwork.png)

3. Nella scheda **Processi** è possibile tenere traccia dello stato di avanzamento del failover. Dovrebbe anche essere possibile vedere la replica di test della macchina virtuale nel portale di Azure. Se è stato impostato l'accesso alle macchine virtuali dalla rete locale, è possibile inizializzare una Connessione Desktop remoto alla macchina virtuale.
4. Quando il processo di failover raggiunge la **fase conclusiva**, fare clic su **Completa** test per completare il failover di test. È possibile eseguire il drill-down fino alla scheda **Processo** per tenere traccia dello stato e dell’avanzamento del failover ed eseguire eventuali azioni necessarie.
5. Al termine del processo di failover, nel portale di Azure sarà possibile visualizzare la replica del test eseguito sulla macchina virtuale. Se è stato impostato l'accesso alle macchine virtuali dalla rete locale, è possibile inizializzare una Connessione Desktop remoto alla macchina virtuale.

	1. Verificare che le macchine virtuali vengano avviate correttamente.
    2. Per eseguire la connessione alla macchina virtuale in Azure tramite Desktop remoto dopo il failover, abilitare Connessione Desktop remoto sulla macchina virtuale prima di eseguire il failover di test. È necessario anche aggiungere un endpoint RDP nella macchina virtuale. A tale scopo, è possibile utilizzare un [Runbook di Automazione di Azure](site-recovery-runbook-automation.md).
    3. Dopo il failover, se si utilizza un indirizzo IP pubblico per connettersi alla macchina virtuale in Azure tramite Desktop remoto, verificare che non siano definiti criteri di dominio che impediscono la connessione a una macchina virtuale utilizzando un indirizzo pubblico.

6. Al completamento del test, procedere come segue:

	- Fare clic su **Il failover di test è completo**. Pulire l'ambiente di test per spegnere automaticamente ed eliminare la macchine virtuali di test.
	- Fare clic su **Note** per registrare e salvare eventuali commenti associati al failover di test.
7. Quando il processo di failover raggiunge la **fase conclusiva**, completare la verifica come segue:
	1. Visualizzare la macchina virtuale di replica nel portale di Azure. Verificare che la macchina virtuale venga avviata correttamente.
	2. Se è stato impostato l'accesso alle macchine virtuali dalla rete locale, è possibile inizializzare una Connessione Desktop remoto alla macchina virtuale.
	3. Fare clic su **Completa il test** per portarlo a termine.
	4. Fare clic su **Note** per registrare e salvare eventuali commenti associati al failover di test.
	5.  Fare clic su **Il failover di test è completo**. Pulire l'ambiente di test per spegnere automaticamente ed eliminare la macchina virtuale di test.

## Passaggi successivi

Dopo aver configurato correttamente la distribuzione, leggere [altre informazioni](site-recovery-failover.md) sul failover.

<!---HONumber=58_postMigration-->