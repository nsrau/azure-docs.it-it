<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale

<div class="dev-callout">

<p>Usare Azure Site Recovery per gestire la protezione delle macchine virtuali su server host Hyper-V locali presenti in cloud VMM. È possibile configurare i seguenti elementi:</p>

<ul>
<li><b>Protezione da sito locale a sito locale</b>— le macchine virtuali locali vengono replicate in un altro sito locale. Configurare e abilitare le impostazioni di protezione negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati da un server Hyper-V locale a un altro. Per altre informazioni su questo scenario, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Introduzione ad Azure Site Recovery: protezione da sito locale a sito locale</a></li>
<li><b>Protezione da sito locale ad Azure</b>— le macchine virtuali locali vengono replicate in Azure. Configurare e abilitare le impostazioni di protezione negli insiemi di credenziali di Azure Site Recovery. I dati delle macchine virtuali vengono replicati da un server Hyper-V locale a una risorsa di archiviazione di Azure. Per altre informazioni su questo scenario, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Introduzione ad Azure Site Recovery: protezione da sito locale ad Azure</a></li>

</ul>




<h2><a id="about"></a>Informazioni sull'esercitazione</h2>

<P>Usare questa esercitazione per impostare un modello di prova rapido per Azure Site Recovery in una distribuzione locale di Azure. Quando possibile, vengono usati il percorso più veloce e le impostazioni predefinite. Verranno eseguite le seguenti attività: creazione di un insieme di credenziali di Azure Site Recovery, installazione del provider di Azure Site Recovery nel server VMM di origine, configurazione delle impostazioni di protezione del cloud, abilitazione delle macchine virtuali e test della distribuzione.</P>

<P>Per altre informazioni su una distribuzione completa, vedere:</P>

<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Pianificare la distribuzione di Azure Site Recovery</a>: descrive i passaggi di pianificazione che è necessario eseguire prima di avviare una distribuzione completa.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321295">Distribuire Azure Site Recovery: protezione da sito locale a sito locale</a>: fornisce istruzioni dettagliate per una distribuzione completa.</LI>

</UL>
<P>In caso di problemi nel corso dell'esercitazione, vedere l'articolo di Wiki relativo ad <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: scenari di errore comuni e soluzioni</a> oppure inviare le proprie domande al <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum sui servizi di ripristino di Azure</a>.</P>

</div>


<h2><a id="before"></a>Prima di iniziare</h2> 
<div class="dev-callout"> 
<P>Prima di iniziare l'esercitazione, verificare i prerequisiti seguenti.</P>

<h3><a id="HVRMPrereq"></a>Prerequisiti</h3>

<UL>
<LI><b>Account di Azure</b>: è necessario disporre di un account di Azure. Se non si dispone di un account, vedere la pagina <a href="http://aka.ms/try-azure">Versione di valutazione gratuita per un mese</a>. Per informazioni sui prezzi, consultare la pagina relativa ai <a href="http://go.microsoft.com/fwlink/?LinkId=378268">dettagli sui prezzi per Azure Site Recovery</a>.</LI>
<LI><b>Server VMM</b>: è necessario disporre di almeno un server VMM in esecuzione su System Center 2012 SP1 o System Center 2012 R2.</LI>
<LI><b>Cloud VMM</b>: è necessario disporre di almeno un cloud sul server VMM di origine che si desidera proteggere e uno sul server VMM di destinazione. Se è in esecuzione un server VMM, saranno necessari due cloud. Il cloud primario da proteggere deve includere quanto segue:<UL>
	<LI>Uno o più gruppi host VMM</LI>
	<LI>Uno o più cluster o server host Hyper-V in ogni gruppo host.</LI>
	<li>Una o più macchine virtuali presenti sul server Hyper-V di origine nel cloud.</li>
		</UL></LI>
</UL>



<h2><a id="tutorial"></a>Passaggi dell'esercitazione</h2> 

