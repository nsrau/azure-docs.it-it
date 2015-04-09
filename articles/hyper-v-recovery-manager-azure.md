<properties 
	pageTitle="Esercitazione: Configurare la protezione tra un sito VMM locale e Azure" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino in Azure di macchine virtuali Hyper-V ubicate in cloud VMM locali." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Esercitazione: Configurare la protezione tra un sito VMM locale e Azure

<h2><a id="overview" name="overview" href="#overview"></a>Informazioni generali</h2>
<p>Azure Site Recovery favorisce la strategia di continuità del carico di lavoro e delle attività aziendali gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione.<p>

<P>Questa esercitazione illustra come distribuire Azure Site Recovery per gestire la protezione tra un sito VMM locale e Azure tramite la replica Hyper-V.  Quando possibile, vengono usati il percorso di distribuzione più veloce e le impostazioni predefinite.</P>

<UL>
<LI>Per altre informazioni su una distribuzione completa, vedere le guide sulla <a href="http://go.microsoft.com/fwlink/?LinkId=321294">pianificazione</a> e la <a href="http://go.microsoft.com/fwlink/?LinkId=402679">distribuzione</a>.</LI>
<LI>Per scoprire altri scenari di distribuzione di Azure Site Recovery, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Panoramica di Azure Site Recovery</a>.</LI>
<LI>In caso di problemi durante questa esercitazione, vedere l'articolo wiki su <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Scenari di errore comuni e soluzioni</a> oppure inviare domande al <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum sui Servizi di ripristino di Azure</a>.</LI>
</UL>


<h2><a id="prerequisites" name="prerequisites" href="#prerequisites"></a>Prerequisiti</h2>
<div class="dev-callout"> 
<P>Prima di iniziare l'esercitazione, accertarsi che i seguenti prerequisiti siano soddisfatti.</P>

<UL>
<LI><b>Account di Azure</b>: è necessario disporre di un account di Azure. Se non si ha un account, vedere la pagina relativa alla <a href="http://aka.ms/try-azure">versione di prova gratuita di Azure</a>. Per informazioni sui prezzi della sottoscrizione, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Prezzi di Azure Site Recovery</a>.</LI>

<LI><b>Account di archiviazione di Azure</b>: per archiviare dati replicati in Azure è necessario un account di archiviazione di Azure. Nell'account deve essere abilitata la replica geografica. L'account deve trovarsi nella stessa area geografica in cui si trova il servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione. Per altre informazioni sulla configurazione dell'archiviazione di Azure, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Introduzione ad Archiviazione di Microsoft Azure</a>.</LI><LI><b>Server VMM</b>: un server VMM in esecuzione su System Center 2012 R2.</LI>
<LI><b>Cloud VMM</b>: almeno un cloud sul server VMM server. Il cloud deve includere:
	<UL>
	<LI>Uno o più gruppi host VMM</LI>
	<LI>Uno o più cluster o server host Hyper-V in ogni gruppo host.</LI>
	<li>Una o più macchine virtuali situate nel server Hyper-V di origine nel cloud. Le macchine virtuali devono essere di generazione 1.</li>
		</UL></LI>	
<LI><b>Macchina virtuale</b>: sono necessarie macchine virtuali conformi ai requisiti di Azure. Vedere <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Prerequisiti e supporto</a> nella Guida alla pianificazione.</LI>
<LI>Per un elenco completo dei requisiti di supporto delle macchine virtuali per il failover in Azure, vedere  </LI>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Passaggi dell'esercitazione</h2>
Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:
<UL>

