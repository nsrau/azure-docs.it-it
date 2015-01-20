<properties urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Introduzione ad Azure Site Recovery: protezione da sito VMM locale a sito VMM locale con replica Hyper-V" metaKeywords="Azure Site Recovery, VMM, cloud, ripristino di emergenza" description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali Hyper-V tra siti VMM locali." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery:  On-Premises to On-Premises VMM Site Protection with Hyper-V Replication" editor="jimbe" manager="johndaw" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/19/2014" ms.author="raynew" />


# Introduzione ad Azure Site Recovery:  protezione da sito VMM locale a sito VMM locale con replica Hyper-V


<div class="dev-callout"> 

<p>Azure Site Recovery favorisce la strategia di continuità del carico di lavoro e delle attività aziendali gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione.<p>

<P>Questa esercitazione illustra come distribuire Azure Site Recovery per gestire e automatizzare la protezione per carichi di lavoro in esecuzione da un sito VMM locale a un altro sito VMM locale tramite la replica Hyper-V.  Quando possibile, vengono usati il percorso di distribuzione più veloce e le impostazioni predefinite.</P>

<UL>
<LI>Istruzioni complete per la distribuzione sono disponibili nelle guide per la <a href="http://go.microsoft.com/fwlink/?LinkId=321294">pianificazione</a> e la <a href="http://go.microsoft.com/fwlink/?LinkId=321295">distribuzione</a>.</LI>
<LI>Per scoprire altri scenari di distribuzione di Azure Site Recovery, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Panoramica di Azure Site Recovery</a>.</LI>
<LI>In caso di problemi durante questa esercitazione, vedere l'articolo wiki su <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Scenari di errore comuni e soluzioni</a> oppure inviare domande al <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum sui Servizi di ripristino di Azure</a>.</LI>
</UL>

</div>


<h2><a id="before"></a>Prerequisiti</h2> 
<div class="dev-callout"> 
<P>Prima di iniziare l'esercitazione, accertarsi che i seguenti prerequisiti siano soddisfatti.</P>

<UL>
<LI><b>Account di Azure</b>: è necessario disporre di un account di Azure. Se non si ha un account, vedere la pagina relativa alla <a href="http://aka.ms/try-azure">versione di prova gratuita di Azure</a>. Ottenere <a href="http://go.microsoft.com/fwlink/?LinkId=378268">informazioni dettagliate sui prezzi di Azure Site Recovery Manager</a>.</LI>
<LI><b>Server VMM</b>: sarà necessario almeno un server VMM in esecuzione su System Center 2012 SP1 o System Center 2012 R2.</LI>
<LI><b>Cloud VMM</b>: è necessario almeno un cloud sul server VMM di origine che si vuole proteggere e uno sul server VMM di destinazione. Se è in esecuzione un server VMM, saranno necessari due cloud. Il cloud primario da proteggere deve includere quanto segue:<UL>
	<LI>Uno o più gruppi host VMM</LI>
	<LI>Uno o più cluster o server host Hyper-V in ogni gruppo host.</LI>
	<li>Una o più macchine virtuali situate nel server Hyper-V di origine nel cloud.</li>
		</UL></LI>
<LI>**Reti**: è possibile configurare il mapping di rete per verificare che le macchine virtuali di replica vengano correttamente posizionate su server host Hyper-V dopo il failover e che possano essere connesse a reti VM appropriate (facoltativo). Se è abilitato il mapping di rete, una macchina virtuale presente nella posizione principale verrà connessa a una rete e la replica della macchina virtuale nella posizione di destinazione verrà connessa alla rete mappata. Se invece non si configura il mapping di rete, le macchine virtuali non verranno connesse a reti VM in seguito al processo di failover. Questa esercitazione descrivere le impostazioni delle procedure più semplici e non include quindi il mapping di rete. Per informazioni su questo argomento, vedere:</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Mapping di rete</a> nella guida per la pianificazione.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522293">Abilitare il mapping di rete</a> nella guida per la distribuzione.</LI>
	</UL></LI>