Dopo la verifica dei prerequisiti, eseguire le operazioni seguenti:
<UL>
<LI><a href="#vault">Passaggio 1: Creare un insieme di credenziali</a>: creare un insieme di credenziali di Azure Site Recovery.</LI>
<LI><a href="#download">Passaggio 2: Installare l'applicazione del provider</a>: generare una chiave di registrazione ed eseguire l'applicazione Microsoft Azure Site Recovery Provider sul server VMM. Questa operazione installa il provider e registra il server VMM nell'insieme di credenziali.</LI>
<LI><a href="#clouds">Passaggio 3: Configurare la protezione cloud</a>: configurare le impostazioni di protezione per i cloud VMM.</LI>
<LI><a href="#networkmapping">Passaggio 5: Configurare il mapping di rete: facoltativamente, è possibile configurare il mapping di rete per mappare le reti VM di origine a quelle di destinazione.</LI>
<LI><a href="#storagemapping">Passaggio 6: Configurare il mapping di archiviazione</a>: facoltativamente, è possibile configurare il mapping di archiviazione per mappare le classificazioni di archiviazione presenti sul server VMM di origine a quelle presenti sul server di destinazione.</LI>
<LI><a href="#enablevirtual">Passaggio 7: Abilitare la protezione per le macchine virtuali</a>: abilitare la protezione per le macchine virtuali presenti in cloud VMM protetti.</LI>
<LI><a href="#recovery plans">Passaggio 8: Configurare ed eseguire piani di ripristino</a>: creare un piano di ripristino ed eseguire un failover di test per tale piano.</LI>
</UL>




<a name="vault"></a> <h2>Passaggio 1: Creare un insieme di credenziali</h2>

</p>
1.  Accedere al [portale di gestione][portale di gestione].

2.  Espandere <b>Data Services</b>, quindi <b>Servizi di ripristino</b> e fare clic su <b>Site Recovery Vault</b>.

3.  Fare clic su <b>Crea nuovo</b>, quindi su <b>Creazione rapida</b>.

4.  In <b>Nome</b> immettere un nome descrittivo per identificare l'insieme di credenziali.

5.  In <b>Area</b> selezionare l'area geografica per l'insieme di credenziali. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery][1].

6.  Fare clic su <b>Crea insieme di credenziali</b>.

    ![Nuovo insieme di credenziali][Nuovo insieme di credenziali]

<p>Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà indicato come <b>Attivo</b> nella pagina principale di Servizi di ripristino.</p>

<a name="upload"></a> <h2>Passaggio 2: Configurare l'insieme di credenziali</h2>


1.  Nella pagina <b>Servizi di ripristino</b> fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. È possibile aprire la pagina Avvio rapido in qualsiasi momento usando l'icona corrispondente.

    ![Icona Avvio rapido][Icona Avvio rapido]

2.  Dall'elenco a discesa selezionare **Between two on-premises Hyper-V sites**.
3.  In **Prepare VMM Servers** fare clic sul file **Generate registration key**. Una volta generata, la chiave rimane valida per 5 giorni. Copiare il file sul server VMM. Questo file sarà necessario al momento della generazione del provider.

    ![Chiave di registrazione][Chiave di registrazione]

<a name="download"></a> <h2>Passaggio 3: Installare Azure Site Recovery Provider</h2>

1.  Nella pagina <b>Avvio rapido</b>, in **Prepare VMM servers**, fare clic su <b>Download Microsoft Azure Site Recovery Provider for installation on VMM servers</b> per ottenere la versione più recente del file di installazione del provider.

2.  Eseguire questo file sul server VMM di origine e su quello di destinazione.

3.  In **Pre-requirements Check** selezionare l'opzione per l'arresto del servizio VMM per iniziare la configurazione del provider. Il servizio si arresta e verrà riavviato automaticamente al termine della configurazione.

    ![Prerequisiti][Prerequisiti]

4.  In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Con questa impostazione abilitata, gli aggiornamenti del provider verranno installati in base al criterio Microsoft Update dell'utente.

    ![Aggiornamenti Microsoft][Aggiornamenti Microsoft]

