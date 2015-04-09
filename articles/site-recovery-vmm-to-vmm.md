<properties 
	pageTitle="Configurare la protezione tra siti VMM locali" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali Hyper-V tra siti VMM locali.." 
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
	ms.date="03/19/2015" 
	ms.author="raynew"/>

# Configurare la protezione tra siti VMM locali


## Informazioni generali


Azure Site Recovery favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione. Per un elenco completo degli scenari di distribuzione, vedere [Panoramica di Azure Site Recovery](hyper-v-recovery-manager-overview.md).

Questo scenario descrive come distribuire Site Recovery per gestire e automatizzare la protezione per carichi di lavoro in esecuzione su macchine virtuali su server host Hyper-V situati in cloud privati VMM. In questo scenario le macchine virtuali vengono replicate da un sito VMM primario a un sito VMM secondario usando la replica Hyper-V.

La guida include i prerequisiti per lo scenario e mostra come configurare un insieme di credenziali di Azure Site Recovery, installare il provider di Azure Site Recovery sui server VMM di origine e destinazione, configurare le impostazioni di protezione per i cloud VMM che verranno applicate a tutte le macchine virtuali protette e quindi abilitare la protezione per tali macchine virtuali. Terminare con il test del failover, per accertarsi che tutti gli elementi funzionino come previsto.