</UL>



<h2><a id="tutorial"></a>Passaggi dell'esercitazione</h2> 

Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:
<UL>
<LI><a href="#vault">Passaggio 1: Creare un insieme di credenziali</a>: creare un insieme di credenziali di Azure Site Recovery.</LI>
<LI><a href="#download">Passaggio 2: Installare l'applicazione del provider in ogni server VMM</a>. Generare un codice di registrazione nell'insieme di credenziali e scaricare il file di configurazione del provider. Eseguire il file di configurazione su ciascun server VMM per installare il provider e registrare il server VMM nell'insieme di credenziali.</LI>
<LI><a href="#clouds">Passaggio 3: Configurare la protezione cloud</a>: configurare le impostazioni di protezione per i cloud VMM. Queste impostazioni di protezione vengono applicate su tutte le macchine virtuali del cloud abilitate per la protezione di Azure Site Recovery.</LI>
<LI><a href="#storagemapping">Passaggio 4: Configurare il mapping di archiviazione</a>. Se si vuole specificare il percorso in cui devono essere archiviati i dati di replica, è possibile configurare il mapping di archiviazione. In questo modo, le classificazioni di archiviazione presenti sul server VMM di origine vengono mappate a quelle presenti sul server di destinazione.</LI>
<LI><a href="#enablevirtual">Passaggio 5: Abilitare la protezione per le macchine virtuali</a>: abilitare la protezione per le macchine virtuali presenti in cloud VMM protetti.</LI>
<LI><a href="#recovery plans">Passaggio 6: Configurare ed eseguire piani di ripristino</a>: creare un piano di ripristino ed eseguire un failover di test per tale piano per verificarne il funzionamento.</LI>

</UL>




<a name="vault"></a> <h2>Passaggio 1: Creare un insieme di credenziali</h2>

1. Accedere al [portale di gestione](https://manage.windowsazure.com).


2. Espandere <b>Servizi dati</b>, quindi <b>Servizi di ripristino</b>e fare clic su <b>Insieme di credenziali di Azure Site Recovery</b>.


3. Fare clic su <b>Crea nuovo</b> e quindi su <b>Creazione rapida</b>.
	
4. In <b>Nome</b> immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In <b>Area</b> selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate vedere <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Prezzi di Azure Site Recovery</a>

6. Fare clic su <b>Crea insieme di credenziali</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come <b>Attivo</b> nella pagina principale di Servizi di ripristino.</P>

<a name="upload"></a> <h2>Passaggio 2: Configurare l'insieme di credenziali</h2>


1. Nella pagina <b>Servizi di ripristino</b> fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può essere anche aperta in qualsiasi momento tramite l'icona.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. Dall'elenco a discesa selezionare **Tra due siti Hyper-V locali**.
3. In **Prepara server VMM** fare clic sul file **Genera codice di registrazione**. Il codice è valido per 5 giorni dal momento in cui viene generato. Copiare il file nel server VMM. Il file sarà necessario quando si configura il provider.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png)
	



<a name="download"></a> <h2>Passaggio 3: Installare il provider di Azure Site Recovery</h2>
	

1. Nella pagina <b>Avvio rapido</b>, in **Prepara server VMM**, fare clic su <b>Scarica il provider di Microsoft Azure Site Recovery per l'installazione nei server VMM</b> per ottenere la versione più recente del file di installazione del provider.

2. Eseguire il file nei server VMM di origine e di destinazione.

3. In **Verifica prerequisiti** selezionare l'opzione per l'arresto del servizio VMM per iniziare la configurazione del provider. Il servizio verrà arrestato e riavviato automaticamente al termine dell'installazione. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider verranno installati in base ai criteri indicati in Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Dopo l'installazione del provider, continuare con il programma di installazione per registrare il server nell'insieme di credenziali.