Una volta installato il provider, continuare la configurazione per registrare il server nell'insieme di credenziali.

5.  Nella pagina Internet Connection specificare la modalità di connessione a Internet del provider in esecuzione nel server VMM. Selezionare <b>Use default system proxy settings</b> per usare le impostazioni predefinite di connessione a Internet configurate sul server.

    ![Impostazioni Internet][Impostazioni Internet]

6.  In **Registration Key** selezionare il codice di registrazione scaricato da Azure Site Recovery e copiato nel server VMM.
7.  In **Vault name** verificare il nome dell'insieme di credenziali in cui verrà registrato il server.
8.  In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.

    ![Registrazione del server][Registrazione del server]

9.  In **Initial cloud metadata** selezionare l'opzione relativa alla sincronizzazione dei metadati per tutti i cloud presenti sul server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si desidera sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare singolarmente ciascun cloud nelle proprietà del cloud della console VMM.

7.  In **Data Encryption** è possibile generare un certificato da usare per crittografare i dati protetti in Azure.
    Se si sta eseguendo la replica da sito locale a sito locale, questa opzione non è rilevante.

    ![Registrazione del server][2]

8.  Fare clic su <b>Register</b> per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella parte finale della scheda <b>Resources</b> della pagina **Servers** dell'insieme di credenziali.

<h2><a id="clouds"></a>Passaggio 4: Configurare le impostazioni di protezione del cloud</h2>

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. Quando si installa il provider, abilitare l'opzione **Synchronize cloud data with the vault**, in modo che tutti i cloud presenti sul server VMM vengano visualizzati nella scheda <b>Elementi protetti</b> dell'insieme di credenziali.

![Cloud pubblicato][Cloud pubblicato]

1.  Nella pagina Avvio rapido fare clic su **Set up protection for VMM clouds**.
2.  Nella scheda **Elementi protetti** selezionare il cloud da configurare, quindi passare alla scheda **Configurazione**. Si noti che:
3.  In <b>Destinazione</b> selezionare <b>VMM</b>.
4.  In <b>Percorso di destinazione</b> selezionare il server VMM locale che gestisce il cloud da usare per il ripristino.
5.  In <b>Cloud di destinazione</b> selezionare il cloud di destinazione da usare per il failover di macchine virtuali nel cloud di origine. Si noti che:

    -   È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
    -   Un cloud può appartenere solo a una singola coppia di cloud, come cloud primario o di destinazione.

6.  In <b>Frequenza di copia</b> lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della sincronizzazione dei dati tra il percorso di origine e di destinazione. Risulta rilevante solo se l'host Hyper-V esegue Windows Server 2012 R2. Per gli altri server viene usata un'impostazione predefinita pari a cinque minuti.
7.  In <b>Punti di ripristino aggiuntivi</b> lasciare l'impostazione predefinita. Questo valore consente di specificare se si desidera creare punti di ripristino aggiuntivi. Il valore predefinito zero indica che solo il punto di ripristino più recente per una macchina virtuale primaria viene archiviato in un server host di replica.
8.  In <b>Frequenza dell'applicazione - Snapshot coerenti</b> lasciare l'impostazione predefinita. Questo valore consente di specificare la frequenza della creazione di snapshot. Gli snapshot usano il Servizio Copia Shadow del volume (VSS, Volume Shadow Copy Service) per assicurare che lo stato delle applicazioni sia coerente quando viene creato lo snapshot. Se si desidera impostare questo valore per la procedura dettagliata dell'esercitazione, verificare che sia impostato su un valore inferiore al numero di punti di ripristino aggiuntivi configurati.
    ![Configurazione delle impostazioni di protezione][Configurazione delle impostazioni di protezione]
