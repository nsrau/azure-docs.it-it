<properties 
	pageTitle="Esercitazione: Configurare la protezione tra un sito Hyper-V locale e Azure" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali tra un sito Hyper-V locale e Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Esercitazione: Configurare la protezione tra un sito Hyper-V locale e Azure


<h2><a id="overview" name="overview" href="#overview"></a>Informazioni generali</h2>
<p>Azure Site Recovery favorisce la strategia di continuità del carico di lavoro e delle attività aziendali gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione.<p>

Questa esercitazione illustra come distribuire Azure Site Recovery per proteggere i carichi di lavoro in esecuzione su un server Hyper-V con Windows Server 2012 R2 all'interno di un sito locale. Per replicare contenuti in Azure, le macchine virtuali presenti in un server Hyper-V usano la replica Hyper-V. Questa distribuzione è particolarmente utile per gli uffici o le filiali che dispongono di server Hyper-V ma non hanno ancora distribuito System Center VMM.


<LI>Questa esercitazione usa il percorso di distribuzione più veloce e le impostazioni minime predefinite.
Per istruzioni complete sulla distribuzione, leggere le guide alla <a href="http://go.microsoft.com/fwlink/?LinkId=522087">pianificazione</a> e <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">distribuzione</a>.</LI>
<LI>Per scoprire altri scenari di distribuzione di Azure Site Recovery, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Panoramica di Azure Site Recovery</a>.</LI>
<LI>In caso di problemi durante questa esercitazione, vedere l'articolo wiki su <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Scenari di errore comuni e soluzioni</a> oppure inviare domande al <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum sui Servizi di ripristino di Azure</a>.</LI>
</UL>

</div>

<h2><a id="before" name="before" href="#before"></a>Prerequisiti</h2>
<div class="dev-callout"> 
<P>Prima di iniziare l'esercitazione, accertarsi che i seguenti prerequisiti siano soddisfatti.</P>

<UL>
<LI><b>Account di Azure</b>: è necessario disporre di un account di Azure. Se non si ha un account, vedere la pagina relativa alla <a href="http://aka.ms/try-azure">versione di prova gratuita di Azure</a>. Ottenere informazioni dettagliate sui <a href="http://go.microsoft.com/fwlink/?LinkId=378268">prezzi di Azure Site Recovery Manager</a>.</LI>
<LI><b>Hyper-V</b>: nel sito locale di origine è necessario almeno un server che esegue Windows Server 2012 R2 con il ruolo di Hyper-V. Il server Hyper-V deve contenere una o più macchine virtuali e deve essere connesso a Internet, in modo diretto o tramite proxy.</LI>
<LI><b>Macchine virtuali</b>: le macchine da proteggere devono essere conformi ai prerequisiti di Azure per le macchine virtuali. Vedere <a href="http://go.microsoft.com/fwlink/?LinkId=522287">Supporto di macchine virtuali</a>.</LI>

</UL>
<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Passaggi dell'esercitazione</h2>
Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:
<UL>
<LI><a href="#vault">Passaggio 1: Creare un insieme di credenziali</a>. Creare un insieme di credenziali di Azure Site Recovery.</LI>
<LI><a href="#site">Passaggio 2: Creare un sito Hyper-V</a>. Creare un sito Hyper-V come contenitore logico per tutti i server Hyper-V contenenti le macchine virtuali da proteggere.</LI>
<LI><a href="#download">Passaggio 3: Preparare i server Hyper-V</a>. Generare un codice di registrazione e scaricare il file di configurazione del provider. Eseguire il file su ciascun server Hyper-V presente nel sito e selezionare il codice per registrare il server nell'insieme di credenziali.</LI>
<LI><a href="#resources">Passaggio 4: Preparare le risorse</a>. Creare un account di archiviazione di Azure per consentire l'archiviazione delle macchine virtuali replicate.</LI>
<LI><a href="#protectiongroup">Passaggio 5: Creare e configurare gruppi di protezione</a>. Creare un gruppo di protezione e applicare ad esso impostazioni di protezione. Queste impostazioni verranno applicate ad ogni macchina virtuale aggiunta al gruppo.</LI>
<LI><a href="#enablevirtual">Passaggio 6: Abilitare la protezione per le macchine virtuali</a>. Abilitare la protezione per le macchine virtuali aggiungendole a un gruppo di protezione.</LI>
<LI><a href="#recovery plans">Passaggio 7: Testare la distribuzione</a>. Eseguire un failover di test per una macchina virtuale.</LI>

</UL>




<a name="vault"></a> <h3>Passaggio 1: Creare un insieme di credenziali</h3>