5. Nella pagina Connessione Internet specificare la modalità di connessione a Internet del provider in esecuzione sul server VMM. Selezionare <b>Usa impostazioni proxy del sistema predefinite</b> per usare le impostazioni predefinite di connessione a Internet configurate sul server.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. In **Codice di registrazione** selezionare il codice scaricato da Azure Site Recovery e copiato nel server VMM.
7. In **Nome insieme di credenziali** verificare il nome dell'insieme di credenziali in cui verrà registrato il server.
8. In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)


9. In **Sincronizzazione iniziale metadati cloud** selezionare l'opzione relativa alla sincronizzazione dei metadati di tutti i cloud presenti sul server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM.


7. In **Crittografia dati** è possibile generare un certificato da usare per crittografare i dati protetti in Azure. 
Questa opzione non è rilevante per lo scenario descritto in questa esercitazione.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Fare clic su <b>Registra</b> per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella scheda <b>Risorse</b> della pagina **Server** relativa all'insieme di credenziali.




<h2><a id="clouds"></a>Passaggio 4: Configurare le impostazioni di protezione del cloud</h2>

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. Quando si installa il provider, abilitare l'opzione **Sincronizza dati cloud con insieme credenziali** in modo che tutti i cloud presenti sul server VMM vengano visualizzati nella scheda <b>Elementi protetti</b> dell'insieme di credenziali.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)

1. Nella pagina Avvio rapido fare clic su **Configura la protezione per i cloud VMM**.
2. Nella scheda **Elementi protetti** selezionare il cloud da configurare e passare alla scheda **Configurazione**. Si noti che:
3. In <b>Destinazione</b> selezionare <b>VMM</b>.
4. In <b>Percorso di destinazione</b> selezionare il server VMM locale che gestisce il cloud da usare per il ripristino.
4. In <b>Cloud di destinazione</b> selezionare il cloud di destinazione da usare per il failover di macchine virtuali nel cloud di origine. Si noti che:
	- È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
	- Un cloud può appartenere solo a una singola coppia di cloud, come cloud primario o di destinazione.

6. In <b>Frequenza di copia</b> lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della sincronizzazione dei dati tra il percorso di origine e di destinazione. Risulta rilevante solo se l'host Hyper-V esegue Windows Server 2012 R2. Per gli altri server viene usata un'impostazione predefinita pari a cinque minuti.
7. In <b>Punti di ripristino aggiuntivi</b> lasciare l'impostazione predefinita. Questo valore consente di specificare se si vogliono creare punti di ripristino aggiuntivi. Il valore predefinito zero specifica che solo il punto di ripristino più recente per una macchina virtuale primaria viene archiviato in un server host di replica.
8. In <b>Frequenza degli snapshop coerenti con l'applicazione</b> lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della creazione di snapshot. Gli snapshot usano il Servizio Copia Shadow del volume (VSS, Volume Shadow Copy Service) per assicurare che lo stato delle applicazioni sia coerente quando viene creato lo snapshot.  Se si desidera impostare questo valore per la procedura dettagliata dell'esercitazione, verificare che sia impostato su un valore inferiore al numero di punti di ripristino aggiuntivi configurati.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png)
9. In <b>Compressione trasferimento dati</b> specificare se si vogliono comprimere i dati replicati trasferiti. 
10. In <b>Autenticazione</b> specificare come viene autenticato il traffico tra i server host Hyper-V primario e di ripristino. Per questa procedura dettagliata, selezionare HTTPS a meno che non sia stato configurato un ambiente Kerberos funzionante. Azure Site Recovery configura automaticamente i certificati per l'autenticazione HTTPS. Non è necessaria alcuna configurazione manuale. Questa impostazione è rilevante solo per i server host Hyper-V in esecuzione su Windows Server 2012 R2.
11. In <b>Porta</b> lasciare l'impostazione predefinita. Questo valore consente di impostare il numero di porta su cui i computer host Hyper-V di origine e di destinazione rimangono in attesa di traffico di replica.
12. In <b>Metodo di replica</b> specificare il modo in cui verrà gestita la replica iniziale dei dati dalla posizione di origine a quella di destinazione, prima che la replica normale venga avviata. 
	- <b>Sulla rete</b>: la copia di dati tramite la rete può richiedere molto tempo e numerose risorse. È consigliabile usare questa opzione se il cloud contiene macchine virtuali con dischi rigidi virtuali relativamente piccoli e se il server VMM primario è connesso al server VMM secondario su una connessione a banda larga. È possibile specificare che la copia deve iniziare immediatamente oppure selezionare un orario. Se si usa la replica tramite la rete, è consigliabile pianificarla durante le fasce orarie di minore attività.
	- <b>Offline</b>: tramite questo metodo viene specificato che la replica iniziale sarà eseguita usando supporti esterni. Questa opzione è utile se si desidera evitare un calo delle prestazioni di rete oppure per percorsi remoti a livello geografico. Per usare questo metodo, è necessario specificare la posizione di esportazione nel cloud di origine e quella di importazione nel cloud di destinazione. Quando si abilita la protezione per una macchina virtuale, il disco rigido virtuale viene copiato nel percorso di esportazione specificato. È necessario inviarlo al sito di destinazione e copiarlo nel percorso di importazione. Il sistema copia le informazioni importate nelle macchine virtuali di replica. Per un elenco completo dei prerequisiti per la replica offline, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM</a> nella Guida alla distribuzione.
