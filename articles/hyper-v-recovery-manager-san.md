<properties 
	pageTitle="Esercitazione: Configurare la protezione tra siti VMM locali con SAN" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali Hyper-V tra siti locali usando la replica SAN." 
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

# Esercitazione: Configurare la protezione tra siti VMM locali con SAN



<h2><a id="overview" name="overview" href="#overview"></a>Informazioni generali</h2>
<p>Azure Site Recovery favorisce la strategia di continuità del carico di lavoro e delle attività aziendali gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione.<p>

<P>Questa esercitazione illustra come distribuire Azure Site Recovery per gestire e automatizzare la protezione per carichi di lavoro in esecuzione su un server Hyper-V gestito da System Center VMM da un sito VMM locale a un altro sito VMM locale tramite la replica SAN basata su un array di archiviazione. Quando possibile, vengono usati il percorso di distribuzione più veloce e le impostazioni predefinite. In questo scenario:</P>

<ul>
	<li>Si sfrutta l'infrastruttura SAN esistente per proteggere le applicazioni mission-critical distribuite in cluster Hyper-V.</li>
	<li>La replica SAN fornisce il supporto per cluster guest e garantisce che la replica venga eseguita in modo uniforme tra i diversi livelli di un'applicazione mediante una replica sincronizzata o non sincronizzata, in base alle caratteristiche dell'array di archiviazione.</li>
	<li>Si configura e si abilita la replica in VMM e nell'insieme di credenziali di Azure Site Recovery. Si individuerà e si classificherà l'archiviazione SAN in VMM, si eseguirà il provisioning di unità logiche e si allocherà spazio di archiviazione a cluster Hyper-V. Azure Site Recovery automatizzerà la replica e gestirà il failover. </li>
	</ul>

<UL>
<LI>Per istruzioni complete sulla distribuzione, leggere le guide alla <a href="http://go.microsoft.com/fwlink/?LinkId=321294">pianificazione</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=519251">distribuzione</a>.</LI>
<LI>Per scoprire altri scenari di distribuzione di Azure Site Recovery, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Panoramica di Azure Site Recovery</a>.</LI>
<LI>In caso di problemi durante questa esercitazione, vedere l'articolo wiki su <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Scenari di errore comuni e soluzioni</a> oppure inviare domande al <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum sui Servizi di ripristino di Azure</a>.</LI>
</UL>




<h2><a id="before" name="before" href="#before"></a>prima di</h2>
<div class="dev-callout"> 
<P>Prima di iniziare la procedura dettagliata, accertarsi che i seguenti prerequisiti siano soddisfatti.</P>

<UL>
<LI><b>Account di Azure</b>: è necessario disporre di un account di Azure. Se non si ha un account, vedere la pagina relativa alla <a href="http://aka.ms/try-azure">versione di prova gratuita di Azure</a>. Ottenere informazioni dettagliate sui <a href="http://go.microsoft.com/fwlink/?LinkId=378268">prezzi di Azure Site Recovery Manager</a>.</LI>
<LI>Per sapere quali informazioni vengono raccolte, elaborate o trasmesse durante le operazioni di Azure Site Recovery, vedere la sezione sui <a href="http://go.microsoft.com/fwlink/?LinkId=513016">requisiti per la privacy</a> su MSDN.</LI>
<LI><b>Server VMM</b>: in ciascun sito locale è necessario un server VMM, distribuito come server autonomo, fisico o virtuale, o come cluster virtuale, eseguito su System Center 2012 R2 con <a href="http://go.microsoft.com/fwlink/?LinkId=517707">aggiornamento cumulativo 5.0 per VMM</a>.</LI>
<LI>È necessario un cluster host Hyper-V distribuito sia nel sito primario sia in quello secondario, che esegua almeno Windows Server 2012 con gli aggiornamenti più recenti.</LI>
<LI><b>Cloud VMM</b>: è necessario disporre di almeno un cloud sul server VMM primario che si desidera proteggere e uno sul server VMM secondario. Il cloud primario da proteggere deve includere quanto segue:<UL>
	<LI>Uno o più gruppi host VMM</LI>
	<LI>Uno o più cluster Hyper-V in ogni gruppo host.</LI>
	<li>Una o più macchine virtuali situate nel server Hyper-V di origine nel cloud.</li>
		</UL>Per altre informazioni sulla configurazione dei cloud VMM, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=513015">Pianificare l'infrastruttura VMM</a> nella Guida alla pianificazione.</LI>