Nel caso di problemi di configurazione di questo scenario, inviare le proprie domande al [forum sui Servizi di ripristino di Azure](http://go.microsoft.com/fwlink/?LinkId=313628).


## Prima di iniziare
Assicurarsi che siano rispettati i prerequisiti seguenti:
### Prerequisiti di Azure

- È necessario un account [Microsoft Azure](http://azure.microsoft.com/). Se non si ha un account, iniziare con una [versione di valutazione gratuita](http://aka.ms/try-azure). È anche possibile leggere dei [prezzi di Azure Site Recovery Manager](http://go.microsoft.com/fwlink/?LinkId=378268).
- Per comprendere in che modo vengono usati informazioni e dati, leggere l'[Informativa sulla privacy di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899) e l'ulteriore <a href="#privacy">Informativa sulla privacy per Azure Site Recovery</a> alla fine di questo argomento.

### Prerequisiti di VMM
- Sarà necessario almeno un server VMM.
- Il server VMM deve eseguire almeno System Center 2012 SP1 con gli ultimi aggiornamenti cumulativi. 
- Nei server VMM contenenti macchine virtuali che si desidera proteggere deve essere in esecuzione il provider di Azure Site Recovery. Viene installato durante la distribuzione di Azure Site Recovery.
- Se si vuole configurare la protezione con un singolo server VMM saranno necessari almeno due cloud configurati sul server.
- Se si vuole distribuire la protezione con due server VMM, in ogni server deve essere configurato almeno un cloud nel server VMM primario che si vuole proteggere e un cloud nel server VMM secondario da usare per la protezione e il ripristino.
- Tutti i cloud VMM devono avere il profilo di capacità Hyper-V impostato.
- Il cloud di origine che si vuole proteggere deve includere i seguenti elementi:
	- Uno o più gruppi host VMM.
	- Uno o più server host Hyper-V in ogni gruppo host.
	- Una o più macchine virtuali nel server host. 
- Per altre informazioni sulla configurazione dei cloud VMM:
	- Altre informazioni sui cloud VMM privati sono disponibili nei blog sulle [novità del cloud privato con System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) e [VMM 2012 e i cloud](http://go.microsoft.com/fwlink/?LinkId=324956). 
	- Per altre informazioni, vedere [Configurare l'infrastruttura cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- Quando gli elementi dell'infrastruttura cloud sono presenti, vedere come creare i cloud privati in [Creare un cloud privato in VMM](http://go.microsoft.com/fwlink/?LinkId=324953) e nell'esercitazione[ Creare cloud privati con System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/?LinkId=324954).

### Prerequisiti di Hyper-V

- I server Hyper-V host e di destinazione devono eseguire almeno Windows Server 2012 con ruolo Hyper-V e disporre degli ultimi aggiornamenti installati.
- Se si esegue Hyper-V in un cluster, si noti che il gestore del cluster non viene creato automaticamente se viene usato un cluster basato su indirizzi IP statici. Sarà necessario configurare manualmente il broker del cluster. Per istruzioni, vedere [Configurare il Gestore di replica Hyper-V](http://go.microsoft.com/fwlink/?LinkId=403937).
- Qualsiasi server o cluster Hyper-V per cui si vuole gestire la protezione deve essere incluso in un cloud VMM.
 
### Prerequisiti di mapping di rete
Il mapping di rete garantisce che le macchine virtuali di replica vengano correttamente posizionate su server host Hyper-V dopo il failover e che possano essere connesse a reti VM appropriate. Se invece non si configura la replica del mapping di rete, le macchine virtuali non verranno connesse a reti VM in seguito al processo di failover. Per distribuire il mapping di rete sarà necessario quanto segue:

- Le macchine virtuali che si vuole proteggere nel server VMM di origine devono essere connesse a una rete VM. È necessario che tale rete sia collegata a una rete logica associata al cloud.
- Nel cloud di destinazione nel server VMM secondario usato per il ripristino deve essere configurata una rete VM corrispondente e, a sua volta, deve essere collegata a una rete logica corrispondente associata al cloud di destinazione.

Altre informazioni sul mapping di rete:

- [Configurazione di reti logiche in VMM](http://go.microsoft.com/fwlink/?LinkId=386307)
- [Configurazione di reti VM e gateway in VMM](http://go.microsoft.com/fwlink/?LinkId=386308)

### Prerequisiti del mapping di archiviazione
Per impostazione predefinita, quando si replica una macchina virtuale di un server host Hyper-V di origine in un server host Hyper-V di destinazione, i dati replicati vengono archiviati nel percorso predefinito indicato per l'host Hyper-V di destinazione nella Console di gestione di Hyper-V. Per un maggiore controllo sul percorso di archiviazione dei dati replicati, è possibile configurare il mapping di archiviazione. A tale scopo, prima di iniziare la distribuzione è necessario configurare le classificazioni di archiviazione nei server VMM di origine e di destinazione.
Per istruzioni, vedere [Come creare classificazioni di archiviazione in VMM]( http://go.microsoft.com/fwlink/?LinkId=400937).


## Passaggio 1: Creare un insieme di credenziali per il ripristino del sito

1. Accedere al [portale di gestione](https://portal.azure.com) dal server VMM che si vuole registrare.

2. Espandere **Servizi dati** > **Servizi di ripristino** e fare clic su **Insieme di credenziali di Site Recovery**.


3. Fare clic su **Crea nuovo** > **Creazione rapida**.
	
4. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In **Area** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate vedere [Prezzi di Azure Site Recovery](http://go.microsoft.com/fwlink/?LinkId=389880).</a>

6. Fare clic su **Crea insieme di credenziali**.

	![Create vault](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CreateVault.png)

Controllare nella barra di stato che l'insieme di credenziali sia stato creato. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di Servizi di ripristino.

## Passaggio 2: Generare una chiave di registrazione dell'insieme di credenziali

Generare una chiave di registrazione nell'insieme di credenziali. Dopo aver scaricato il provider di Azure Site Recovery e averlo installato sul server VMM, si userà questa chiave per registrare il server VMM nell'insieme di credenziali.

1. Nella pagina <b>Servizi di ripristino</b> fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Quick Start Icon](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_QuickStartIcon.png)

2. Dall'elenco a discesa selezionare **Tra due siti Hyper-V locali**.
3. In **Prepara server VMM** fare clic sul file **Genera codice di registrazione**. Il file di chiave viene generato automaticamente ed è valido per 5 giorni dal momento in cui viene generato. Se non si accede al portale di Azure dal server VMM sarà necessario copiare questo file sul server. 

	![Registration key](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_E2ERegisterKey.png)
	
## Passaggio 3: Installare il provider di Azure Site Recovery	

1. Nella pagina <b>Avvio rapido</b>, in **Prepara server VMM**, fare clic su <b>Scarica il provider di Microsoft Azure Site Recovery per l'installazione nei server VMM</b> per ottenere la versione più recente del file di installazione del provider.

2. Eseguire il file nei server VMM di origine e di destinazione. Se VMM viene distribuito in un cluster e si installa il provider per la prima volta, installarlo in un nodo attivo nel cluster e completare l'installazione per registrare il server VMM nell'insieme di credenziali. Quindi, installare il provider sugli altri nodi. Si noti che se si sta aggiornando il provider sarà necessario eseguire l'aggiornamento su tutti i nodi, in quanto dovrebbero eseguire tutti la stessa versione del provider.

3. In **Verifica prerequisiti** selezionare l'opzione per l'arresto del servizio VMM per iniziare la configurazione del provider. Il servizio verrà arrestato e riavviato automaticamente al termine dell'installazione. Se si esegue l'installazione in un cluster VMM, verrà richiesto di interrompere il ruolo Cluster.

	![Prerequisites](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderPrereq.png)

4. In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider verranno installati in base ai criteri indicati in Microsoft Update.

	![Microsoft Updates](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderUpdate.png)

Dopo l'installazione del provider, continuare con il programma di installazione per registrare il server nell'insieme di credenziali.

5. Nella pagina Connessione Internet specificare la modalità di connessione a Internet del provider in esecuzione sul server VMM. Selezionare <b>Usa impostazioni proxy del sistema predefinite</b> per usare le impostazioni predefinite di connessione a Internet configurate sul server.

	![Internet Settings](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderProxy.png)

	- Se si vuole usare un server proxy personalizzato, configurarlo prima di installare il provider. Quando si configurano impostazioni proxy personalizzate, verrà eseguito un test per verificare la connessione proxy.
	- Se si usa un proxy personalizzato oppure se il proxy predefinito richiede l'autenticazione, sarà necessario immettere i dettagli del proxy, tra cui l'indirizzo e la porta.
	- È consigliabile impedire ai seguenti indirizzi di eseguire il routing tramite il proxy:
		- L'URL per la connessione ad Azure Site Recovery: *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net 
		- *.store.core.windows.net 
	- Se sono necessarie connessioni in uscita a un controller di dominio di Azure, consentire l'uso degli indirizzi IP descritti in [Intervalli IP dei data center di Azure](http://go.microsoft.com/fwlink/?LinkId=511094) e dei protocolli HTTP (80) e HTTPS (443). 
	- Se si usa un proxy personalizzato, un account RunAs di VMM (DRAProxyAccount) verrà creato automaticamente con le credenziali del proxy specificate. Configurare il server proxy in modo che l'account possa eseguire correttamente l'autenticazione. Le impostazioni dell'account RunAs di VMM possono essere modificate nella console VMM. A tale scopo, aprire l'area di lavoro Impostazioni, espandere Sicurezza, fare clic su Account RunAs, quindi modificare la password di DRAProxyAccount. È necessario riavviare il servizio VMM per rendere effettiva l'impostazione.
6. In **Codice di registrazione** selezionare il codice scaricato da Azure Site Recovery e copiato nel server VMM.
7. In **Nome insieme di credenziali** verificare il nome dell'insieme di credenziali in cui verrà registrato il server.
8. In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali. In una configurazione cluster specificare il nome del ruolo relativo al cluster VMM. 

	![Server registration](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderRegKeyServerName.png)


9. In **Sincronizzazione iniziale metadati cloud** selezionare l'opzione relativa alla sincronizzazione dei metadati di tutti i cloud presenti sul server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM.


7. L'opzione **Crittografia dati** non è pertinente per la protezione da sito locale a sito locale.

	![Server registration](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_ProviderSyncEncrypt.png)

8. Fare clic su <b>Registra</b> per completare il processo. I metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella scheda **Risorse** della pagina **Server** relativa all'insieme di credenziali.

Dopo la registrazione, è possibile modificare le impostazioni del provider nella console VMM o dalla riga di comando.

## Passaggio 4: Configurare le impostazioni di protezione del cloud

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. Se è stata abilitata l'opzione **Sincronizza dati cloud con insieme credenziali** quando è stato installato il provider, tutti i cloud presenti sul server VMM verranno visualizzati nella scheda <b>Elementi protetti</b> dell'insieme di credenziali. In caso contrario, è possibile sincronizzare uno specifico cloud con Azure Site Recovery nella scheda **Generale** nella pagina delle proprietà del cloud nella console VMM.

![Published Cloud](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudsList.png)

1. Nella pagina Avvio rapido fare clic su **Configura la protezione per i cloud VMM**.
2. Nella scheda **Cloud VMM** selezionare il cloud da configurare e passare alla scheda **Configurazione**. 
3. In <b>Destinazione</b> selezionare <b>VMM</b>.
4. In <b>Percorso di destinazione</b> selezionare il server VMM locale che gestisce il cloud da usare per il ripristino.
4. In <b>Cloud di destinazione</b> selezionare il cloud di destinazione da usare per il failover di macchine virtuali nel cloud di origine. Si noti che:
	- È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
	- Un cloud può appartenere solo a una singola coppia di cloud, come cloud primario o di destinazione.
6. In <b>Frequenza di copia</b> specificare la frequenza di sincronizzazione dei dati tra i percorsi di origine e di destinazione. Questa impostazione è rilevante solo quando l'host Hyper-V esegue Windows Server 2012 R2. Per gli altri server viene usata un'impostazione predefinita pari a cinque minuti.
7. In <b>Punti di ripristino aggiuntivi</b> specificare se si vogliono creare punti di ripristino aggiuntivi. Il valore predefinito zero indica che solo il punto di ripristino più recente per una macchina virtuale primaria viene archiviato in un server host di replica. Si noti che per abilitare più punti di ripristino è necessario uno spazio di archiviazione aggiuntivo per gli snapshot archiviati in corrispondenza di ogni punto di ripristino. Per impostazione predefinita, i punti di ripristino vengono creati ogni ora, in modo che in ogni punto siano contenuti dati per un periodo di tempo di un'ora. Il valore del punto di ripristino assegnato alla macchina virtuale nella console VMM non deve essere inferiore al valore assegnato nella console Azure Site Recovery.
8. In <b>Frequenza dell'applicazione - Snapshot coerenti</b> specificare la frequenza di creazione di snapshot coerenti dell'applicazione. Tramite Hyper-V vengono usati due tipi di backup: un backup standard tramite cui viene fornito un backup incrementale dell'intera macchina virtuale e uno snapshot coerente dell'applicazione tramite cui viene creato uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Negli snapshot coerenti dell'applicazione viene usato il servizio Copia Shadow del volume (VSS) per garantire che le applicazioni siano coerenti durante la creazione dello snapshot. Si noti che un'eventuale abilitazione di snapshot coerenti dell'applicazione influirà sulle prestazioni delle applicazioni in esecuzione nelle macchine virtuali di origine. Assicurarsi che il valore impostato sia inferiore al numero di punti di ripristino aggiuntivi configurati.

	![Configure protection settings](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettings.png)

9. In <b>Compressione trasferimento dati</b> specificare se si vogliono comprimere i dati replicati trasferiti. 
10. In <b>Autenticazione</b> specificare come viene autenticato il traffico tra i server host Hyper-V primario e di ripristino. Selezionare HTTPS a meno che sia stato configurato un ambiente Kerberos funzionante. Azure Site Recovery configura automaticamente i certificati per l'autenticazione HTTPS. Non è necessaria alcuna configurazione manuale. Se si seleziona Kerberos, viene usato un ticket Kerberos per l'autenticazione reciproca dei server host. Per impostazione predefinita, le porte 8083 e 8084 (per i certificati) sono aperte in Windows Firewall per i server host Hyper-V. Questa impostazione è rilevante solo per i server host Hyper-V in esecuzione su Windows Server 2012 R2.
11. In <b>Porta</b> modificare il numero di porta sul quale i computer host di origine e di destinazione sono in attesa del traffico di replica. Ad esempio, è possibile modificare l'impostazione se si desidera applicare la limitazione della larghezza di banda di rete QoS (Quality of Service) per il traffico di replica. Verificare che la porta non sia usata da un'altra applicazione e che sia aperta nelle impostazioni del firewall.
12. In <b>Metodo di replica</b> specificare il modo in cui verrà gestita la replica iniziale dei dati dalla posizione di origine a quella di destinazione, prima che la replica normale venga avviata. 
	- <b>Sulla rete</b>: la copia di dati tramite la rete può richiedere molto tempo e numerose risorse. È consigliabile usare questa opzione se il cloud contiene macchine virtuali con dischi rigidi virtuali relativamente piccoli e se il server VMM primario è connesso al server VMM secondario su una connessione a banda larga. È possibile specificare che la copia deve iniziare immediatamente oppure selezionare un orario. Se si usa la replica tramite la rete, è consigliabile pianificarla durante le fasce orarie di minore attività.
	- <b>Offline</b>: tramite questo metodo viene specificato che la replica iniziale sarà eseguita usando supporti esterni. Questa opzione è utile se si desidera evitare un calo delle prestazioni di rete oppure per percorsi remoti a livello geografico. Per usare questo metodo, è necessario specificare la posizione di esportazione nel cloud di origine e quella di importazione nel cloud di destinazione. Quando si abilita la protezione per una macchina virtuale, il disco rigido virtuale viene copiato nel percorso di esportazione specificato. È necessario inviarlo al sito di destinazione e copiarlo nel percorso di importazione. Il sistema copia le informazioni importate nelle macchine virtuali di replica. Per un elenco completo dei prerequisiti per la replica offline, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Passaggio 3: Configurare le impostazioni di protezione per i cloud VMM</a> nella Guida alla distribuzione.
13. Selezionare **Elimina macchina virtuale di replica** per specificare che la replica della macchina virtuale deve essere eliminata se si interrompe la protezione della macchina virtuale stessa selezionando l'opzione **Disabilita protezione per la macchina virtuale** sulla scheda delle proprietà del cloud relativa alle macchine virtuali. Con questa impostazione abilitata, quando si disabilita la protezione la macchina virtuale viene rimossa da Azure Site Recovery, le impostazioni di Site Recovery relative alla macchina virtuale vengono rimosse dalla console VMM e la replica viene eliminata.
	![Configure protection settings](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettingsRep.png)

<p>Dopo aver salvato le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda <b>Processi</b>. Tutti i server host Hyper-V nel cloud di origine VMM verranno configurati per la replica. È possibile modificare le impostazioni del cloud nella scheda <b>Configura</b>. Se si vuole modificare la posizione o il cloud di destinazione, è necessario rimuovere la configurazione del cloud e, successivamente, riconfigurare il cloud.</p>

### Preparare la replica iniziale offline

È necessario effettuare numerose operazioni per preparare la replica iniziale offline:

- Nel server di origine specificare il percorso dal quale verrà eseguita l'esportazione dei dati. Assegnare il controllo completo per NTFS e le autorizzazioni di condivisione al servizio VMM sul percorso di esportazione. Nel server di destinazione specificare il percorso dal quale verrà eseguita l'importazione dei dati. Assegnare le stesse autorizzazioni indicate in precedenza per questo percorso esportazione. 
- Se il percorso importazione o esportazione è condiviso, assegnare l'appartenenza al gruppo Administrator, Power User, Print Operators o Server Operators per l'account del servizio VMM nel computer remoto in cui si trova il percorso condiviso.
- Se si utilizzano account RunAs per aggiungere gli host, nei percorsi di importazione ed esportazione assegnare le autorizzazioni di lettura e scrittura per l'esecuzione agli account RunAs in VMM.
- Le condivisioni di importazione ed esportazione non devono essere posizionate nei computer usati come server host Hyper-V, in quanto la configurazione del loopback non è supportata da Hyper-V.
- In Active Directory, in ogni server host Hyper-V contenente le macchine virtuali che si desidera proteggere, abilitare e configurare la delega vincolata affinché i computer remoti nei quali sono situati i percorsi di esportazione e importazione siano considerati attendibili, come illustrato di seguito:
	1. Nel controller di dominio, aprire **Utenti e computer di Active Directory**.
	2. Nell'albero della console fare clic su **DomainName** > **Computer**.
	3. Fare clic con il pulsante destro del mouse sul nome del server dell'host Hyper-V > **Proprietà**.
	4. Nella scheda **Delega** fare clic su **Computer attendibile per la delega solo ai servizi specificati**.
	5. Selezionare **Usa un qualsiasi protocollo di autenticazione**.
	6. Fare clic su **Aggiungi** > **Utenti e computer**.
	7. Digitare il nome del computer che ospita il percorso di esportazione > **OK**. Nell'elenco di servizi disponibili tenere premuto il tasto CTRL e fare clic su **cifs** > **OK**. Ripetere l'operazione per il nome del computer che ospita il percorso esportazione. Ripetere l'operazione per eventuali altri server host Hyper-V.
	
## Passaggio 5: Configurare il mapping di rete
1. Nella pagina Avvio rapido fare clic su **Mapping reti**.
2. Selezionare il server VMM di origine da cui si desidera eseguire il mapping delle reti, quindi il server VMM di destinazione a cui verrà eseguito il mapping delle reti. Vengono visualizzati l'elenco delle reti di origine e le relative reti di destinazione associate. Viene visualizzato un valore vuoto per le reti che non sono state attualmente sottoposte a mapping. Fare clic sull'icona delle informazioni accanto ai nomi delle reti di origine e di destinazione per visualizzare le subnet per ogni rete.
3. Selezionare una rete in **Rete nell'origine** > **Esegui mapping**. Il servizio rileva le reti VM nel server di destinazione e le visualizza. 

	![Configure network mapping](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping1.png)

4. Nella finestra di dialogo selezionare una delle reti VM dal server VMM di destinazione.

	![Select a target network](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping2.png)

5. Quando si seleziona una rete di destinazione, vengono visualizzati i cloud protetti in cui viene usata la rete di origine. Vengono visualizzate anche le reti di destinazione disponibili associate ai cloud usati per la protezione. È consigliabile selezionare una rete di destinazione disponibile per tutti i cloud che vengono usati per la protezione.
6. Fare clic sul segno di spunta per completare il processo di mapping. Un processo inizierà a tenere traccia dell'avanzamento del mapping, È possibile visualizzarlo nella scheda **Processi**.


## Passaggio 6: Configurare il mapping di archiviazione
Per impostazione predefinita, quando si replica una macchina virtuale di un server host Hyper-V di origine in un server host Hyper-V di destinazione, i dati replicati vengono archiviati nel percorso predefinito indicato per l'host Hyper-V di destinazione nella Console di gestione di Hyper-V. Per un maggiore controllo della destinazione dell'archiviazione dei dati replicati, è possibile configurare i mapping di archiviazione nel seguente modo:

- Definire le classificazioni di archiviazione nei server VMM di origine e di destinazione. Per istruzioni, vedere [Come creare classificazioni di archiviazione in VMM]( http://go.microsoft.com/fwlink/?LinkId=400937). Le classificazioni devono essere disponibili per i server host Hyper-V nei cloud di origine e di destinazione. Le classificazioni non devono necessariamente avere lo stesso tipo di archiviazione. È possibile ad esempio mappare una classificazione di origine contenente condivisioni SMB a una classificazione di destinazione contenente CSV.
- Dopo la configurazione delle classificazioni, è possibile creare i mapping.
1. Nella pagina **Avvio rapido** fare clic su **Mapping archiviazione**.
1. Fare clic sulla scheda **Archiviazione** > **Mapping classificazioni di archiviazione**.
1. Nella scheda **Mappa classificazioni di archiviazione** selezionare le classificazioni nei server VMM di origine e di destinazione. Salvare le impostazioni.

	![Select a target network](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_StorageMapping1.png)


## Passaggio 7: Abilitare la protezione delle macchine virtuali
Dopo la configurazione corretta di server, cloud e reti, sarà possibile abilitare la protezione per le macchine virtuali nel cloud.

1. Nella scheda **Macchine virtuali** nel cloud in cui si trova la macchina virtuale fare clic su **Abilita protezione** > **Aggiungi macchine virtuali**. 
2. Nell'elenco di macchine virtuali nel cloud selezionare quella da proteggere.


![Enable virtual machine protection](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_EnableProtectionVM.png)

Tenere traccia dell'avanzamento dell'azione di abilitazione della protezione, inclusa la replica iniziale, nella scheda **Processi**. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover. Al termine dell'operazione di abilitazione della protezione e di replica delle macchine virtuali, sarà possibile visualizzarle in Azure.

Si noti che è anche possibile abilitare la protezione per le macchine virtuali nella console VMM. Fare clic su Attiva protezione sulla barra degli strumenti nella scheda Azure Site Recovery nelle proprietà della macchina virtuale,


![Virtual machine protection job](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_VMJobs.png)

### Caricare le macchine virtuali esistenti
Se si dispone di macchine virtuali esistenti in VMM replicate attualmente tramite la replica Hyper-V, è necessario caricarle nella protezione di Azure Site Recovery.
1. Verificare di avere cloud primari e secondari. Assicurarsi che il server Hyper-V che ospita la macchina virtuale esistente si trovi nel cloud primario e che il server Hyper-V che ospita la macchina virtuale di replica si trovi nel cloud secondario. Verificare di aver configurato le impostazioni di protezione per i cloud. Le impostazioni devono corrispondere a quelle configurate attualmente per la replica Hyper-V. In caso contrario, la replica delle macchine virtuali potrebbe non funzionare come previsto.
2. Abilitare la protezione per la macchina virtuale primaria. Azure Site Recovery e VMM garantiscono che vengano rilevati la stessa macchina virtuale e lo stesso host di replica. Azure Site Recovery riuserà e ristabilirà la replica mediante le impostazioni configurate durante la configurazione del cloud.


## Testare la distribuzione

Per testare la distribuzione è possibile eseguire un failover di test per una singola macchina virtuale oppure creare un piano di ripristino costituito da più macchine virtuali ed eseguire un failover di test per il piano.  Il failover di test consente di simulare il meccanismo di failover e di ripristino in una rete isolata.

### Creare un piano di ripristino

1. Nella scheda **Piani di ripristino** fare clic su **Creare il piano di ripristino**.
2. Specificare un nome per il piano di ripristino e per i server VMM di origine e di destinazione. Sul server di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino. Selezionare **Hyper-V** per visualizzare solo i cloud configurati per la replica Hyper-V.

	![Create recovery plan](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP1.png)

3. In **Seleziona macchine virtuali** selezionare i gruppi di replica. Tutte le macchine virtuali associate al gruppo di replica verranno selezionate e aggiunte al piano di ripristino. In questa esercitazione, le macchine virtuali vengono aggiunte al gruppo predefinito del piano di ripristino Gruppo 1. Se necessario, è possibile aggiungere altri gruppi. Al termine della replica, le macchine virtuali verranno avviate in base all'ordine dei gruppi del piano di ripristino.

	![Add virtual machines](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP2.png)	

Una volta creato, il piano di ripristino verrà visualizzato nell'elenco all'interno della scheda **Piani di ripristino**. 

###Eseguire un failover di test

1. Nella scheda **Piani di ripristino** selezionare il piano e fare clic su **Failover di test**.
2. Nella pagina **Conferma failover di test** selezionare **Nessuno**. Se si abilita questa opzione, le macchine virtuali di replica su cui è stato eseguito il failover non verranno connesse in rete. Il test, infatti, controlla che il failover delle macchine virtuali venga eseguito correttamente ma non verifica l'ambiente di rete di replica. Se si vuole eseguire un failover di test più completo, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testare una distribuzione locale su MSDN</a>.

	![Select test network](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_TestFailover1.png)


7. La macchina virtuale di test viene creata sullo stesso host in cui è presente la macchina virtuale di replica, ma non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.

### Eseguire un piano di ripristino
Al termine del processo di replica, alla macchina virtuale di replica verrà assegnato un indirizzo IP diverso dall'indirizzo IP della macchina virtuale primaria. Se gli indirizzi vengono emessi da un protocollo DHCP, il DNS verrà aggiornato automaticamente. Se invece non si usa il protocollo DHCP e si desidera indirizzi IP identici, sarà necessario eseguire alcuni script.

#### Script per il recupero dell'indirizzo IP
Eseguire il seguente script di esempio per recuperare l'indirizzo IP.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  

#### Script per l'aggiornamento del DNS
Eseguire il seguente script di esempio per aggiornare il DNS e specificare l'indirizzo IP recuperato mediante il precedente script di esempio.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**



<a name="privacy"></a><h2>Informativa sulla privacy per Site Recovery</h2>

Questa sezione fornisce altre informazioni sulla privacy per il servizio Site Recovery di Microsoft Azure ("Servizio"). Per visualizzare l'informativa sulla privacy per i servizi Microsoft Azure, vedere l'
[Informativa sulla privacy di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Funzionalità: Registrazione**

- **Risultato**: Registra il server nel servizio in modo da proteggere le macchine virtuali
- **Informazioni raccolte**: Dopo la registrazione il Servizio raccoglie, elabora e trasmette le informazioni sul certificato di gestione dal server VMM designato per fornire il ripristino di emergenza usando il nome del Servizio del server VMM e il nome dei cloud della macchina virtuale sul server VMM.
- **Uso delle informazioni**: 
	- Certificato di gestione: viene usato per identificare e autenticare il server VMM registrato per accedere al Servizio. Il Servizio usa la parte della chiave pubblica del certificato per fornire un token a cui solo il server VMM registrato può accedere. Il server dovrà usare questo token per accedere alle funzionalità del Servizio.
	- Nome del server VMM: il nome del server VMM è necessario per identificare e comunicare con il server VMM appropriato in cui si trovano i cloud.
	- Nomi dei cloud dal server VMM: il nome del cloud è obbligatorio quando si usa la funzionalità di associazione/annullamento associazione descritta di seguito. Quando si decide di associare il cloud da un data center principale con un altro cloud nel data center di ripristino, vengono presentati i nomi di tutti i cloud dal data center di ripristino.

- **Scelta**: queste informazioni sono una parte essenziale del processo di registrazione del Servizio perché consentono all'utente e al Servizio di identificare il server VMM per il quale si desidera garantire la protezione di Azure Site Recovery e di identificare il server VMM registrato corretto. Se non si vogliono inviare queste informazioni al Servizio, non usare questo Servizio. Se si registra il server e successivamente si desidera annullare la registrazione, è possibile farlo eliminando le informazioni sul server VMM dal portale del Servizio, ovvero il portale di Azure.

**Funzionalità: Abilitare la protezione di Azure Site Recovery**

- **Risultato**: Il provider di Azure Site Recovery installato nel server VMM è il canale per le comunicazioni con il Servizio. Il provider è una DLL (Dynamic Link Library, libreria di collegamento dinamico) ospitata nel processo VMM. Al termine dell'installazione del provider, la funzionalità "Datacenter Recovery" viene abilitata nella Console di amministrazione VMM. In tutte le macchine virtuali nuove o esistenti in un cloud è possibile abilitare una proprietà denominata "Datacenter Recovery" per garantire la protezione della macchina virtuale. Dopo che questa proprietà è stata impostata, il provider invia il nome e l'ID della macchina virtuale al Servizio. La protezione virtuale basata sulla tecnologia di replica Hyper-V disponibile in Windows Server 2012 o in Windows Server 2012 R2. I dati delle macchine virtuali vengono replicati da un host Hyper-V a un altro, generalmente situato in un data center di "ripristino" diverso.

- **Informazioni raccolte**: Il Servizio raccoglie, elabora e trasmette i metadati per la macchina virtuale, che includono nome, ID, rete virtuale e nome del cloud 
a cui appartiene.

- **Uso delle informazioni**: Il Servizio usa i dati citati sopra per popolare le informazioni della macchina virtuale nel portale del Servizio.

- **Scelta**: Poiché si tratta di una parte essenziale del servizio, questa funzionalità non può essere disabilitata. Se non si vuole che queste informazioni vengano inviate al Servizio, non abilitare la protezione di Azure Site Recovery per le macchine virtuali. Si noti che tutti i dati inviati dal provider al Servizio vengono trasmessi tramite HTTPS.

**Funzionalità: Piano di ripristino**

- **Risultato**: Questa funzionalità consente di creare un piano di orchestrazione per il data center di "ripristino". È possibile definire l'ordine con cui le macchine virtuali o un gruppo di macchine virtuali deve essere avviato presso il sito di ripristino. È inoltre possibile specificare l'esecuzione di script automatizzati o di azioni manuali al momento del ripristino per ogni macchina virtuale. Il failover (illustrato nella sezione successiva) viene in genere attivato a livello del piano di ripristino per un ripristino coordinato.

- **Informazioni raccolte**: il Servizio raccoglie, elabora e trasmette i metadati per il piano di ripristino, inclusi i metadati della macchina virtuale e i metadati di qualsiasi script di automazione e nota di azione manuale.

- **Uso delle informazioni**: I metadati descritti in precedenza sono usati per creare il piano di ripristino nel portale del Servizio.

- **Scelta**: Poiché si tratta di una parte essenziale del servizio, questa funzionalità non può essere disabilitata. Se non si vogliono inviare queste informazioni al Servizio, non creare piani di ripristino in questo Servizio.

**Funzionalità: Mapping di rete**

- **Risultato**: Questa funzionalità consente di eseguire il mapping delle informazioni di rete dal data center principale al data center di ripristino. Quando le macchine virtuali sono ripristinate presso il sito di ripristino, il mapping consente di ristabilire la connettività di rete per esse.

- **Informazioni raccolte**: Nell'ambito della funzionalità di mapping di rete, il Servizio raccoglie, elabora e trasmette i metadati delle reti logiche per ogni sito (primario e data center).

- **Uso delle informazioni**: il Servizio usa i metadati per popolare il portale del Servizio in cui è possibile eseguire il mapping delle informazioni di rete.

- **Scelta**: Poiché si tratta di una parte essenziale del Servizio, questa funzionalità non può essere disabilitata. Se non si desidera che queste informazioni vengano inviate al Servizio, non usare la funzionalità di mapping di rete.

**Funzionalità: Failover (pianificato, non pianificato e di test)**

- **Risultato**: Questa funzionalità consente di eseguire il failover di una macchina virtuale da un data center gestito VMM a un altro data center gestito VMM. L'azione di failover viene attivata dall'utente nel portale del Servizio. Alcune possibili cause di failover possono essere un evento non pianificato (ad esempio, una calamità naturale), un evento pianificato (ad esempio, il bilanciamento del carico di un data center) e il failover di test (ad esempio la prova di un piano di ripristino).

Il provider nel server VMM riceve la notifica dell'evento dal Servizio ed esegue un'azione di failover sull'host Hyper-V tramite le interfacce VMM. Il failover effettivo della macchina virtuale da un host Hyper-V a un altro (generalmente in esecuzione in un data center di "ripristino" diverso) è gestita dalla tecnologia di replica Hyper-V disponibile in Windows Server 2012 o Windows Server 2012 R2. Al termine del failover, il provider installato nel server VMM del data center di "ripristino" invia al Servizio le informazioni relative all'esito positivo dell'operazione.

- **Informazioni raccolte**: Il Servizio usa i dati citati sopra per popolare le informazioni relative allo stato dell'azione di failover nel portale del Servizio.

- **Uso delle informazioni**: Il Servizio usa le informazioni citate sopra nel modo seguente:

	- Certificato di gestione: viene usato per identificare e autenticare il server VMM registrato per accedere al Servizio. Il Servizio usa la parte della chiave pubblica del certificato per fornire un token a cui solo il server VMM registrato può accedere. Il server dovrà usare questo token per accedere alle funzionalità del Servizio.
	- Nome del server VMM: il nome del server VMM è necessario per identificare e comunicare con il server VMM appropriato in cui si trovano i cloud.
	- Nomi dei cloud dal server VMM: il nome del cloud è obbligatorio quando si usa la funzionalità di associazione/annullamento associazione descritta di seguito. Quando si decide di associare il cloud da un data center principale con un altro cloud nel data center di ripristino, vengono presentati i nomi di tutti i cloud dal data center di ripristino.

- **Scelta**: Poiché si tratta di una parte essenziale del servizio, questa funzionalità non può essere disabilitata. Se non si vogliono inviare queste informazioni al Servizio, non usare questo Servizio.


<!--HONumber=49-->