9.  In <b>Compressione trasferimento dati</b> specificare se si desidera comprimere i dati replicati trasferiti.
10. In <b>Autenticazione</b> specificare come viene autenticato il traffico tra i server host Hyper-V primario e di ripristino. Ai fini di questa procedura dettagliata, selezionare HTTPS, a meno che non sia stato configurato un ambiente Kerberos funzionante. Azure Site Recovery configurerà automaticamente i certificati per l'autenticazione HTTPS. Non è necessaria alcuna configurazione manuale. Questa impostazione è rilevante solo per i server host Hyper-V in esecuzione su Windows Server 2012 R2.
11. In <b>Porta</b> lasciare l'impostazione predefinita. Questo valore consente di impostare il numero di porta su cui i computer host Hyper-V di origine e di destinazione rimangono in attesa del traffico di replica.
12. In <b>Metodo di replica</b> specificare il modo in cui verrà gestita la replica iniziale di dati dal percorso di origine al percorso di destinazione, prima dell'inizio della replica normale.

    -   <b>Over network</b>: la copia di dati tramite rete può richiedere molto tempo e molte risorse. È consigliabile usare questa opzione se il cloud contiene macchine virtuali con dischi rigidi virtuali relativamente piccoli e se il server VMM primario è connesso al server VMM secondario su una connessione a banda larga. È possibile specificare che la copia deve iniziare immediatamente oppure selezionare un orario. Se si usa la replica tramite la rete, è consigliabile pianificarla durante le fasce orarie di minore attività.
    -   <b>Offline</b>: questo metodo specifica che la replica iniziale sarà eseguita usando supporti esterni. Questa opzione è utile se si desidera evitare un calo delle prestazioni di rete oppure per percorsi remoti a livello geografico. Per usare questo metodo, è necessario specificare la posizione di esportazione nel cloud di origine e quella di importazione nel cloud di destinazione. Quando si abilita la protezione per una macchina virtuale, il disco rigido virtuale viene copiato nel percorso di esportazione specificato. È necessario inviarlo al sito di destinazione e copiarlo nel percorso di importazione. Il sistema copia le informazioni importate nelle macchine virtuali di replica. Per un elenco completo dei prerequisiti per la replica offline, vedere [Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM][Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM] nella Guida alla distribuzione.

13. Selezionare <b>Delete Replica Virtual Machine</b> per specificare che la replica della macchina virtuale deve essere eliminata se si interrompe la protezione della macchina virtuale stessa selezionando l'opzione <b>Delete protection for the virtual machine</b> sulla scheda delle proprietà del cloud relativa alle macchine virtuali. Con questa impostazione abilitata, quando si disabilita la protezione la macchina virtuale viene rimossa da Azure Site Recovery, le impostazioni di Site Recovery relative alla macchina virtuale vengono rimosse dalla console VMM e la replica viene eliminata.
    ![Configurazione delle impostazioni di protezione][3]

<p>Una volta salvate le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda <b>Processi</b>. Tutti i server host Hyper-V presenti nel cloud di origine VMM verranno configurati per la replica. È possibile modificare le impostazioni del cloud nella scheda <b>Configura</b>. Se si desidera modificare il percorso o il cloud di destinazione sarà necessario rimuovere la configurazione del cloud e quindi riconfigurare il cloud.</p>

<h2><a id="networkmapping"></a>Passaggio 5: Configurare il mapping di rete</h2>

<p>Questa esercitazione descrive la procedura più semplice per distribuire Azure Site Recovery in un ambiente di test. Se si desidera configurare il mapping di rete come parte dell'esercitazione, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=324817">Passaggio 5: Configurare il mapping di archiviazione - Da sito locale a sito locale</a> nella Guida alla pianificazione. Per ulteriori informazioni sul mapping di rete, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=402534">Passaggio 4: Configurare il mapping delle reti - Da sito locale a sito locale</a> nella Guida alla pianificazione.</p>

<h2><a id="storagemapping"></a>Passaggio 6: Configurare il mapping di archiviazione</h2>

<p>Questa esercitazione descrive la procedura più semplice per distribuire Azure Site Recovery in un ambiente di test. Se si desidera configurare il mapping di configurazione come parte dell'esercitazione, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=402535"> Configurare il mapping di archiviazione - Da sito locale a sito locale</a> nella Guida alla pianificazione.</p>