<LI><b>Archiviazione SAN</b>: la replica SAN consente di replicare macchine virtuali in cluster guest con l'archiviazione iSCSI o Fibre Channel o usando dischi rigidi virtuali condivisi (vhdx). Di seguito sono elencati i prerequisiti SAN:</LI>
	<UL>
	<LI>Sono necessari due array configurati, uno nel sito primario, l'altro nel sito secondario.</LI>
	<LI>L'infrastruttura di rete deve essere configurata tra array. Devono essere configurati processi di peering e di replica. Le licenze di replica devono essere configurate in base ai requisiti dell'array di archiviazione.</LI>
	<LI>Devono essere configurate connessioni di rete tra i server host Hyper-V e l'array di archiviazione, in modo che gli host possano comunicare con le unità logiche di archiviazione tramite ISCSI o Fibre Channel.</LI>
	<LI>Per visualizzare un elenco degli array supportati, vedere la pagina relativa agli <a href="http://go.microsoft.com/fwlink/?LinkId=518669">array di archiviazione supportati in una distribuzione di Azure Site Recovery con VMM e SAN</a>.</LI>
	
	<LI>Devono essere installati provider SMI-S, forniti da EMC e NetApp, incaricati della gestione degli array SAN. I provider devono essere configurati in base alla documentazione di accompagnamento.</LI>
	<LI>Accertarsi che il provider SMI-S per l'array si trovi su un server a cui il server VMM può accedere tramite rete con un indirizzo IP o un nome di dominio completo (FQDN).</LI>
	<LI>In ciascun array SAN deve essere disponibile uno o più pool di archiviazione da poter usare in questa distribuzione.</LI>
	<LI>Il server VMM configurato nel sito primario dovrà gestire l'array primario, mentre il server VMM secondario dovrà gestire l'array secondario.</LI>
	</UL>
<LI><b>Reti</b>: è possibile configurare il mapping di rete per verificare che le macchine virtuali di replica siano correttamente posizionate su server host Hyper-V dopo il failover e che possano essere connesse a reti VM appropriate (facoltativo). Se è abilitato il mapping di rete, una macchina virtuale presente nella posizione principale verrà connessa a una rete e la replica della macchina virtuale nella posizione di destinazione verrà connessa alla rete mappata. Se invece non si configura il mapping di rete, le macchine virtuali non verranno connesse a reti VM in seguito al processo di failover. Questa esercitazione descrivere le impostazioni delle procedure più semplici e non include quindi il mapping di rete. Per informazioni su questo argomento, vedere:</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Mapping di rete</a> nella Guida alla pianificazione.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522290">Abilitare il mapping delle reti</a> nella guida alla distribuzione SAN.</LI>
	</UL>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Passaggi dell'esercitazione</h2>
Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:

1. <a href="#VMM">Passaggio 1: Preparare l'infrastruttura VMM</a>
	- Integrare e classificare l'archiviazione SAN in VMM
	- Eseguire il provisioning di unità logiche (LUN) e pool di archiviazione e allocare spazio di archiviazione a cluster Hyper-V.
	- Creare gruppi di replica per le unità logiche che dovranno essere replicate insieme. 
2. <a href="#vault">Passaggio 2: Creare un insieme di credenziali</a>
3. <a href="#download">Passaggio 3: Installare il provider nei server VMM</a>. Generare un codice di registrazione nell'insieme di credenziali e scaricare il file di configurazione del provider. Eseguire il file di configurazione sui server VMM per installare il provider e registrare i server nell'insieme di credenziali.
4. <a href="#storage">Passaggio 4: Mappare array e pool di archiviazione</a>. Mappare gli array per specificare il pool di archiviazione secondario che dovrà ricevere i dati di replica dal pool primario. Il mapping delle unità di archiviazione deve essere eseguito prima di configurare la protezione del cloud poiché le informazioni di mapping sono necessarie quando si abilita la protezione per i gruppi di replica.
5. <a href="#clouds">Passaggio 5: Configurare la protezione del cloud</a>. Configurare le impostazioni di protezione per i cloud VMM.
7. <a href="#replication">Passaggio 6: Abilitare i gruppi di replica</a>. Prima di abilitare la protezione per le macchine virtuali è necessario abilitare la replica per i gruppi di replica di archiviazione.
8. <a href="#enablevirtual">Passaggio 7: Abilitare la protezione per le macchine virtuali</a>. Dopo aver replicato i gruppi di replica, è necessario abilitare la protezione per le macchine virtuali.
9. <a href="#recovery plans">Passaggio 8: Testare la distribuzione</a>. È possibile verificare che il failover delle macchine virtuali e dei dati venga effettuato correttamente eseguendo un failover di test. 