<LI><a href="#vault">Passaggio 1: Creare un insieme di credenziali</a>. Creare un insieme di credenziali di Azure Site Recovery.</LI>
<LI><a href="#download">Passaggio 2: Installare l'applicazione del provider nel server VMM</a>. Generare un codice di registrazione nell'insieme di credenziali e scaricare il file di configurazione del provider. Eseguire il file di configurazione sul server VMM per installare il provider e registrare il server VMM nell'insieme di credenziali.</LI>
<LI><a href="#storage">Passaggio 3: Aggiungere un account di archiviazione di Azure</a>. Se non si dispone ancora di un account di archiviazione, crearne uno. </LI>
<LI><a href="#agent">Passaggio 4: Installare l'applicazione agente</a>. Installare l'agente di Servizi di ripristino di Microsoft Azure in ciascun host Hyper-V presente nei cloud VMM da proteggere.</LI>
<LI><a href="#clouds">Passaggio 5: Configurare la protezione del cloud</a>. Configurare le impostazioni di protezione per i cloud VMM.</LI>
<LI><a href="#NetworkMapping">Passaggio 6: Configurare il mapping di rete</a>. È possibile configurare il mapping di rete per associare le reti VM di origine alle reti Azure di destinazione (facoltativo).</LI>
<LI><a href="#virtualmachines">Passaggio 7: Abilitare la protezione per le macchine virtuali</a>. Abilitare la protezione per le macchine virtuali presenti in cloud VMM protetti.</LI>
<LI><a href="#test">Passaggio 8: Testare la distribuzione</a>. È possibile eseguire un failover di test per una singola macchina virtuale oppure creare un piano di ripristino e, per accertarsi che funzioni, eseguire un failover di test per il piano.</LI>
</UL>



<a name="vault"></a> <h3>Passaggio 1: Creare un insieme di credenziali</h3>

1. Accedere al [portale di gestione](https://manage.windowsazure.com).


2. Espandere <b>Servizi dati</b>, quindi <b>Servizi di ripristino</b>e fare clic su <b>Insieme di credenziali di Azure Site Recovery</b>.

3. Fare clic su <b>Crea nuovo</b> e quindi su <b>Creazione rapida</b>.
	

4. In <b>Nome</b> immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In <b>Area</b> selezionare l'area geografica per l'insieme di credenziali. Le aree geografiche disponibili includono Asia orientale, Europa occidentale, Stati Uniti occidentali, Stati Uniti orientali, Europa settentrionale e Asia sudorientale.
6. Fare clic su <b>Crea insieme di credenziali</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come <b>Attivo</b> nella pagina principale di Servizi di ripristino.</P>





 <a name="download"></a> <h3>Passaggio 2: Generare un codice di registrazione e installare il provider di Azure Site Recovery</h3>
 

1. Nella pagina <b>Servizi di ripristino</b> fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Dall'elenco a discesa selezionare **Tra un sito Hyper-V locale e Microsoft Azure**.
3. In **Prepara server VMM** fare clic sul file **Genera codice di registrazione**. Il codice è valido per 5 giorni dal momento in cui viene generato. Copiare il file nel server VMM. Il file sarà necessario quando si configura il provider.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png)

4. Nella pagina <b>Avvio rapido</b>, in **Prepara server VMM**, fare clic su <b>Scarica il provider di Microsoft Azure Site Recovery per l'installazione nei server VMM</b> per ottenere la versione più recente del file di installazione del provider.

2. Eseguire il file nel server VMM di origine.


3. In **Verifica prerequisiti** selezionare l'opzione per l'arresto del servizio VMM per iniziare la configurazione del provider. Il servizio verrà arrestato e riavviato automaticamente al termine dell'installazione.

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider verranno installati in base ai criteri indicati in Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Dopo l'installazione del provider, continuare con il programma di installazione per registrare il server nell'insieme di credenziali.

5. In **Connessione Internet** specificare la modalità di connessione a Internet del provider in esecuzione sul server VMM. Selezionare <b>Usa impostazioni proxy del sistema predefinite</b> per usare le impostazioni di connessione a Internet predefinite configurate nel server.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. In **Codice di registrazione** selezionare il codice scaricato da Azure Site Recovery e copiato nel server VMM.
7. In **Nome insieme di credenziali** verificare il nome dell'insieme di credenziali in cui verrà registrato il server.
8. In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.

	
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)

8. In **Sincronizzazione iniziale metadati cloud** selezionare l'opzione relativa alla sincronizzazione dei metadati di tutti i cloud presenti sul server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM.