1. Accedere al [portale di gestione](https://manage.windowsazure.com).


2. Espandere <b>Servizi dati</b>, quindi <b>Servizi di ripristino</b>e fare clic su <b>Insieme di credenziali di Azure Site Recovery</b>.


3. Fare clic su <b>Crea nuovo</b> e quindi su <b>Creazione rapida</b>.
	
4. In <b>Nome</b> immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In <b>Area</b> selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate vedere <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Prezzi di Azure Site Recovery</a>.

6. Fare clic su <b>Crea insieme di credenziali</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come <b>Attivo</b> nella pagina principale di Servizi di ripristino.</P>

<a name="site"></a> <h3>Passaggio 2: Creare un sito Hyper-V</h3>

1. Nella pagina Servizi di ripristino fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Dall'elenco a discesa selezionare **Tra un sito Hyper-V locale e Azure**.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectScenario.png)

3. In **Crea un sito Hyper-V** fare clic su **Crea sito Hyper-V**. Specificare un nome per il sito e salvare.

	![Hyper-V site](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateSite2.png)

<a name="download"></a> <h3>Passaggio 3: Preparare i server Hyper-V</h3>
	

1. In **Prepara server VMM** fare clic sul file **Scarica codice di registrazione**.
2. Nella pagina **Scarica codice di registrazione** fare clic su **Scarica** accanto al nome del sito. Scaricare il codice in un percorso sicuro a cui sia possibile accedere facilmente tramite il server Hyper-V. Il codice è valido per 5 giorni dal momento in cui viene generato.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_DownloadKey2.png)

4. Fare clic su <b>Scaricare il provider</b> per ottenere la versione più recente.
5. Eseguire il file su ciascun server Hyper-V da registrare nell'insieme di credenziali. Il file installa due componenti:
	- **Provider di Azure Site Recovery**: gestisce le comunicazioni e il coordinamento tra il server Hyper-V e il portale di Azure Site Recovery. 
	- **Agente di Servizi di ripristino di Azure**: gestisce il trasferimento dei dati tra le macchine virtuali in esecuzione sul server Hyper-V di origine e l'archiviazione di Azure. 
6. In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider e dell'agente verranno installati in base ai criteri indicati in Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider1.png)

7. In **Installazione** specificare il percorso del server Hyper-V in cui si desidera installare il provider e l'agente.

	![Install location](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider2.png)

8. Al termine dell'installazione, proseguire con la configurazione per registrare il server nell'insieme di credenziali.

	![Installation complete](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider3.png)


9. Nella pagina **Connessione Internet** specificare la modalità di connessione del provider ad Azure Site Recovery. Selezionare <b>Usa impostazioni proxy del sistema predefinite</b> per usare le impostazioni di connessione a Internet predefinite configurate nel server. 

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider4.png)

9. Nella pagina **Impostazioni insieme di credenziali** fare clic su **Sfoglia** per selezionare il file di chiave. Specificare la sottoscrizione di Azure Site Recovery, il nome dell'insieme di credenziali e il sito Hyper-V a cui appartiene il server Hyper-V.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectKey.png)


11. La procedura di registrazione ha inizio e il server viene registrato nell'insieme di credenziali.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider6.png)

11. Al termine della registrazione, i metadati del server Hyper-V vengono recuperati da Azure Site Recovery e il server viene visualizzato nella scheda **Siti Hyper-V** della pagina **Server** all'interno dell'insieme di credenziali.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider7.png)


<h3><a id="resources"></a>Passaggio 4: Preparare le risorse</h3>


1. Nella pagina Avvio rapido, in **Prepara risorse**, selezionare **Crea account di archiviazione** per creare un account di archiviazione di Azure se non ne è ancora stato creato uno. Nell'account deve essere abilitata la replica geografica. L'account, inoltre, deve trovarsi nella stessa area geografica dell'insieme di credenziali di Azure Site Recovery e deve essere associato alla stessa sottoscrizione.

	![Create storage account](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateResources1.png)



<h3><a id="protectiongroup"></a>Passaggio 5: Creare e configurare gruppi di protezione</h3>

<p>I gruppi di protezione raggruppano le macchine virtuali che condividono le stesse impostazioni di protezione. Se si applicano specifiche impostazioni di protezione a un gruppo, queste vengono automaticamente applicate a tutte le macchine virtuali aggiunte al gruppo.</p>
1. In **Crea e configura gruppi di protezione** fare clic su **Crea gruppo di protezione**. Se non tutti i requisiti risultano soddisfatti, viene visualizzato un messaggio in cui è disponibile l'opzione **Visualizza dettagli** che consente di ottenere informazioni dettagliate.