<a name="VMM"></a> <h3>Passaggio 1: Preparare l'infrastruttura VMM</h3>

<a name="SAN"></a> <h4>Integrare e classificare l'archiviazione SAN in VMM</h4>


1. Nell'area di lavoro **Infrastruttura** fare clic su **Archiviazione**. Fare clic su **Home page** > **Aggiungi risorse** > **Dispositivi di archiviazione** per avviare l'Aggiunta guidata dispositivi di archiviazione.
2. Nella pagina **Seleziona un tipo di provider di archiviazione** selezionare **Dispositivi SAN e NAS individuati e gestiti da un provider SMI-S**.

	![Provider type](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Providertype.png)

3. Nella pagina **Specifica il protocollo e l'indirizzo del provider SMI-S di archiviazione** selezionare **SMI-S CIMXML** e specificare le impostazioni per la connessione al provider.
4. Nei campi **FQDN o indirizzo IP del provider** e **porta TCP/IP** specificare le impostazioni per la connessione al provider. È possibile usare una connessione SSL solo per SMI-S CIMXML.

	![Provider connect](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ConnectSettings.png)

5. In **Account RunAs** specificare un account VMM RunAs che possa accedere al provider o creare un nuovo account.
6. VMM tenta di individuare e importare automaticamente nella pagina **Raccolta informazioni** alcune informazioni sul dispositivo di archiviazione. Per provare a recuperare altre informazioni fare clic su **Analizza provider**. Se il tentativo ha esito positivo, nella pagina vengono elencati gli array di archiviazione individuati, i pool di archiviazione, il produttore, il modello e la capacità.

	![Discover storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Discover.png)

7. In **Selezionare i pool di archiviazione da gestire e assegnare una classificazione** selezionare i pool di archiviazione che dovranno essere gestiti da VMM e assegnare loro una classificazione. Dai pool di archiviazione verranno importate informazioni sulle unità logiche. Creare unità logiche in base alle applicazioni da proteggere, ai relativi requisiti di capacità e ai requisiti sugli elementi da replicare insieme.

	![Classify storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Classify.png)

<a name="LUNs"></a> <h4>Creare unità logiche e allocare spazio di archiviazione.</h4>


1. Dopo aver integrato l'archiviazione SAN in VMM, eseguire il provisioning di (ovvero creare) unità logiche. Per informazioni dettagliate, vedere:
	
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518490">Come selezionare un metodo per la creazione di unità logiche in VMM</a>
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518491">Come eseguire il provisioning di unità logiche di archiviazione in VMM</a>
2. Allocare quindi capacità di archiviazione al cluster host Hyper-V, in modo che VMM possa distribuire i dati delle macchine virtuali nell'archiviazione su cui è stato eseguito il provisioning: 
	- Prima di allocare capacità di archiviazione al cluster, è necessario allocare capacità di archiviazione al gruppo host VMM in cui risiede il cluster. Vedere <a href="http://go.microsoft.com/fwlink/?LinkId=518493">Modalità di allocazione delle unità logiche di archiviazione a un gruppo Host</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=518492">Modalità di allocazione di pool di archiviazione a un gruppo Host</a>.
	- Allocare la capacità di archiviazione al cluster, come descritto in <a href="http://go.microsoft.com/fwlink/?LinkId=513017">Come configurare l'archiviazione in un cluster host Hyper-V in VMM</a>.
	

<a name="RGs"></a> <h4>Creare gruppi di replica</h4>