13. Selezionare **Elimina macchina virtuale di replica** per specificare che la replica della macchina virtuale deve essere eliminata se si interrompe la protezione della macchina virtuale stessa selezionando l'opzione **Disabilita protezione per la macchina virtuale** sulla scheda delle proprietà del cloud relativa alle macchine virtuali. Con questa impostazione abilitata, quando si disabilita la protezione la macchina virtuale viene rimossa da Azure Site Recovery, le impostazioni di Site Recovery relative alla macchina virtuale vengono rimosse dalla console VMM e la replica viene eliminata.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png)

<p>Dopo aver salvato le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda <b>Processi</b>. Tutti i server host Hyper-V nel cloud di origine VMM verranno configurati per la replica. È possibile modificare le impostazioni del cloud nella scheda <b>Configura</b>. Se si vuole modificare la posizione o il cloud di destinazione, è necessario rimuovere la configurazione del cloud e, successivamente, riconfigurare il cloud.</p>

<h2><a id="storagemapping"></a>Passaggio 5: Configurare il mapping di archiviazione</h2>

<p>Questa esercitazione descrive il percorso più semplice per l'implementazione di Azure Site Recovery in un ambiente di test. Se si vuole configurare il mapping di archiviazione nell'ambito di questa esercitazione, attenersi alla procedura riportata in <a href="http://go.microsoft.com/fwlink/?LinkId=402535">Configurare il mapping di archiviazione</a> nella Guida alla pianificazione.</p>


<h2><a id="enablevirtual"></a>Passaggio 6: Abilitare la protezione delle macchine virtuali</h2>
<p>Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud.</p>
<OL>
<li>Nella scheda <b>Macchine virtuali</b> nel cloud in cui si trova la macchina virtuale fare clic su <b>Abilita protezione</b> e selezionare <b>Aggiungi macchine virtuali</b>. </li>
<li>Nell'elenco di macchine virtuali nel cloud selezionare quella da proteggere.</li> 
</OL>

![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png)


<P>Tenere traccia dell'avanzamento dell'azione di abilitazione della protezione, inclusa la replica iniziale, nella scheda **Processi**. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover. Al termine dell'operazione di abilitazione della protezione e di replica delle macchine virtuali, sarà possibile visualizzarle in Azure.</p>



