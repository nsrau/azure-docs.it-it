<properties 
	pageTitle="Configurare la protezione tra siti VMM locali con SAN" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali Hyper-V tra siti locali usando la replica SAN." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="raynew"/>

# Configurare la protezione tra siti VMM locali con SAN

Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. Per informazioni sui possibili scenari di distribuzione consultare [Panoramica di Site Recovery di Azure](site-recovery-overview.md).

Questa procedura dettagliata illustra come distribuire Site Recovery per gestire e automatizzare la protezione per carichi di lavoro di macchine virtuali in esecuzione su un server Hyper-V locale gestito da System Center VMM in un altro sito VMM locale, tramite la replica SAN basata su un array di archiviazione.

I vantaggi aziendali includono:

- Fornire una soluzione di replica scalabile aziendale automatizzata mediante Site Recovery.
- Sfruttare le funzionalità di replica SAN fornite dai partner di archiviazione aziendali attraverso fibre channel e archiviazione iSCSI. Vedere [partner di archiviazione SAN](http://go.microsoft.com/fwlink/?LinkId=518669).
- Sfruttare l'infrastruttura SAN esistente per proteggere le applicazioni di importanza strategica distribuite in cluster Hyper-V. 
- Fornire supporto per i cluster guest.
- Garantire la replica coerente tra i diversi livelli di un'applicazione mediante una replica sincronizzata con valori RTO e RPO bassi e una replica non sincronizzata per elevata flessibilità, in base alle caratteristiche dell'array di archiviazione.  
- L'integrazione con VMM fornisce la gestione SAN all'interno della console VMM e SMI-S in VMM individua lo spazio di archiviazione esistente.  


## Informazioni sull’articolo

Questo articolo include una panoramica e i prerequisiti per la distribuzione. Descrive la configurazione e abilita la replica in VMM e nell'insieme di credenziali di Site Recovery. Si individuerà e si classificherà l'archiviazione SAN in VMM, si eseguirà il provisioning di unità logiche e si allocherà spazio di archiviazione a cluster Hyper-V. Si conclude con il test del failover, per accertarsi che tutti gli elementi funzionino come previsto.

Nel caso di problemi, inviare domande al [forum sui Servizi di ripristino di Azure](http://go.microsoft.com/fwlink/?LinkId=313628).

## Panoramica
Questo scenario consente di proteggere i carichi di lavoro tramite il backup di macchine virtuali Hyper-V da un sito VMM locale a un altro tramite la replica SAN.

![Architettura SAN](./media/site-recovery-vmm-san/ASRSAN_Arch.png)

### Componenti dello scenario

- **Macchine virtuali locali**: i server Hyper-V locali gestiti nei cloud privati VMM contengono le macchine virtuali da proteggere.
- **Server VMM locali**: è possibile fare in modo che uno o più server VMM siano in esecuzione nel sito primario che si vuole proteggere e nel sito secondario.
- **Archiviazione SAN**: un array SAN nel sito primario e uno nel sito secondario
-  **Insieme di credenziali di Azure Site Recovery**: l'insieme di credenziali coordina e orchestra la replica, il failover e il ripristino dei dati tra i siti locali.
- **Provider di Azure Site Recovery**: il provider è installato in ciascun server VMM.

## Prima di iniziare

### Prerequisiti di Azure

- È necessario un account [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](http://aka.ms/try-azure). Ottenere [informazioni dettagliate sui prezzi di Azure Site Recovery Manager](http://go.microsoft.com/fwlink/?LinkId=378268).

### Prerequisiti di VMM

- In ciascun sito locale è necessario almeno un server VMM, distribuito come server autonomo, fisico o virtuale, o come cluster virtuale, eseguito su System Center 2012 R2 con [aggiornamento cumulativo 5.0 per VMM](http://go.microsoft.com/fwlink/?LinkId=517707).
- È necessario disporre di almeno un cloud sul server VMM primario che si desidera proteggere e uno sul server VMM secondario. Il cloud primario da proteggere deve includere quanto segue:
	- Uno o più gruppi host VMM
	- Uno o più cluster Hyper-V in ogni gruppo host.
	- Una o più macchine virtuali situate nel server Hyper-V di origine nel cloud.
		
### Requisiti Hyper-V

- È necessario un cluster host Hyper-V distribuito sia nel sito primario sia in quello secondario, che esegua almeno Windows Server 2012 con gli aggiornamenti più recenti.

### Prerequisiti SAN

- La replica SAN consente di replicare macchine virtuali in cluster guest con l'archiviazione iSCSI o Fibre Channel o usando dischi rigidi virtuali condivisi \(vhdx\). Di seguito sono elencati i prerequisiti SAN:
	- Sono necessari due array configurati, uno nel sito primario, l'altro nel sito secondario.
	- L'infrastruttura di rete deve essere configurata tra array. Devono essere configurati processi di peering e di replica. Le licenze di replica devono essere configurate in base ai requisiti dell'array di archiviazione.
	- Devono essere configurate connessioni di rete tra i server host Hyper-V e l'array di archiviazione, in modo che gli host possano comunicare con le unità logiche di archiviazione tramite ISCSI o Fibre Channel.
	- Vedere l'elenco degli [array di archiviazione supportati](http://go.microsoft.com/fwlink/?LinkId=518669).
	- I provider SMI-S, forniti dai produttori degli array di archiviazione, devono essere installati e gli array SAN devono essere gestiti dal provider. Configurare il provider in base alla relativa documentazione.
	- Accertarsi che il provider SMI-S per l'array si trovi su un server a cui il server VMM può accedere tramite rete con un indirizzo IP o un nome di dominio completo \(FQDN\).
	- In ogni array SAN devono essere disponibili uno o più pool di archiviazione da utilizzare in questa distribuzione. Il server VMM nel sito primario dovrà gestire l'array primario e il server VMM secondario gestirà l'array secondario.
	- Il server VMM nel sito primario dovrà gestire l'array primario, mentre il server VMM secondario dovrà gestire l'array secondario.

### Prerequisiti di rete

È possibile configurare il mapping di rete per verificare che le macchine virtuali di replica siano correttamente posizionate su server host Hyper-V dopo il failover e che possano essere connesse a reti VM appropriate \(facoltativo\). Si noti che:

- Se è abilitato il mapping di rete, una macchina virtuale presente nella posizione principale verrà connessa a una rete e la replica della macchina virtuale nella posizione di destinazione verrà connessa alla rete mappata.
- Se invece non si configura il mapping di rete, le macchine virtuali non verranno connesse a reti VM in seguito al processo di failover.
- Le reti VM devono essere configurate in VMM. Per ulteriori informazioni, consultare Configurazione di reti VM e gateway in VMM
- Le macchine virtuali nel server VMM di origine devono essere connesse a una rete VM. È necessario che la rete VM di origine sia collegata a una rete logica associata al cloud.


## Passaggio 1: preparare l'infrastruttura VMM

Per preparare l'infrastruttura VMM è necessario:

1. Verificare che i cloud VMM siano configurati
2. Integrare e classificare l'archiviazione SAN in VMM
3. Creare unità logiche e allocare spazio di archiviazione.
4. Creare gruppi di replica
5. Configurare le reti VM

### Verificare che i cloud VMM siano configurati

Site Recovery per orchestra la protezione per le macchine virtuali in server host Hyper-V locali ubicati in cloud VMM. È necessario assicurarsi che tali cloud siano configurati correttamente prima di iniziare la distribuzione di Site Recovery. Tra le origini utili sono inclusi:

- [Novità nel cloud privato](http://go.microsoft.com/fwlink/?LinkId=324952)
- [VMM 2012 e i cloud](http://go.microsoft.com/fwlink/?LinkId=324956) sul blog di Gunter Danzeisen.
- [Configurazione dell'infrastruttura cloud VMM](https://msdn.microsoft.com/library/azure/dn883636.aspx#BKMK_Fabric)
- [Creazione di un cloud privato in VMM](http://go.microsoft.com/fwlink/?LinkId=324953)
- [Procedura dettagliata: creazione di cloud privati](http://go.microsoft.com/fwlink/?LinkId=324954) nel blog di Keith Mayer.

### Integrare e classificare l'archiviazione SAN in VMM


Aggiungere e classificazione SAN nella console VMM:

1. Nell'area di lavoro **Infrastruttura** fare clic su **Archiviazione**. Fare clic su **Home page** \> **Aggiungi risorse** \> **Dispositivi di archiviazione** per avviare l'Aggiunta guidata dispositivi di archiviazione.
2. Nella pagina **Seleziona un tipo di provider di archiviazione** selezionare **Dispositivi SAN e NAS individuati e gestiti da un provider SMI-S**.

	![Tipo di provider](./media/site-recovery-vmm-san/SRSAN_Providertype.png)

3. Nella pagina **Specifica il protocollo e l'indirizzo del provider SMI-S di archiviazione** selezionare **SMI-S CIMXML** e specificare le impostazioni per la connessione al provider.
4. Nei campi **FQDN o indirizzo IP del provider** e **Porta TCP/IP** specificare le impostazioni per la connessione al provider. È possibile usare una connessione SSL solo per SMI-S CIMXML.

	![Connessione provider](./media/site-recovery-vmm-san/SRSAN_ConnectSettings.png)

5. In **Account RunAs** specificare un account VMM RunAs che possa accedere al provider o creare un nuovo account.
6. VMM tenta di individuare e importare automaticamente nella pagina **Raccolta informazioni** alcune informazioni sul dispositivo di archiviazione. Per provare a recuperare altre informazioni fare clic su **Analizza provider**. Se il tentativo ha esito positivo, nella pagina vengono elencati gli array di archiviazione individuati, i pool di archiviazione, il produttore, il modello e la capacità.

	![Individua l'archiviazione](./media/site-recovery-vmm-san/SRSAN_Discover.png)

7. In **Selezionare i pool di archiviazione da gestire e assegnare una classificazione** selezionare i pool di archiviazione che dovranno essere gestiti da VMM e assegnare loro una classificazione. Dai pool di archiviazione verranno importate informazioni sulle unità logiche. Creare unità logiche in base alle applicazioni da proteggere, ai relativi requisiti di capacità e ai requisiti sugli elementi da replicare insieme.

	![Classifica l’archiviazione](./media/site-recovery-vmm-san/SRSAN_Classify.png)

### Creare unità logiche e allocare spazio di archiviazione.

1. Dopo aver integrato l'archiviazione SAN in VMM, eseguire il provisioning di \(ovvero creare\) unità logiche \(LUN\).

- [Come selezionare un metodo per la creazione di unità logiche in VMM](http://go.microsoft.com/fwlink/?LinkId=518490)
- [Come eseguire il provisioning di unità logiche di archiviazione in VMM](http://go.microsoft.com/fwlink/?LinkId=518491)

2. Allocare quindi capacità di archiviazione al cluster host Hyper-V, in modo che VMM possa distribuire i dati delle macchine virtuali nell'archiviazione su cui è stato eseguito il provisioning: 

	- Prima di allocare capacità di archiviazione al cluster, è necessario allocare capacità di archiviazione al gruppo host VMM in cui risiede il cluster. Vedere [Modalità di allocazione delle unità logiche di archiviazione a un gruppo Host](http://go.microsoft.com/fwlink/?LinkId=518493) e [Modalità di allocazione di pool di archiviazione a un gruppo Host](http://go.microsoft.com/fwlink/?LinkId=518492).</a>
	- Allocare la capacità di archiviazione al cluster, come descritto in [Come configurare l'archiviazione in un cluster host Hyper-V in VMM](http://go.microsoft.com/fwlink/?LinkId=513017).</a>.

### Creare gruppi di replica

Creare un gruppo di replica contenente tutte le unità logiche che dovranno essere replicate insieme.

1. Nella console VMM aprire la scheda **Gruppi di replica** nelle proprietà dell'array di archiviazione e fare clic su **Nuovo**.
2. Quindi creare il gruppo di replica.

	![Gruppo di replica SAN](./media/site-recovery-vmm-san/SRSAN_RepGroup.png)

### Configurare le reti

Se si desidera configurare il mapping di rete procedere come segue:

1. Leggere le informazioni su [Mapping di rete](https://msdn.microsoft.com/library/azure/dn801052.aspx).
2. Preparare le reti VM in VMM:
 
	- Leggere le informazioni su [Configurazione di reti logiche](http://go.microsoft.com/fwlink/?LinkId=386307). Configurare le reti logiche: leggere Configurazione di reti logiche in Panoramica su VMM.
	- [Configurare le reti VM](http://go.microsoft.com/fwlink/?LinkId=386308).

## Passaggio 2: creare un insieme di credenziali


1. Accedere al [portale di gestione](https://portal.azure.com).


2. Espandere **Servizi dati** \> **Servizi di ripristino** e fare clic su **Insieme di credenziali di Site Recovery**.


3. Fare clic su **Crea nuovo** \> **Creazione rapida**.
	
4. In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In **Region** selezionare l'area geografica per l'insieme di credenziali. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](href="http://go.microsoft.com/fwlink/?LinkId=389880).

6. Fare clic su **Create vault**.

	![New Vault](./media/site-recovery-vmm-san/SRSAN_HvVault.png)

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di **Servizi di ripristino**.


### Registrare i server VMM

1. Nella pagina <b>Servizi di ripristino</b> fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Quick Start Icon](./media/site-recovery-vmm-san/SRSAN_QuickStartIcon.png)

2. Dall'elenco a discesa selezionare **Tra siti Hyper-V locali tramite la replica di array**.

	![Registration key](./media/site-recovery-vmm-san/SRSAN_QuickStartRegKey.png)

3. In **Prepara server VMM** fare clic sul file **Genera codice di registrazione**. Il codice è valido per 5 giorni dal momento in cui viene generato. Se non si utilizza la console sul server VMM che si desidera proteggere, scaricare il file in una posizione sicura a cui il server VMM può accedere, ad esempio in una condivisione. Si noti che:

- Se si installa il provider per la prima volta, installarlo in un nodo attivo nel cluster e completare l'installazione per registrare il server VMM nell'insieme di credenziali di Azure Site Recovery. Quindi, installare il provider sugli altri nodi del cluster.
- Se si sta aggiornando la versione del provider, eseguire l'installazione del provider in tutti i nodi del cluster.

4. Fare clic su **Scarica il provider di Microsoft Azure Site Recovery per l'installazione nei server VMM** per ottenere la versione più recente del file di installazione del provider.
5. Eseguire il file nei server VMM di origine e di destinazione per aprire la procedura guidata di installazione del provider di Azure Site Recovery.
6. Nella pagina **Verifica prerequisiti** selezionare l'opzione per l'arresto del servizio VMM per iniziare la configurazione del provider. Il servizio verrà arrestato e riavviato automaticamente al termine dell'installazione. Se si esegue l'installazione in un cluster VMM, sarà necessario interrompere il ruolo Cluster.

	![Prerequisiti](./media/site-recovery-vmm-san/SRSAN_ProviderPrereq.png)

5. In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider verranno installati in base ai criteri indicati in Microsoft Update.

Dopo l'installazione del provider, continuare con il programma di installazione per registrare il server nell'insieme di credenziali.

6. Nella pagina Connessione Internet specificare la modalità di connessione a Internet del provider in esecuzione nel server VMM. È possibile selezionare l’opzione per non usare un proxy, per usare il proxy predefinito configurato nel server VMM o per usare un server proxy personalizzato. Si noti che:

	- Se si vuole usare un server proxy personalizzato, configurarlo prima di installare il provider.
	- Gli URL seguenti dovranno essere accessibili dal server VMM
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net 
		- *.backup.windowsazure.com 
		- *.blob.core.windows.net 
		- *.store.core.windows.net 
	- Consentire gli indirizzi IP descritti in [Intervalli IP dei data center di Azure](http://go.microsoft.com/fwlink/?LinkId=511094) e il protocollo HTTPS \(443\). È necessario aggiungere all'elenco di indirizzi consentiti gli IP dell'area Azure che si prevede di utilizzare e quello degli Stati Uniti occidentali. 
	
	- Se si sceglie di usare un proxy personalizzato, un account RunAs di VMM \(DRAProxyAccount\) verrà creato automaticamente con le credenziali del proxy specificate. Configurare il server proxy in modo che l'account possa eseguire correttamente l'autenticazione.
	- Le impostazioni dell'account RunAs di VMM possono essere modificate nella console VMM. A tale scopo, aprire l'area di lavoro Impostazioni, espandere Sicurezza, fare clic su Account RunAs, quindi modificare la password di DRAProxyAccount. È necessario riavviare il servizio VMM per rendere effettiva l'impostazione.
	- Verrà eseguito un test per verificare la connessione a Internet. Eventuali errori del proxy vengono visualizzati nella console VMM.

	![Internet Settings](./media/site-recovery-vmm-san/SRSAN_ProviderProxy.png)

7. In **Registration Key** selezionare il codice di registrazione scaricato da Azure Site Recovery e copiato nel server VMM.
8. In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali.

	![Server registration](./media/site-recovery-vmm-san/SRSAN_ProviderRegKeyServerName.png)

9. In **Initial cloud metadata** selezionare l'opzione relativa alla sincronizzazione dei metadati per tutti i cloud presenti sul server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM. L'opzione **Crittografia dati** non è rilevante in questo scenario.

	![Server registration](./media/site-recovery-vmm-san/SRSAN_ProviderSyncEncrypt.png)

11. Nella pagina successiva, fare clic su **Registra** per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella scheda <b>Risorse</b> della pagina **Server** relativa all'insieme di credenziali. Dopo aver installato il provider, è possibile modificarne le impostazioni nella console VMM.


## Passaggio 4: eseguire il mapping di array e pool di archiviazione

Eseguire il mapping degli array per specificare il pool di archiviazione secondario che dovrà ricevere i dati di replica dal pool primario. Il mapping della risorsa di archiviazione deve essere eseguito prima di configurare la protezione del cloud poiché le informazioni di mapping vengono usate quando si abilita la protezione per i gruppi di replica.

Prima di iniziare, verificare che i cloud siano visibili nell'insieme di credenziali. I cloud possono essere identificati scegliendo di sincronizzare tutti i cloud durante l'installazione del provider oppure selezionando la sincronizzazione di un cloud specifico nella scheda **Generale** delle proprietà del cloud nella console VMM. Eseguire quindi il mapping degli array di archiviazione come segue:

1. Fare clic su **Risorse** \> **Archiviazione server** \> **Mappa array di origine e di destinazione**. ![Server registration](./media/site-recovery-vmm-san/SRSAN_StorageMap.png)
2. Selezionare gli array di archiviazione presenti nel sito primario e mapparli agli array di archiviazione del sito secondario.
3.  Eseguire quindi il mapping dei pool di archiviazione di origine e di archiviazione all'interno degli array. A tale scopo, in **Pool di archiviazione** selezionare un pool di archiviazione di origine e uno di destinazione di cui eseguire il mapping.

	![Server registration](./media/site-recovery-vmm-san/SRSAN_StorageMapPool.png)

## Passaggio 5: configurare le impostazioni di protezione del cloud

Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. Quando si installa il provider, abilitare l'opzione **Sincronizza dati cloud con insieme credenziali** in modo che tutti i cloud presenti sul server VMM vengano visualizzati nella scheda <b>Elementi protetti</b> dell'insieme di credenziali.

![Cloud pubblicato](./media/site-recovery-vmm-san/SRSAN_CloudsList.png)

1. Nella pagina Avvio rapido fare clic su **Configurare la protezione per i cloud VMM**.
2. Nella scheda **Elementi protetti** selezionare il cloud da configurare, quindi passare alla scheda **Configurazione**. Si noti che:
3. In <b>Destinazione</b> selezionare <b>VMM</b>.
4. In <b>Percorso di destinazione</b> selezionare il server VMM locale che gestisce il cloud da usare per il ripristino.
5. In <b>Cloud di destinazione</b> selezionare il cloud di destinazione da usare per il failover di macchine virtuali nel cloud di origine. Si noti che:
	- È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
	- Un cloud può appartenere solo a una singola coppia di cloud, come cloud primario o di destinazione.
6. Azure Site Recovery verifica che i cloud abbiano accesso all'archiviazione abilitata per la replica SAN e che sia stato eseguito il peering degli array di archiviazione. Vengono visualizzati i peer degli array partecipanti.
7. Se la verifica ha esito positivo, in **Tipo di replica** selezionare **SAN**.

<p>Dopo avere salvato le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda <b>Processi</b>. È possibile modificare le impostazioni del cloud nella scheda <b>Configure</b>. Se si vuole modificare la posizione o il cloud di destinazione, è necessario rimuovere la configurazione del cloud e, successivamente, riconfigurare il cloud.</p>

## Passaggio 6: abilitare il mapping di rete

1. Nella pagina Avvio rapido fare clic su **Mapping reti**.
2. Selezionare il server VMM di origine da cui si desidera eseguire il mapping delle reti, quindi il server VMM di destinazione a cui verrà eseguito il mapping delle reti. Vengono visualizzati l'elenco delle reti di origine e le relative reti di destinazione associate. Viene visualizzato un valore vuoto per le reti che non sono state attualmente sottoposte a mapping. Fare clic sull'icona delle informazioni accanto ai nomi delle reti di origine e di destinazione per visualizzare le subnet per ogni rete.
3. Selezionare una rete in **Rete nell'origine**, e quindi fare clic su **Esegui mapping**. Il servizio rileva le reti VM nel server di destinazione e le visualizza.

	![Architettura SAN](./media/site-recovery-vmm-san/ASRSAN_NetworkMap1.png)

4. Nella finestra di dialogo selezionare una delle reti VM dal server VMM di destinazione.

	![Architettura SAN](./media/site-recovery-vmm-san/ASRSAN_NetworkMap2.png)

5. Quando si seleziona una rete di destinazione, vengono visualizzati i cloud protetti in cui viene usata la rete di origine. Vengono visualizzate anche le reti di destinazione disponibili associate ai cloud usati per la protezione. È consigliabile selezionare una rete di destinazione disponibile per tutti i cloud che vengono usati per la protezione.
6.  Fare clic sul segno di spunta per completare il processo di mapping. Un processo inizierà a tenere traccia dell'avanzamento del mapping, È possibile visualizzarlo nella scheda **Processi**.


## Passaggio 7: abilitare la replica per i gruppi di replica</h3>

Prima di abilitare la protezione per le macchine virtuali è necessario abilitare la replica per i gruppi di replica di archiviazione.

1. Nel portale di Azure Site Recovery, all'interno della pagina delle proprietà del cloud primario, aprire la scheda **Macchine virtuali**. Fare clic su **Aggiungi gruppo di replica**.
2. Selezionare uno o più gruppi di replica VMM associati al cloud, verificare gli array di origine e di destinazione e specificare la frequenza di replica.

Al termine dell'operazione, Azure Site Recovery, insieme ai provider VMM e SMI-S, esegue il provisioning delle unità logiche di archiviazione del sito di destinazione e abilita la replica di archiviazione. Se il gruppo di replica è già stato replicato, Azure Site Recovery usa la relazione di replica esistente e aggiorna le informazioni in Azure Site Recovery.

## Abilitare la protezione per le macchine virtuali

Dopo aver avviato la replica di un gruppo di archiviazione, è necessario abilitare la protezione per le macchine virtuali nella console VMM usando uno dei metodi descritti di seguito:

- **Nuova macchina virtuale**: nella console VMM, quando si crea una nuova macchina virtuale, si abilita la protezione di Azure Site Recovery e si associa la macchina virtuale al gruppo di replica. Se si sceglie questa opzione, VMM usa la funzione di posizionamento intelligente per posizionare in modo ottimale l'archiviazione della macchina virtuale sulle unità logiche del gruppo di replica. Azure Site Recovery gestisce inoltre la creazione di una macchina virtuale ombra nel sito secondario e alloca la capacità necessaria per consentire l'avvio della macchina virtuale di replica dopo il failover.
- **Macchina virtuale esistente**: se in VMM è già stata distribuita una macchina virtuale, è possibile abilitare la protezione di Azure Site Recovery e migrare l'archiviazione in un gruppo di replica. Al termine di questa operazione, VMM e Azure Site Recovery identificano la nuova macchina virtuale e iniziano a gestirla in Azure Site Recovery per abilitarne la protezione. Nel sito secondario viene creata una macchina virtuale ombra e viene allocata la capacità necessaria per consentire l'avvio della macchina virtuale di replica dopo il failover.


	![Abilita protezione](./media/site-recovery-vmm-san/SRSAN_EnableProtection.png)
	

<P>Una volta abilitate per la protezione, le macchine virtuali vengono visualizzate nella console di Azure Site Recovery. È possibile ora visualizzare le proprietà delle macchine virtuali, monitorarne lo stato ed eseguire il failover dei gruppi di replica che contengono più macchine virtuali. Tenere presente che nella replica SAN tutte le macchine virtuali associate a un gruppo di replica devono essere sottoposte a failover insieme. Il failover, infatti, viene eseguito prima a livello di archiviazione. È importante, quindi, raggruppare correttamente i gruppi di replica e collocare insieme solo le macchine virtuali associate.</P>

Tenere traccia dell'avanzamento dell'azione di abilitazione della protezione, inclusa la replica iniziale, nella scheda **Processi** Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover. ![Processo di protezione delle macchine virtuali](./media/site-recovery-vmm-san/SRSAN_JobPropertiesTab.png)

## Passaggio 8: testare la distribuzione</h3>

Eseguire il test della distribuzione per verificare che il failover delle macchine virtuali e dei dati venga eseguito correttamente. Per effettuare questa operazione, creare un piano di ripristino selezionando i gruppi di replica, quindi eseguire un failover di test sul piano.

1. Nella scheda **Piani di ripristino** fare clic su **Crea piano di ripristino**.
2. Specificare un nome per il piano di ripristino e per i server VMM di origine e di destinazione. Sul server di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino. Selezionare **SAN** per visualizzare solo i cloud configurati per la replica SAN. 
3.
	![Crea piano di ripristino](./media/site-recovery-vmm-san/SRSAN_RPlan.png)

4. In **Seleziona macchine virtuali** selezionare i gruppi di replica. Tutte le macchine virtuali associate al gruppo di replica verranno selezionate e aggiunte al piano di ripristino. In questa esercitazione, le macchine virtuali vengono aggiunte al gruppo predefinito del piano di ripristino Gruppo 1. Se necessario, è possibile aggiungere altri gruppi. Al termine della replica, le macchine virtuali verranno avviate in base all'ordine dei gruppi del piano di ripristino.

	![Aggiungi macchine virtuali.](./media/site-recovery-vmm-san/SRSAN_RPlanVM.png)	
5. Una volta creato, il piano di ripristino verrà visualizzato nell'elenco all'interno della scheda **Piani di ripristino**. 
6. Nella scheda **Piani di ripristino** selezionare il piano e fare clic su **Failover di test**.
7. Nella pagina **Conferma failover di test** selezionare **Nessuno**. Se si abilita questa opzione, le macchine virtuali di replica su cui è stato eseguito il failover non verranno connesse in rete. Il test, infatti, controlla che il failover delle macchine virtuali venga eseguito correttamente ma non verifica l'ambiente di rete di replica. Se si vuole eseguire un failover di test più completo, vedere <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testare una distribuzione locale su MSDN</a>.

	![Seleziona rete di test](./media/site-recovery-vmm-san/SRSAN_TestFailover1.png)


8. La macchina virtuale di test viene creata sullo stesso host in cui è presente la macchina virtuale di replica, ma non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.
9. Al termine del processo di replica, alla macchina virtuale di replica verrà assegnato un indirizzo IP diverso dall'indirizzo IP della macchina virtuale primaria. Se gli indirizzi vengono emessi da un protocollo DHCP, l'aggiornamento verrà eseguito automaticamente. Se invece non si usa il protocollo DHCP e si desidera indirizzi IP identici, sarà necessario eseguire alcuni script.
10. Eseguire il seguente script di esempio per recuperare l'indirizzo IP.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

11. Eseguire il seguente script di esempio per aggiornare il DNS e specificare l'indirizzo IP recuperato mediante il precedente script di esempio.

		[string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Monitorare l'attività

La scheda **Processi** e il **Dashboard** consentono di visualizzare e monitorare i processi principali eseguiti dall'insieme di credenziali di Azure Site Recovery, tra cui la configurazione della protezione per un cloud, l'abilitazione e la disabilitazione della protezione per una macchina virtuale, l'esecuzione di un failover \(pianificato, non pianificato o di test\) e il commit di un failover non pianificato.

Nella scheda **Processi** è possibile visualizzare i processi, eseguire il drill-down di dettagli ed errori dei processi, eseguire query sui processi per recuperare i processi che soddisfano specifici criteri, esportare processi in Excel e riavviare i processi non riusciti.

Nel **Dashboard** è possibile scaricare le versioni più recenti dei file di installazione del provider e dell'agente, ottenere informazioni di configurazione per l'insieme di credenziali, visualizzare il numero di macchine virtuali la cui protezione è gestita dall'insieme di credenziali, visualizzare i processi recenti, gestire il certificato dell'insieme di credenziali e risincronizzare le macchine virtuali.

Per altre informazioni sull'interazione con i processi e il dashboard, vedere la [guida alle operazioni e al monitoraggio](http://go.microsoft.com/fwlink/?LinkId=398534).
	
 

<!---HONumber=58_postMigration-->