1. Creare un gruppo di replica contenente tutte le unità logiche che dovranno essere replicate insieme.
2. Nella console VMM aprire la scheda **Gruppi di replica** nelle proprietà dell'array di archiviazione e fare clic su **Nuovo**.
	![SAN replication group](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RepGroup.png)



<a name="vault"></a> <h3>Passaggio 2: Creare un insieme di credenziali</h3>
Dopo aver convalidato i prerequisiti di distribuzione, è necessario creare un insieme di credenziali di Azure Site Recovery. In alternativa, è possibile usare un insieme di credenziali esistente e configurare la replica SAN.

1. Accedere al [portale di gestione](https://manage.windowsazure.com).


2. Espandere <b>Servizi dati</b>, quindi <b>Servizi di ripristino</b>e fare clic su <b>Insieme di credenziali di Azure Site Recovery</b>.


3. Fare clic su <b>Crea nuovo</b> e quindi su <b>Creazione rapida</b>.
	
4. In <b>Nome</b> immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In <b>Area</b> selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate, vedere la sezione Disponibilità a livello geografico nell'articolo <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Prezzi di Azure Site Recovery</a>.

6. Fare clic su <b>Crea insieme di credenziali</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come <b>Attivo</b> nella pagina principale di Servizi di ripristino.</P>

<a name="upload"></a> <h2>Passaggio 3: Configurare l'insieme di credenziali</h3>


1. Nella pagina <b>Servizi di ripristino</b> fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. Dall'elenco a discesa selezionare **Tra siti Hyper-V locali tramite la replica di array**.


<a name="download"></a> <h3>Passaggio 3: Installare il provider di Azure Site Recovery</h3>
Dopo aver creato l'insieme di credenziali, è necessario generare un file di registrazione che contenga un codice di registrazione. Il file dovrà essere selezionato durante l'installazione del provider di Azure Site Recovery. 

1. Nella pagina <b>Avvio rapido</b>, in **Prepara server VMM**, fare clic su **Genera codice di registrazione**. Il codice è valido per 5 giorni dal momento in cui viene generato. È opportuno scaricare il codice in un percorso sicuro a cui possano accedere anche i server VMM, ad esempio in una condivisione. Sarà necessario selezionare il file durante la configurazione del provider.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartRegKey.png)


2. Nella pagina <b>Avvio rapido</b>, in **Prepara server VMM**, fare clic su <b>Scarica il provider di Microsoft Azure Site Recovery per l'installazione nei server VMM</b> per ottenere la versione più recente del file di installazione del provider.
3. Eseguire il file nei server VMM di origine e di destinazione.
4. In **Verifica prerequisiti** selezionare l'opzione per l'arresto del servizio VMM per iniziare la configurazione del provider. Il servizio verrà arrestato e riavviato automaticamente al termine dell'installazione. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderPrereq.png)

5. In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider verranno installati in base ai criteri indicati in Microsoft Update.

Dopo l'installazione del provider, continuare con il programma di installazione per registrare il server nell'insieme di credenziali.

6. Nella pagina Connessione Internet specificare la modalità di connessione a Internet del provider in esecuzione sul server VMM. Selezionare <b>Usa impostazioni proxy del sistema predefinite</b> per usare le impostazioni predefinite di connessione a Internet configurate sul server.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderProxy.png)

7. In **Codice di registrazione** selezionare il codice scaricato da Azure Site Recovery e copiato nel server VMM.
8. In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderRegKeyServerName.png)

9. In **Sincronizzazione iniziale metadati cloud** selezionare l'opzione relativa alla sincronizzazione dei metadati di tutti i cloud presenti sul server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si desidera sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud
10. 
11. singolarmente nelle proprietà del cloud nella console VMM.

10. L'opzione **Crittografia dati** non è rilevante in questo scenario. 

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderSyncEncrypt.png)

11. Fare clic su <b>Registra</b> per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella scheda <b>Risorse</b> della pagina **Server** relativa all'insieme di credenziali. Dopo aver installato il provider, è possibile modificarne le impostazioni nella console VMM.

 
<h3><a id="storage"></a>Passaggio 4: Mappare array e pool di archiviazione</h3>

È necessario creare una rete di mapping tra gli array e i pool di archiviazione sia nel sito primario sia in quello secondario.