2. Nella scheda **Gruppi protezione dati** aggiungere un gruppo di protezione. Specificare un nome, il sito Hyper-V di origine, **Azure** nel campo di destinazione, il nome della propria sottoscrizione di Azure Site Recovery e l'account di archiviazione di Azure.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroupCreate.png)


2. In **Impostazioni della replica** lasciare le impostazioni predefinite.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroup2.png)


<h3><a id="enablevirtual"></a>Passaggio 6: Abilitare la protezione delle macchine virtuali</h3>
<p>È possibile abilitare la protezione per le macchine virtuali aggiungendole a un gruppo di protezione.</p>

1. Nella scheda <b>Macchine</b> del gruppo di protezione, fare clic su <b>Per proteggere le macchine virtuali, aggiungerle ai gruppi di protezione</b>.
2. Nella pagina **Abilita protezione macchine virtuali** selezionare le macchine virtuali da proteggere. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_AddVM1.png)

Vengono avviati i processi di abilitazione della protezione, di cui è possibile monitorare lo stato di avanzamento nella scheda **Processi**. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover. Una volta abilitata la protezione e completata la replica iniziale, è possibile visualizzare le macchine virtuali in Azure.

In Azure Site Recovery è possibile visualizzare le macchine virtuali protette in **Elementi protetti** > **Gruppi di protezione** > *protectiongroup_name* > **Macchine virtuali**. 


<h3><a id="recoveryplans"></a>Passaggio 7: Testare la distribuzione</h3>

È possibile eseguire il test della distribuzione per simulare il meccanismo di failover e ripristino in una rete isolata, senza interferire con l'ambiente di produzione. A questo scopo verrà eseguito un failover di test per una macchina virtuale protetta.

1. In **Elementi protetti** > **Gruppi di protezione** > *protectiongroup_name* > **Macchine virtuali** selezionare la macchina virtuale di cui eseguire il failover e fare clic su **Failover di test**.
2. Nella pagina **Conferma failover di test** selezionare **Nessuno**. 

	![Recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_TestFailoverNoNetwork.png)

3. È possibile monitorare lo stato di avanzamento del processo di failover nella scheda **Processo**.
4. Quando il processo di failover raggiunge la fase conclusiva del test,**** completare la verifica come segue:
	- Al termine del failover, visualizzare la macchina virtuale di replica nel portale di Azure. Verificare che la macchina virtuale venga avviata correttamente.
	- Se è stato impostato l'accesso alle macchine virtuali dalla rete locale, è possibile inizializzare una Connessione Desktop remoto alla macchina virtuale.
	- Fare clic su **Completa il test** per portarlo a termine.
	- Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.
	- Fare clic su **Il failover di test è completo**. Pulire l'ambiente di test per spegnere automaticamente ed eliminare la macchina virtuale di test.

<h2><a id="runtest" name="runtest" href="#runtest"></a>Monitorare l'attività</h2>
<p>La scheda <b>Processi</b> e il <b>Dashboard</b> consentono di visualizzare e monitorare i principali processi eseguiti dall'insieme di credenziali di Azure Site Recovery, inclusi la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover (pianificato, non pianificato o di test) e il commit di un failover non pianificato.</p>

<p>Nella scheda <b>Processi</b> è possibile visualizzare i processi, analizzare in profondità i dettagli e gli errori, eseguire query per recuperare processi corrispondenti a criteri specifici, esportare processi in Excel e riavviare quelli non riusciti.</p>

<p>Nel <b>Dashboard</b> è possibile scaricare le versioni più recenti dei file di installazione del provider e dell'agente, ottenere informazioni di configurazione per l'insieme di credenziali, visualizzare il numero di macchine virtuali con protezione gestita dall'insieme di credenziali, visualizzare i processi recenti, gestire il certificato dell'insieme di credenziali e risincronizzare le macchine virtuali.</p>

<p>Per altre informazioni sull'interazione con i processi e il dashboard, vedere la <a href="http://go.microsoft.com/fwlink/?LinkId=398534">guida alle operazioni e al monitoraggio</a>.</p>

<h2><a id="next" name="next" href="#next"></a>Passaggi successivi</h2>
<UL>
<LI>Per pianificare e distribuire Azure Site Recovery in un ambiente di produzione completo, vedere la <a href="http://go.microsoft.com/fwlink/?LinkId=522087">guida alla pianificazione per Azure Site Recovery</a> e la <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">guida alla distribuzione per Azure Site Recovery</a>.</LI>
<LI>In caso di domande, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum relativo ai Servizi di ripristino di Azure</a>.</LI> 
</UL>

<!--HONumber=49-->