![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="recoveryplans"></a>Passaggio 7: testare la distribuzione</h2>

Eseguire il test della distribuzione per verificare che il failover delle macchine virtuali e dei dati venga eseguito correttamente. Per effettuare questa operazione, creare un piano di ripristino selezionando i gruppi di replica, quindi eseguire un failover di test sul piano.

1. Nella scheda **Piani di ripristino** fare clic su **Creare il piano di ripristino**.
2. Specificare un nome per il piano di ripristino e per i server VMM di origine e di destinazione. Sul server di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino. Selezionare **Hyper-V** per visualizzare solo i cloud configurati per la replica Hyper-V.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP1.png)

3. In **Seleziona macchine virtuali** selezionare i gruppi di replica. Tutte le macchine virtuali associate al gruppo di replica verranno selezionate e aggiunte al piano di ripristino. In questa esercitazione, le macchine virtuali vengono aggiunte al gruppo predefinito del piano di ripristino Gruppo 1. Se necessario, è possibile aggiungere altri gruppi. Al termine della replica, le macchine virtuali verranno avviate in base all'ordine dei gruppi del piano di ripristino.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP2.png)	

4. Una volta creato, il piano di ripristino verrà visualizzato nell'elenco all'interno della scheda **Piani di ripristino**. 
5. Nella scheda **Piani di ripristino** selezionare il piano e fare clic su **Failover di test**.
6. Nella pagina **Conferma failover di test** selezionare **Nessuno**. Se si abilita questa opzione, le macchine virtuali di replica su cui è stato eseguito il failover non verranno connesse in rete. Il test, infatti, controlla che il failover delle macchine virtuali venga eseguito correttamente ma non verifica l'ambiente di rete di replica. Per eseguire un failover di test più completo, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testare una distribuzione da sito locale a sito locale</a>.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


7. La macchina virtuale di test viene creata sullo stesso host in cui è presente la macchina virtuale di replica, ma non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.
8. Al termine del processo di replica, alla macchina virtuale di replica verrà assegnato un indirizzo IP diverso dall'indirizzo IP della macchina virtuale primaria. Se gli indirizzi vengono emessi da un protocollo DHCP, il DNS verrà aggiornato automaticamente. Se invece non si usa il protocollo DHCP e si desidera indirizzi IP identici, sarà necessario eseguire alcuni script.
9. Eseguire il seguente script di esempio per recuperare l'indirizzo IP.
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


<h3><a id="runtest"></a>Monitorare l'attività</h3>
<p>La scheda <b>Processi</b> e il <b>Dashboard</b> consentono di visualizzare e monitorare i principali processi eseguiti dall'insieme di credenziali di Azure Site Recovery, inclusi la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover (pianificato, non pianificato o di test) e il commit di un failover non pianificato.</p>

<p>Nella scheda <b>Processi</b> è possibile visualizzare i processi, analizzare in profondità i dettagli e gli errori, eseguire query per recuperare processi corrispondenti a criteri specifici, esportare processi in Excel e riavviare quelli non riusciti.</p>

<p>Nel <b>Dashboard</b> è possibile scaricare le versioni più recenti dei file di installazione del provider e dell'agente, ottenere informazioni di configurazione per l'insieme di credenziali, visualizzare il numero di macchine virtuali con protezione gestita dall'insieme di credenziali, visualizzare i processi recenti, gestire il certificato dell'insieme di credenziali e risincronizzare le macchine virtuali.</p>

<p>Per altre informazioni sull'interazione con i processi e il dashboard, vedere la <a href="http://go.microsoft.com/fwlink/?LinkId=398534">guida alle operazioni e al monitoraggio</a>.</p>
	
<h2><a id="next"></a>Passaggi successivi</h2>
<UL>
<LI>Per pianificare e distribuire Azure Site Recovery in un ambiente di produzione completo, vedere la <a href="http://go.microsoft.com/fwlink/?LinkId=321294">guida alla pianificazione per Azure Site Recovery</a> e la <a href="http://go.microsoft.com/fwlink/?LinkId=321295">guida alla distribuzione per Azure Site Recovery</a>.</LI>
<LI>In caso di domande, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum relativo ai Servizi di ripristino di Azure</a>.</LI> 
</UL>

<!--HONumber=35.2-->