Prima di iniziare, verificare che i cloud siano visibili nell'insieme di credenziali. I cloud possono essere identificati scegliendo di sincronizzare tutti i cloud durante l'installazione del provider oppure selezionando la sincronizzazione di un cloud specifico nella scheda **Generale** delle proprietà del cloud nella console VMM. Eseguire quindi il mapping degli array di archiviazione come segue:

1. Fare clic su **Risorse** > **Archiviazione server** > **Mappa array di origine e di destinazione**.
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMap.png)
2. Selezionare gli array di archiviazione presenti nel sito primario e mapparli agli array di archiviazione del sito secondario.
3.  Eseguire quindi il mapping dei pool di archiviazione di origine e di archiviazione all'interno degli array. A tale scopo, in **Pool di archiviazione** selezionare un pool di archiviazione di origine e uno di destinazione di cui eseguire il mapping.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMapPool.png)




<h3><a id="clouds"></a>Passaggio 5: Configurare le impostazioni di protezione del cloud</h3>

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. Quando si installa il provider, abilitare l'opzione **Sincronizza dati cloud con insieme credenziali** in modo che tutti i cloud presenti sul server VMM vengano visualizzati nella scheda <b>Elementi protetti</b> dell'insieme di credenziali.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SRSAN_CloudsList.png)

1. Nella pagina Avvio rapido fare clic su **Configura la protezione per i cloud VMM**.
2. Nella scheda **Elementi protetti** selezionare il cloud da configurare e passare alla scheda **Configurazione**. Si noti che:
3. In <b>Destinazione</b> selezionare <b>VMM</b>.
4. In <b>Percorso di destinazione</b> selezionare il server VMM locale che gestisce il cloud da usare per il ripristino.
5. In <b>Cloud di destinazione</b> selezionare il cloud di destinazione da usare per il failover di macchine virtuali nel cloud di origine. Si noti che:
	- È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
	- Un cloud può appartenere solo a una singola coppia di cloud, come cloud primario o di destinazione.
6. Azure Site Recovery verifica che i cloud abbiano accesso all'archiviazione abilitata per la replica SAN e che sia stato eseguito il peering degli array di archiviazione. Vengono visualizzati i peer degli array partecipanti.
7. Se la verifica ha esito positivo, in **Tipo di replica** selezionare **SAN**.

<p>Dopo aver salvato le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda <b>Processi</b>. È possibile modificare le impostazioni del cloud nella scheda <b>Configura</b>. Se si vuole modificare la posizione o il cloud di destinazione, è necessario rimuovere la configurazione del cloud e, successivamente, riconfigurare il cloud.</p>


<h3><a id="replication"></a>Passaggio 6: Abilitare la replica per i gruppi di replica</h3>

Prima di abilitare la protezione per le macchine virtuali è necessario abilitare la replica per i gruppi di replica di archiviazione. 

1. Nel portale di Azure Site Recovery, all'interno della pagina delle proprietà del cloud primario, aprire la scheda **Macchine virtuali**. Fare clic su **Aggiungi gruppo di replica**.
2. Selezionare uno o più gruppi di replica VMM associati al cloud, verificare gli array di origine e di destinazione e specificare la frequenza di replica.

<P>Al termine dell'operazione, Azure Site Recovery, insieme ai provider VMM e SMI-S, esegue il provisioning delle unità logiche di archiviazione del sito di destinazione e abilita la replica di archiviazione. Se il gruppo di replica è già stato replicato, Azure Site Recovery usa la relazione di replica esistente e aggiorna le informazioni in Azure Site Recovery.</P>

<h2><a id="enablevirtual"></a>Passaggio 7: Abilitare la protezione delle macchine virtuali</h2>
<p>Dopo aver avviato la replica di un gruppo di archiviazione, è necessario abilitare la protezione per le macchine virtuali nella console VMM usando uno dei metodi descritti di seguito:</p>