<h2><a id="enablevirtual"></a>Passaggio 7: Abilitare la protezione per le macchine virtuali</h2>
<p>Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud.</p>
<OL>
<li>Nella scheda <b>Virtual Machines</b> del cloud in cui è presente la macchina virtuale fare clic su <b>Enable protection</b>, quindi selezionare <b>Add virtual machines</b>.</li>
<li>Dall'elenco delle macchine virtuali presenti nel cloud selezionare quella che si desidera proteggere.</li> 
</OL>

![Abilitare la protezione per le macchine virtuali][Abilitare la protezione per le macchine virtuali]

<P>Tenere traccia dell'avanzamento dell'azione di abilitazione della protezione, inclusa la replica iniziale, nella scheda relativa ai processi. Dopo il completamento del processo di finalizzazione della protezione, la macchina virtuale è pronta per il failover. Una volta abilitata la protezione e replicate le macchine virtuali, sarà possibile visualizzare queste ultime in Azure.</P>

![Processo di protezione della macchina virtuale][Processo di protezione della macchina virtuale]

<h2><a id="recoveryplans"></a>Passaggio 8: Test della distribuzione</h2>

Per verificare la distribuzione, è possibile eseguire un failover di test per una singola macchina virtuale. In alternativa, è possibile creare un piano di ripristino comprendente più macchine virtuali ed eseguire un failover di test per tale piano. Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata.
<UL>
<li>Per istruzioni sulla creazione di un piano di ripristino, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=511492">Passaggio 7: Creare e personalizzare piani di ripristino - Da sito locale ad Azure</a>.</li>
<li>Per istruzioni sull'esecuzione di un failover di test, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=511493">Testare una distribuzione da sito locale a sito locale</a>.</li>
</UL>


<h3><a id="runtest"></a>Monitoraggio dell'attività</h3>
<p>È possibile usare le schede <b>Processi</b> e <b>Dashboard</b> per visualizzare e monitorare i principali processi eseguiti dall'insieme di credenziali di Azure Site Recovery, inclusi la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover (pianificato, non pianificato o di test) e il commit di un failover non pianificato.</b>

<p>Nella scheda <b>Processi</b> è possibile visualizzare i processi, analizzare in profondità i dettagli e gli errori, eseguire query per recuperare processi corrispondenti a criteri specifici, esportare processi in Excel e riavviare quelli non riusciti.</p>

<p>Nella scheda <b>Dashboard</b> è possibile scaricare le versioni più recenti dei file di installazione del provider e dell'agente, ottenere informazioni di configurazione per l'insieme di credenziali, visualizzare il numero di macchine virtuali con protezione gestita dall'insieme di credenziali, visualizzare i processi recenti, gestire il certificato dell'insieme di credenziali e risincronizzare le macchine virtuali.</p>

<p>Per altre informazioni sull'interazione con le schede Processi e Dashboard, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Amministrare e monitorare Azure Site Recovery</a>.</p>

	
<h2><a id="next"></a>Passaggi successivi</h2>
<UL>
<LI>Per pianificare e distribuire Azure Site Recovery in un ambiente di produzione completo, vedere la <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Pianificare la distribuzione di Azure Site Recovery</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Distribuire Azure Site Recovery: protezione da sito locale a sito locale</a>.</LI>
<LI>In caso di domande, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=313628">forum sui servizi di ripristino di Azure</a>.</LI> 
</UL>


  [dettagli sui prezzi per Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=378268
  [portale di gestione]: https://manage.windowsazure.com
  [1]: http://go.microsoft.com/fwlink/?LinkId=389880
  [Nuovo insieme di credenziali]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icona Avvio rapido]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Chiave di registrazione]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Prerequisiti]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Aggiornamenti Microsoft]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Impostazioni Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registrazione del server]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Cloud pubblicato]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Configurazione delle impostazioni di protezione]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM]: http://go.microsoft.com/fwlink/?LinkId=323469
  [3]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Abilitare la protezione per le macchine virtuali]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Processo di protezione della macchina virtuale]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