9. In **Crittografia dati** è possibile specificare un percorso in cui salvare un certificato SSL generato automaticamente per la crittografia dei dati. Questo certificato viene usato se si abilita la crittografia dei dati per un cloud protetto da Azure nel portale di Azure Site Recovery. Conservare il certificato in una posizione sicura, poiché sarà necessario selezionarlo per decrittografare i dati nel caso in cui venga eseguito un failover in Azure. 
Se si sta eseguendo la replica da un sito locale a un sito locale, questa opzione non è rilevante.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Fare clic su <b>Registra</b> per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella scheda <b>Risorse</b> della pagina **Server** nell'insieme di credenziali.

<h3><a id="storage"></a>Passaggio 3: Creare un account di archiviazione di Azure</h3>
Se non si dispone di un account di archiviazione di Azure, fare clic su **Aggiungi un account di archiviazione di Azure**. Nell'account deve essere abilitata la replica geografica. L'account deve trovarsi nella stessa area geografica del servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione.

<P>Usare questa esercitazione per configurare un modello di prova rapido per Azure Site Recovery in una distribuzione da sito locale ad Azure. Dove possibile, vengono usati il percorso più rapido e le impostazioni predefinite. Verranno effettuate le operazioni di creazione di un insieme di credenziali di Azure Site Recovery, installazione del provider di Azure Site Recovery nel server VMM di origine, installazione dell'agente di Servizi di ripristino di Azure in server host Hyper-V nei cloud VMM, configurazione delle impostazioni di protezione cloud, abilitazione della protezione per le macchine virtuali e test della distribuzione.</P>

![Storage account](./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png)

<h3><a id="agent"></a>Passaggio 4: Installare l'agente di Servizi di ripristino di Azure in host Hyper-V</h3>

Installare l'agente di Servizi di ripristino di Azure su ogni server host Hyper-V presente nei cloud VMM da proteggere.

1. Nella pagina Avvio rapido fare clic su <b>Scarica agente Servizi di ripristino di Azure e installa negli host</b> per ottenere la versione più recente dei file di installazione dell'agente.

	![Install Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png)

2. Eseguire il file di installazione su ogni server host Hyper-V presente nei cloud VMM da proteggere.
3. Nella pagina **Controllo dei prerequisiti** fare clic su <b>Avanti</b>. Gli eventuali prerequisiti mancanti verranno installati automaticamente.

	![Prerequisites Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png)

4. Nella pagina **Impostazioni di installazione** specificare dove si vuole installare l'agente e selezionare il percorso della cache in cui verranno installati i metadati di backup. Fare clic su <b>Installa</b>.


<h3><a id="clouds"></a>Passaggio 5: Configurare le impostazioni di protezione del cloud</h3>

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. Quando si installa il provider, abilitare l'opzione **Sincronizza dati cloud con insieme credenziali** in modo che tutti i cloud presenti sul server VMM vengano visualizzati nella scheda <b>Elementi protetti</b> dell'insieme di credenziali.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)


1. Nella pagina Avvio rapido fare clic su **Configura la protezione per i cloud VMM**.
2. Nella scheda **Elementi protetti** fare clic sul cloud da configurare e passare alla scheda **Configurazione**.
3. In <b>Destinazione</b> selezionare <b>Microsoft Azure</b>.
4. In <b>Account di archiviazione</b> selezionare l'archiviazione di Azure che si vuole usare per archiviare le macchine virtuali di Azure.
5. Impostare <b>Crittografa dati archiviati</b> su <b>Disattivato</b>. Questa impostazione specifica che i dati devono essere replicati crittografati tra il sito locale e Azure.
6. In <b>Frequenza di copia</b> lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della sincronizzazione dei dati tra il percorso di origine e di destinazione.
7. In <b>Mantieni punti di ripristino per</b> lasciare l'impostazione predefinita. Il valore predefinito zero indica che solo il punto di ripristino più recente per una macchina virtuale primaria viene archiviato in un server host di replica.
8. In <b>Frequenza degli snapshot coerenti</b> con l'applicazione lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della creazione di snapshot. Gli snapshot usano il Servizio Copia Shadow del volume (VSS, Volume Shadow Copy Service) per assicurare che lo stato delle applicazioni sia coerente quando viene creato lo snapshot.  Se si imposta un valore, questo deve essere inferiore al numero di punti di ripristino aggiuntivi configurati.
9. In <b>Ora inizio replica</b> specificare quando deve essere avviata la replica iniziale dei dati in Azure. Verrà usato il fuso orario del server host Hyper-V. È consigliabile pianificare la replica iniziale durante le fasce orarie di minore attività. 

	![Cloud replication settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png)