- **Nuova macchina virtuale**: nella console VMM, quando si crea una nuova macchina virtuale, si abilita la protezione di Azure Site Recovery e si associa la macchina virtuale al gruppo di replica.
Se si sceglie questa opzione, VMM usa la funzione di posizionamento intelligente per posizionare in modo ottimale l'archiviazione della macchina virtuale sulle unità logiche del gruppo di replica. Azure Site Recovery gestisce inoltre la creazione di una macchina virtuale ombra nel sito secondario e alloca la capacità necessaria per consentire l'avvio della macchina virtuale di replica dopo il failover.
- **Macchine virtuale esistente**: se in VMM è già stata distribuita una macchina virtuale, è possibile abilitare la protezione di Azure Site Recovery e migrare l'archiviazione in un gruppo di replica. Al termine di questa operazione, VMM e Azure Site Recovery identificano la nuova macchina virtuale e iniziano a gestirla in Azure Site Recovery per abilitarne la protezione. Nel sito secondario viene creata una macchina virtuale ombra e viene allocata la capacità necessaria per consentire l'avvio della macchina virtuale di replica dopo il failover.


	![Enable protection](./media/hyper-v-recovery-manager-configure-vault/SRSAN_EnableProtection.png)
	

<P>Una volta abilitate per la protezione, le macchine virtuali vengono visualizzate nella console di Azure Site Recovery. È possibile ora visualizzare le proprietà delle macchine virtuali, monitorarne lo stato ed eseguire il failover dei gruppi di replica che contengono più macchine virtuali. Tenere presente che nella replica SAN tutte le macchine virtuali associate a un gruppo di replica devono essere sottoposte a failover insieme. Il failover, infatti, viene eseguito prima a livello di archiviazione. È importante, quindi, raggruppare correttamente i gruppi di replica e collocare insieme solo le macchine virtuali associate.</P>

Tenere traccia dell'avanzamento dell'azione di abilitazione della protezione, inclusa la replica iniziale, nella scheda **Processi**. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover. 
	![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SRSAN_JobPropertiesTab.png)

<h3><a id="recoveryplans"></a>Passaggio 8: Testare la distribuzione</h3>



Eseguire il test della distribuzione per verificare che il failover delle macchine virtuali e dei dati venga eseguito correttamente. Per effettuare questa operazione, creare un piano di ripristino selezionando i gruppi di replica, quindi eseguire un failover di test sul piano.

1. Nella scheda **Piani di ripristino** fare clic su **Creare il piano di ripristino**.
2. Specificare un nome per il piano di ripristino e per i server VMM di origine e di destinazione. Sul server di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino. Selezionare **SAN** per visualizzare solo i cloud configurati per la replica SAN.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlan.png)

4. In **Seleziona macchine virtuali** selezionare i gruppi di replica. Tutte le macchine virtuali associate al gruppo di replica verranno selezionate e aggiunte al piano di ripristino. In questa esercitazione, le macchine virtuali vengono aggiunte al gruppo predefinito del piano di ripristino Gruppo 1. Se necessario, è possibile aggiungere altri gruppi. Al termine della replica, le macchine virtuali verranno avviate in base all'ordine dei gruppi del piano di ripristino.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlanVM.png)	
5. Una volta creato, il piano di ripristino verrà visualizzato nell'elenco all'interno della scheda **Piani di ripristino**. 
6. Nella scheda **Piani di ripristino** selezionare il piano e fare clic su **Failover di test**.
7. Nella pagina **Conferma failover di test** selezionare **Nessuno**. Se si abilita questa opzione, le macchine virtuali di replica su cui è stato eseguito il failover non verranno connesse in rete. Il test, infatti, controlla che il failover delle macchine virtuali venga eseguito correttamente ma non verifica l'ambiente di rete di replica. Se si vuole eseguire un failover di test più completo, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testare una distribuzione locale su MSDN</a>.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


8. La macchina virtuale di test viene creata sullo stesso host in cui è presente la macchina virtuale di replica, ma non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.
9. Al termine del processo di replica, alla macchina virtuale di replica verrà assegnato un indirizzo IP diverso dall'indirizzo IP della macchina virtuale primaria. Se gli indirizzi vengono emessi da un protocollo DHCP, l'aggiornamento verrà eseguito automaticamente. Se invece non si usa il protocollo DHCP e si desidera indirizzi IP identici, sarà necessario eseguire alcuni script.
10. Eseguire il seguente script di esempio per recuperare l'indirizzo IP.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. Eseguire il seguente script di esempio per aggiornare il DNS e specificare l'indirizzo IP recuperato mediante il precedente script di esempio.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**

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