Dopo aver salvato le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda <b>Processi</b>. Tutti i server host Hyper-V nel cloud di origine VMM verranno configurati per la replica.

Dopo il salvataggio, le impostazioni cloud possono essere modificate nella scheda <b>Configura</b>. Per modificare il percorso o la risorsa di archiviazione di destinazione, è necessario rimuovere la configurazione del cloud e quindi riconfigurare il cloud. Si noti che se si cambia l'account di archiviazione, la modifica viene applicata alle macchine virtuali abilitate per la protezione solo dopo la modifica dell'account di archiviazione. Non viene eseguita la migrazione delle macchine virtuali esistenti al nuovo account di archiviazione.</p>

<h3><a id="networkmapping"></a>Passaggio 6: Configurare il mapping di rete</h3>

<p>Questa esercitazione descrive il percorso più semplice per l'implementazione di Azure Site Recovery in un ambiente di test. Se si vuole configurare il mapping di rete nell'ambito di questa esercitazione, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=324817">Preparare il mapping delle reti</a> nella Guida alla pianificazione. Per configurare il mapping, attenersi alla procedura riportata in <a href="http://go.microsoft.com/fwlink/?LinkId=402533">Configurare il mapping di rete</a> nella guida alla distribuzione.</p>




<h3><a id="virtualmachines"></a>Passaggio 7: Abilitare la protezione per le macchine virtuali</h3>

Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud. Tenere presente quanto segue:

- Le macchine virtuali devono essere conformi ai requisiti di Azure. Vedere tali requisiti in <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Prerequisiti e supporto</a> nella Guida alla pianificazione.
- Per abilitare la protezione, è necessario che le proprietà del sistema operativo e del disco del sistema operativo siano impostate per la macchina virtuale. Quando si crea una macchina virtuale basata su un modello di macchina virtuale VMM è possibile impostare la proprietà. È possibile impostare queste proprietà anche per le macchine virtuali esistenti nelle schede **Generale** e **Configurazione hardware** delle proprietà delle macchine virtuali. Se queste proprietà non vengono impostate in VMM, sarà possibile configurarle nel portale di Azure Site Recovery.

![Create virtual machine](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png)

![Modify virtual machine properties](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png)


1. Per abilitare la protezione, nella scheda <b>Macchine virtuali</b> del cloud in cui si trova la macchina virtuale fare clic su <b>Abilita protezione</b> e selezionare <b>Aggiungi macchine virtuali</b>.
2. Nell'elenco di macchine virtuali nel cloud selezionare quella da proteggere. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png)

3. Verificare le proprietà della macchina virtuale e modificarle in base alle esigenze.

	![Verify virtual machines](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png)

Tenere traccia dell'avanzamento dell'azione di abilitazione della protezione, inclusa la replica iniziale, nella scheda **Processi**. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover. Al termine dell'operazione di abilitazione della protezione e di replica delle macchine virtuali, sarà possibile visualizzarle in Azure.


![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h3><a id="test"></a>Passaggio 8: Testare la distribuzione</h3>
Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale oppure creare un piano di ripristino costituito da più macchine virtuali ed eseguire un failover di test per il piano.  Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata. Tenere presente quanto segue:
<UL>
<li>Per eseguire la connessione alla macchina virtuale in Azure tramite Desktop remoto dopo il failover, abilitare Connessione Desktop remoto sulla macchina virtuale prima di eseguire il failover di test.</li>
<li>Dopo il failover si userà un indirizzo IP pubblico per connettersi alla macchina virtuale in Azure tramite Desktop remoto. Se si vuole procedere in questo senso, assicurarsi che non siano presenti criteri di dominio che impediscono la connessione a una macchina virtuale mediante un indirizzo pubblico.</li>
</UL>

1. Nella scheda **Piani di ripristino** aggiungere un nuovo piano. Specificare un nome, immettere **VMM** nel campo **Tipo origine**, specificare il server VMM di origine in **Origine** e digitare Azure nel campo relativo alla destinazione.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRAzure_RP1.png)

2. Nella pagina **Conferma failover di test** selezionare **Nessuno**. Tenere presente che un failover di test con questa impostazione verifica che la macchina virtuale venga replicata correttamente in Azure ma non controlla la configurazione della rete di replica. Se si vuole eseguire il test con una rete Azure specifica, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=522292">Testare una distribuzione da sito locale ad Azure</a>.

	![No network](./media/hyper-v-recovery-manager-configure-vault/SRAzure_TestFailoverNoNetwork.png)


3. Quando il processo di failover raggiunge la fase conclusiva del test,**** fare clic su **Completa test** per completare il failover di test. È possibile eseguire il drill-down nella scheda **Processo** per tenere traccia dello stato di avanzamento del processo di failover ed eseguire eventuali azioni necessarie.
4. Al completamento del processo di failover, procedere come segue:
	- Verificare che le macchine virtuali vengano avviate correttamente
	- Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.
	- Fare clic su **Il failover di test è completo**. Pulire l'ambiente di test per spegnere automaticamente la macchina virtuale di test ed eliminare la rete di Azure di test.
5. Al termine del processo di failover, nel portale di Azure sarà possibile visualizzare la replica del test eseguito sulla macchina virtuale. Se è stato impostato l'accesso alle macchine virtuali dalla rete locale, è possibile inizializzare una Connessione Desktop remoto alla macchina virtuale.



<h2><a id="runtest" name="runtest" href="#runtest"></a>Monitorare l'attività</h2>
<p>La scheda <b>Processi</b> e il <b>Dashboard</b> consentono di visualizzare e monitorare i principali processi eseguiti dall'insieme di credenziali di Azure Site Recovery, inclusi la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover (pianificato, non pianificato o di test) e il commit di un failover non pianificato.</p>

<p>Nella scheda <b>Processi</b> è possibile visualizzare i processi, analizzare in profondità i dettagli e gli errori, eseguire query per recuperare processi corrispondenti a criteri specifici, esportare processi in Excel e riavviare quelli non riusciti.</p>

<p>Nel <b>Dashboard</b> è possibile scaricare le versioni più recenti dei file di installazione del provider e dell'agente, ottenere informazioni di configurazione per l'insieme di credenziali, visualizzare il numero di macchine virtuali con protezione gestita dall'insieme di credenziali, visualizzare i processi recenti, gestire il certificato dell'insieme di credenziali e risincronizzare le macchine virtuali.</p>

<p>Per altre informazioni sull'interazione con i processi e il dashboard, vedere la <a href="http://go.microsoft.com/fwlink/?LinkId=398534">guida alle operazioni e al monitoraggio</a>.</p>

<h2><a id="next" name="next" href="#next"></a>Passaggi successivi</h2>
<UL>
<LI>Per pianificare e distribuire Azure Site Recovery in un ambiente di produzione completo, vedere la <a href="http://go.microsoft.com/fwlink/?LinkId=321294">guida alla pianificazione per Azure Site Recovery</a> e la <a href="http://go.microsoft.com/fwlink/?LinkId=321295">guida alla distribuzione per Azure Site Recovery</a>.</LI>


<LI>In caso di domande, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum relativo ai Servizi di ripristino di Azure</a>.</LI> 
</UL>

<!--HONumber=49-->