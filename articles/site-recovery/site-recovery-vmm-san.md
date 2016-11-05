---
title: Eseguire la replica di VM Hyper-V in un cloud VMM in un sito secondario con Azure Site Recovery tramite SAN | Microsoft Docs
description: Questo articolo descrive come eseguire la replica di macchine virtuali Hyper-V tra due siti con Azure Site Recovery tramite la replica SAN.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: raynew

---
# Eseguire la replica di VM Hyper-V in un cloud VMM in un sito secondario con Azure Site Recovery tramite SAN
Questo articolo illustra come distribuire Site Recovery per orchestrare e automatizzare la replica SAN e il failover per macchine virtuali Hyper-V posizionate in cloud System Center VMM verso un sito VMM secondario.

Dopo la lettura di questo articolo, è possibile inviare commenti o domande nella parte inferiore dell'articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Panoramica
Le organizzazioni necessitano di una strategia per la continuità aziendale e per il ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) che consente di determinare il modo in cui le app, i carichi di lavoro e i dati rimangono in esecuzione e disponibili durante i periodi di inattività pianificati e come ripristinare le condizioni di lavoro normali il prima possibile. La strategia BCDR è incentrata sulle soluzioni che garantiscono la sicurezza e la possibilità di recuperare i dati aziendali e di mantenere i carichi di lavoro continuamente disponibili in caso di emergenza.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover al sito secondario per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. È possibile usare Site Recovery in diversi scenari per proteggere più carichi di lavoro. Altre informazioni sono disponibili in [Informazioni su Azure Site Recovery](site-recovery-overview.md).

Questo articolo include istruzioni per la configurazione di una replica di VM Hyper-V da un sito VMM a un altro mediante la replica SAN. Include una panoramica dell'architettura, prerequisiti e istruzioni per la distribuzione. Si individuerà e si classificherà l'archiviazione SAN in VMM, si eseguirà il provisioning di unità logiche e si allocherà spazio di archiviazione a cluster Hyper-V. Si conclude con il test del failover, per accertarsi che tutti gli elementi funzionino come previsto.

## Perché eseguire la replica con SAN?
Questo scenario offre i vantaggi seguenti:

* Offrire una soluzione di replica scalabile aziendale automatizzata mediante Site Recovery.
* Sfruttare le funzionalità di replica SAN fornite dai partner di archiviazione aziendali attraverso fibre channel e archiviazione iSCSI. Vedere [partner di archiviazione SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
* Sfruttare l'infrastruttura SAN esistente per proteggere le applicazioni di importanza strategica distribuite in cluster Hyper-V.
* Fornire supporto per i cluster guest.
* Garantire la replica coerente tra i diversi livelli di un'applicazione mediante una replica sincronizzata con valori RTO e RPO bassi e una replica non sincronizzata per elevata flessibilità, in base alle caratteristiche dell'array di archiviazione.
* L'integrazione con VMM offre la gestione SAN all'interno della console VMM e SMI-S in VMM individua lo spazio di archiviazione esistente.

## Architettura
Questo scenario consente di proteggere i carichi di lavoro tramite il backup di macchine virtuali Hyper-V da un sito VMM locale a un altro tramite la replica SAN.

![Architettura SAN](./media/site-recovery-vmm-san/architecture.png)

I componenti di questo scenario comprendono:

* **Macchine virtuali locali**: i server Hyper-V locali gestiti nei cloud privati VMM contengono le macchine virtuali da proteggere.
* **Server VMM locali**: è possibile fare in modo che uno o più server VMM siano in esecuzione nel sito primario che si vuole proteggere e nel sito secondario.
* **Archiviazione SAN**: un array SAN nel sito primario e uno nel sito secondario
* **Insieme di credenziali di Azure Site Recovery**: l'insieme di credenziali coordina e orchestra la replica, il failover e il ripristino dei dati tra i siti locali.
* **Provider di Azure Site Recovery**: il provider è installato in ciascun server VMM.

## Prima di iniziare
Assicurarsi che siano rispettati i prerequisiti seguenti:

| **Prerequisiti** | **Dettagli** |
| --- | --- |
| **Azzurro** |È necessario un account [Microsoft Azure](https://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery. |
| **VMM** |È necessario almeno un server VMM distribuito come server fisico o virtuale autonomo o come cluster virtuale. <br/><br/>Il server VMM deve essere in esecuzione in System Center 2012 R2 con gli ultimi aggiornamenti cumulativi.<br/><br/>È necessario almeno un cloud configurato nel server VMM primario da proteggere e un cloud configurato sul server VMM secondario da usare per la protezione e il ripristino<br/><br/>Il cloud di origine che si vuole proteggere deve contenere uno o più gruppi host VMM.<br/><br/>Per tutti i cloud VMM è necessario impostare il set di profili di capacità Hyper-V.<br/><br/>Per altre informazioni sulla configurazione dei cloud VMM, vedere [Pianificare l'infrastruttura VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) e la [procedura dettagliata per la creazione di cloud privati con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx). |
| **Hyper-V** |Sono necessari uno o più cluster Hyper-V nei siti primari e secondari e una o più VM nel cluster Hyper-V di origine. I gruppi host VMM nei percorsi primari e secondari devono avere uno o più cluster Hyper-V in ogni gruppo.<br/><br/>I server Hyper-V host e di destinazione devono essere in esecuzione almeno in Windows Server 2012 con il ruolo Hyper-V e gli aggiornamenti più recenti installati.<br/><br/>Qualsiasi server Hyper-V contenente le VM da proteggere deve trovarsi in un cloud VMM.<br/><br/>Se si esegue Hyper-V in un cluster, si noti che il gestore del cluster non viene creato automaticamente se si ha un cluster basato su indirizzi IP statici. Sarà necessario configurare manualmente il broker del cluster. [Altre informazioni](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) nel post di blog di Aidan Finn. |
| **Archiviazione SAN** |Con la replica SAN è possibile eseguire la replica di macchine virtuali in cluster guest con archiviazione iSCSI o Fibre Channel o usare i dischi rigidi virtuali condivisi (vhdx).<br/><br/>È necessario configurare due array SAN, uno nel sito primario e uno nel sito secondario.<br/><br/>L'infrastruttura di rete deve essere configurata tra gli array. Devono essere configurati processi di peering e di replica. Le licenze di replica devono essere configurate in base ai requisiti dell'array di archiviazione.<br/><br/>Devono essere configurate connessioni di rete tra i server host Hyper-V e l'array di archiviazione, in modo che gli host possano comunicare con le unità logiche di archiviazione tramite iSCSI o Fibre Channel.<br/><br/> Verificare l'elenco degli [array di archiviazione supportati](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/>È necessario installare i provider SMI-S, messi a disposizione dai produttori degli array di archiviazione, e gli array SAN devono essere gestiti dal provider. Configurare il provider in base alla relativa documentazione.<br/><br/>Accertarsi che il provider SMI-S per l'array si trovi su un server a cui il server VMM può accedere tramite rete con un indirizzo IP o un nome di dominio completo (FQDN).<br/><br/>In ogni array SAN devono essere disponibili uno o più pool di archiviazione da usare in questa distribuzione. Il server VMM nel sito primario dovrà gestire l'array primario mentre il server VMM secondario gestirà l'array secondario.<br/><br/>Il server VMM nel sito primario dovrà gestire l'array primario, mentre il server VMM secondario dovrà gestire l'array secondario. |
| **Mapping di rete** |È possibile configurare il mapping di rete per assicurarsi che le macchine virtuali replicate siano correttamente posizionate su server host Hyper-V secondari dopo il failover e che possano essere connesse a reti VM appropriate. Se non si configura la replica del mapping di rete, le VM non si connetteranno ad alcuna rete dopo il failover.<br/><br/>Per configurare il mapping di rete durante la distribuzione, assicurarsi che le macchine virtuali nel server host Hyper-V di origine siano connesse a una rete VM VMM. È necessario che tale rete sia collegata a una rete logica associata al cloud.<br/<br/>Nel cloud di destinazione nel server VMM secondario usato per il ripristino deve essere configurata una rete VM corrispondente che, a sua volta, deve essere collegata a una rete logica corrispondente associata al cloud di destinazione.<br/><br/>[Altre informazioni](site-recovery-network-mapping.md) sul mapping di rete. |

## Passaggio 1: preparare l'infrastruttura VMM
Per preparare l'infrastruttura VMM è necessario:

1. Verificare che i cloud VMM siano configurati
2. Integrare e classificare l'archiviazione SAN in VMM
3. Creare unità logiche e allocare spazio di archiviazione.
4. Creare gruppi di replica
5. Configurare le reti VM

### Verificare che i cloud VMM siano configurati
Site Recovery per orchestra la protezione per le macchine virtuali in server host Hyper-V locali ubicati in cloud VMM. È necessario assicurarsi che tali cloud siano configurati correttamente prima di iniziare la distribuzione di Site Recovery. Altre informazioni nella [procedura dettagliata per la creazione di cloud privati](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) nel blog di Keith Mayer.

### Integrare e classificare l'archiviazione SAN in VMM
Aggiungere e classificazione SAN nella console VMM:

1. Nell'area di lavoro **Infrastruttura** fare clic su **Archiviazione**. Fare clic su **Home page** > **Aggiungi risorse** > **Dispositivi di archiviazione** per avviare l'Aggiunta guidata dispositivi di archiviazione.
2. Nella pagina **Seleziona un tipo di provider di archiviazione** selezionare **Dispositivi SAN e NAS individuati e gestiti da un provider SMI-S**.
   
    ![Tipo di provider](./media/site-recovery-vmm-san/provider-type.png)
3. Nella pagina **Specifica il protocollo e l'indirizzo del provider SMI-S di archiviazione** selezionare **SMI-S CIMXML** e specificare le impostazioni per la connessione al provider.
4. Nei campi **FQDN o indirizzo IP del provider** e **Porta TCP/IP** specificare le impostazioni per la connessione al provider. È possibile usare una connessione SSL solo per SMI-S CIMXML.
   
    ![Connessione provider](./media/site-recovery-vmm-san/connect-settings.png)
5. In **Account RunAs** specificare un account VMM RunAs che possa accedere al provider o creare un nuovo account.
6. VMM tenta di individuare e importare automaticamente nella pagina **Raccolta informazioni** alcune informazioni sul dispositivo di archiviazione. Per provare a recuperare altre informazioni fare clic su **Analizza provider**. Se il tentativo ha esito positivo, nella pagina vengono elencati gli array di archiviazione individuati, i pool di archiviazione, il produttore, il modello e la capacità.
   
    ![Individua l'archiviazione](./media/site-recovery-vmm-san/discover.png)
7. In **Selezionare i pool di archiviazione da gestire e assegnare una classificazione** selezionare i pool di archiviazione che dovranno essere gestiti da VMM e assegnare loro una classificazione. Dai pool di archiviazione verranno importate informazioni sulle unità logiche. Creare unità logiche in base alle applicazioni da proteggere, ai relativi requisiti di capacità e ai requisiti sugli elementi da replicare insieme.
   
    ![Classifica l’archiviazione](./media/site-recovery-vmm-san/classify.png)

### Creare unità logiche e allocare spazio di archiviazione.
1. Dopo aver integrato l'archiviazione SAN in VMM, eseguire il provisioning di (ovvero creare) unità logiche (LUN).
   
   * [Come selezionare un metodo per la creazione di unità logiche in VMM](https://technet.microsoft.com/library/gg610624.aspx)
   * [Come eseguire il provisioning di unità logiche di archiviazione in VMM](https://technet.microsoft.com/library/gg696973.aspx)
     
     > [!NOTE]
     > Dopo avere abilitato la replica per un computer, è consigliabile non aggiungere VHD per il computer nei LUN che non si trovano in un gruppo di replica di Site Recovery. In caso contrario, non verranno rilevati da Site Recovery.
     > 
     > 
2. Allocare quindi capacità di archiviazione al cluster host Hyper-V, in modo che VMM possa distribuire i dati delle macchine virtuali nell'archiviazione su cui è stato eseguito il provisioning:
   
   * Prima di allocare capacità di archiviazione al cluster, è necessario allocare capacità di archiviazione al gruppo host VMM in cui risiede il cluster. Vedere [Modalità di allocazione delle unità logiche di archiviazione a un gruppo Host](https://technet.microsoft.com/library/gg610686.aspx) e [Modalità di allocazione di pool di archiviazione a un gruppo Host](https://technet.microsoft.com/library/gg610635.aspx).</a>
   * Allocare la capacità di archiviazione al cluster, come descritto in [Come configurare l'archiviazione in un cluster host Hyper-V in VMM](https://technet.microsoft.com/library/gg610692.aspx).</a>.

### Creare gruppi di replica
Creare un gruppo di replica contenente tutte le unità logiche che dovranno essere replicate insieme.

1. Nella console VMM aprire la scheda **Gruppi di replica** nelle proprietà dell'array di archiviazione e fare clic su **Nuovo**.
2. Quindi creare il gruppo di replica.
   
    ![Gruppo di replica SAN](./media/site-recovery-vmm-san/rep-group.png)

### Configurare le reti
Se si desidera configurare il mapping di rete procedere come segue:

1. [Leggere le informazioni](site-recovery-network-mapping.md) sul mapping di rete.
2. Preparare le reti VM in VMM:
   
   * [Configurare le reti logiche](https://technet.microsoft.com/library/jj721568.aspx).
   * [Configurare le reti VM](https://technet.microsoft.com/library/jj721575.aspx).

## Passaggio 2: creare un insieme di credenziali
1. Accedere al [portale di gestione](https://portal.azure.com) dal server VMM che si vuole registrare.
2. Espandere **Servizi dati** > **Servizi di ripristino** e fare clic su **Insieme di credenziali di Site Recovery**.
3. Fare clic su **Crea nuovo** > **Creazione rapida**.
4. In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali.
5. In **Area** selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree geografiche supportate, vedere la sezione Disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Fare clic su **Create vault**.
   
    ![New Vault](./media/site-recovery-vmm-san/create-vault.png)

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di **Servizi di ripristino**.

### Registrare i server VMM
1. Aprire la pagina Avvio rapido dalla pagina **Servizi di ripristino**. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.
   
    ![Quick Start Icon](./media/site-recovery-vmm-san/quick-start-icon.png)
2. Dall'elenco a discesa selezionare **Tra siti Hyper-V locali tramite la replica di array**.
   
    ![Registration key](./media/site-recovery-vmm-san/select-san.png)
3. In **Preparare i server VMM** scaricare la versione più recente del file di installazione del provider di Azure Site Recovery.
4. Eseguire il file nel server VMM di origine. Se VMM viene distribuito in un cluster e si installa il provider per la prima volta, installarlo in un nodo attivo nel cluster e completare l'installazione per registrare il server VMM nell'insieme di credenziali. Quindi, installare il provider sugli altri nodi. Si noti che se si sta aggiornando il provider sarà necessario eseguire l'aggiornamento su tutti i nodi, in quanto dovrebbero eseguire tutti la stessa versione del provider.
5. Il programma di installazione esegue una **Verifica prerequisiti** e chiede l'autorizzazione ad arrestare il servizio VMM per avviare l'installazione del provider. Il servizio VMM verrà riavviato automaticamente al termine dell'installazione. Se si esegue l'installazione in un cluster VMM, verrà richiesto di interrompere il ruolo Cluster.
6. In **Microsoft Update** è possibile fornire il consenso esplicito agli aggiornamenti. Se questa impostazione è abilitata, gli aggiornamenti del provider verranno installati in base ai criteri indicati in Microsoft Update.
   
    ![Microsoft Updates](./media/site-recovery-vmm-san/ms-update.png)
7. Il percorso di installazione è impostato su **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**. Fare clic sul pulsante di installazione per iniziare a installare il provider.
   
    ![InstallLocation](./media/site-recovery-vmm-san/install-location.png)
8. Dopo l'installazione del provider, fare clic sul pulsante per registrare il server nell'insieme di credenziali.
   
    ![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)
9. Nella pagina **Connessione Internet** specificare la modalità di connessione Internet del provider in esecuzione sul server VMM. Selezionare **Usa impostazioni proxy del sistema predefinite** per usare le impostazioni di connessione a Internet predefinite configurate nel server.
   
    ![Internet Settings](./media/site-recovery-vmm-san/proxy-details.png)
   
   * Se si vuole usare un server proxy personalizzato, configurarlo prima di installare il provider. Quando si configurano impostazioni proxy personalizzate, verrà eseguito un test per verificare la connessione proxy.
   * Se si usa un proxy personalizzato oppure se il proxy predefinito richiede l'autenticazione, sarà necessario immettere i dettagli del proxy, tra cui l'indirizzo e la porta.
   * Gli URL seguenti devono essere accessibili dal server VMM e dagli host Hyper-V
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * Consentire gli indirizzi IP descritti in [Intervalli IP dei data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e il protocollo HTTPS (443). È necessario aggiungere all'elenco di indirizzi consentiti gli IP dell'area Azure che si prevede di utilizzare e quello degli Stati Uniti occidentali.
   * Se si usa un proxy personalizzato, un account RunAs di VMM (DRAProxyAccount) verrà creato automaticamente con le credenziali del proxy specificate. Configurare il server proxy in modo che l'account possa eseguire correttamente l'autenticazione. Le impostazioni dell'account RunAs di VMM possono essere modificate nella console VMM. A tale scopo, aprire l'area di lavoro Impostazioni, espandere Sicurezza, fare clic su Account RunAs, quindi modificare la password di DRAProxyAccount. È necessario riavviare il servizio VMM per rendere effettiva l'impostazione.
10. In **Registration Key** selezionare il codice di registrazione scaricato da Azure Site Recovery e copiato nel server VMM.
11. In **Vault name** verificare il nome dell'insieme di credenziali in cui verrà registrato il server.
    
    ![Server registration](./media/site-recovery-vmm-san/vault-creds.png)
12. Questa impostazione di crittografia viene usata solo per lo scenario da VMM ad Azure. Gli utenti di uno scenario solo da VMM a VMM possono ignorare questa schermata.
    
    ![Server registration](./media/site-recovery-vmm-san/encrypt.png)
13. In **Nome server** specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali. In una configurazione cluster specificare il nome del ruolo relativo al cluster VMM.
14. In **Initial cloud metadata sync** (Sincronizzazione iniziale dei metadati cloud) specificare un nome descrittivo per il server, che verrà visualizzato nell'insieme di credenziali, e scegliere se sincronizzare i metadati per tutti i cloud presenti sul server VMM con l'insieme di credenziali. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM.
    
    ![Server registration](./media/site-recovery-vmm-san/friendly-name.png)
15. Fare clic su **Avanti** per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato nella scheda *Server VMM* della pagina **Server** nell'insieme di credenziali.

### Installazione dalla riga di comando
Il provider di Azure Site Recovery può essere installato anche usando la riga di comando seguente. Questo metodo può essere usato per installare il provider in un Server CORE per Windows Server 2012 R2.

1. Scaricare il file di installazione del provider e il codice di registrazione in una cartella, ad esempio C:\\ASR.
2. Arrestare il servizio System Center Virtual Machine Manager.
3. Estrarre il programma di installazione del provider eseguendo questi comandi da un prompt dei comandi con privilegi di **amministratore**:
   
        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Installare il provider eseguendo questo comando:
   
        C:\ASR> setupdr.exe /i
5. Registrare il provider eseguendo questo comando:
   
        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

In cui i parametri sono i seguenti:

* **/Credentials**: parametro obbligatorio che specifica la posizione in cui si trova il file della chiave di registrazione.
* **/FriendlyName** : parametro obbligatorio per il nome del server host Hyper-V che viene visualizzato nel portale di Azure Site Recovery.
* **/EncryptionEnabled**: parametro facoltativo da usare solo nello scenario da VMM ad Azure se è necessario che la crittografia delle macchine virtuali sia inattiva in Azure. Assicurarsi che il nome del file specificato abbia l'estensione **pfx**.
* **/proxyAddress**: parametro facoltativo che specifica l'indirizzo del server proxy.
* **/proxyport**: parametro facoltativo che specifica la porta del server proxy.
* **/proxyUsername**: parametro facoltativo che specifica il nome utente proxy (se il proxy richiede l'autenticazione).
* **/proxyPassword**: parametro facoltativo che specifica la password per l'autenticazione nel server proxy (se il proxy richiede l'autenticazione).

## Passaggio 3: Eseguire il mapping di array e pool di archiviazione
Eseguire il mapping degli array per specificare il pool di archiviazione secondario che dovrà ricevere i dati di replica dal pool primario. Il mapping della risorsa di archiviazione deve essere eseguito prima di configurare la protezione del cloud poiché le informazioni di mapping vengono usate quando si abilita la protezione per i gruppi di replica.

Prima di iniziare, verificare che i cloud siano visibili nell'insieme di credenziali. I cloud possono essere identificati scegliendo di sincronizzare tutti i cloud durante l'installazione del provider oppure selezionando la sincronizzazione di un cloud specifico nella scheda **Generale** delle proprietà del cloud nella console VMM. Eseguire quindi il mapping degli array di archiviazione come segue:

1. Fare clic su **Risorse** > **Archiviazione server** > **Mappa array di origine e di destinazione**. ![Server registration](./media/site-recovery-vmm-san/storage-map.png)
2. Selezionare gli array di archiviazione presenti nel sito primario e mapparli agli array di archiviazione del sito secondario.
3. Eseguire quindi il mapping dei pool di archiviazione di origine e di archiviazione all'interno degli array. A tale scopo, in **Pool di archiviazione** selezionare un pool di archiviazione di origine e uno di destinazione di cui eseguire il mapping.
   
   ![Server registration](./media/site-recovery-vmm-san/storage-map-pool.png)

## Passaggio 4: configurare le impostazioni di protezione del cloud
Dopo la registrazione dei server VMM, sarà possibile configurare le impostazioni di protezione del cloud. Quando si installa il provider, abilitare l'opzione **Sincronizza dati cloud con insieme credenziali** in modo che tutti i cloud presenti sul server VMM vengano visualizzati nella scheda <b>Elementi protetti</b> dell'insieme di credenziali.

![Cloud pubblicato](./media/site-recovery-vmm-san/clouds-list.png)

1. Nella pagina Avvio rapido fare clic su **Configurare la protezione per i cloud VMM**.
2. Nella scheda **Elementi protetti** selezionare il cloud da configurare, quindi passare alla scheda **Configurazione**. Si noti che:
3. In **Destinazione** selezionare **VMM**.
4. In **Percorso di destinazione** selezionare il server VMM locale che gestisce il cloud da usare per il ripristino.
5. In **Cloud di destinazione** selezionare il cloud di destinazione da usare per il failover di macchine virtuali nel cloud di origine. Si noti che:
   * È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
   * Un cloud può appartenere solo a una singola coppia di cloud, come cloud primario o di destinazione.
6. Azure Site Recovery verifica che i cloud abbiano accesso all'archiviazione abilitata per la replica SAN e che sia stato eseguito il peering degli array di archiviazione. Vengono visualizzati i peer degli array partecipanti.
7. Se la verifica ha esito positivo, in **Tipo di replica** selezionare **SAN**.

Dopo avere salvato le impostazioni, verrà creato un processo che potrà essere monitorato nella scheda **Processi**. È possibile modificare le impostazioni del cloud nella scheda **Configure**. Se si vuole modificare la posizione o il cloud di destinazione, è necessario rimuovere la configurazione del cloud e, successivamente, riconfigurare il cloud.

## Passaggio 5: Abilitare il mapping di rete
1. Nella pagina Avvio rapido fare clic su **Mapping reti**.
2. Selezionare il server VMM di origine da cui si desidera eseguire il mapping delle reti, quindi il server VMM di destinazione a cui verrà eseguito il mapping delle reti. Vengono visualizzati l'elenco delle reti di origine e le relative reti di destinazione associate. Viene visualizzato un valore vuoto per le reti che non sono state attualmente sottoposte a mapping. Fare clic sull'icona delle informazioni accanto ai nomi delle reti di origine e di destinazione per visualizzare le subnet per ogni rete.
3. Selezionare una rete in **Rete nell'origine**, e quindi fare clic su **Esegui mapping**. Il servizio rileva le reti VM nel server di destinazione e le visualizza.
   
    ![Architettura SAN](./media/site-recovery-vmm-san/network-map1.png)
4. Nella finestra di dialogo selezionare una delle reti VM dal server VMM di destinazione.
   
    ![Architettura SAN](./media/site-recovery-vmm-san/network-map2.png)
5. Quando si seleziona una rete di destinazione, vengono visualizzati i cloud protetti in cui viene usata la rete di origine. Vengono visualizzate anche le reti di destinazione disponibili associate ai cloud usati per la protezione. È consigliabile selezionare una rete di destinazione disponibile per tutti i cloud che vengono usati per la protezione.
6. Fare clic sul segno di spunta per completare il processo di mapping. Un processo inizierà a tenere traccia dell'avanzamento del mapping, È possibile visualizzarlo nella scheda **Processi**.

## Passaggio 6: Abilitare la replica per i gruppi di replica
Prima di abilitare la protezione per le macchine virtuali è necessario abilitare la replica per i gruppi di replica di archiviazione.

1. Nel portale di Azure Site Recovery, all'interno della pagina delle proprietà del cloud primario, aprire la scheda **Macchine virtuali**. Fare clic su **Aggiungi gruppo di replica**.
2. Selezionare uno o più gruppi di replica VMM associati al cloud, verificare gli array di origine e di destinazione e specificare la frequenza di replica.

Al termine dell'operazione, Azure Site Recovery, insieme ai provider VMM e SMI-S, esegue il provisioning delle unità logiche di archiviazione del sito di destinazione e abilita la replica di archiviazione. Se il gruppo di replica è già stato replicato, Azure Site Recovery usa la relazione di replica esistente e aggiorna le informazioni in Azure Site Recovery.

## Passaggio 7: Abilitare la protezione per le macchine virtuali
Dopo aver avviato la replica di un gruppo di archiviazione, è necessario abilitare la protezione per le macchine virtuali nella console VMM usando uno dei metodi descritti di seguito:

* **Nuova macchina virtuale**: nella console VMM, quando si crea una nuova macchina virtuale, si abilita la protezione di Azure Site Recovery e si associa la macchina virtuale al gruppo di replica. Se si sceglie questa opzione, VMM usa la funzione di posizionamento intelligente per posizionare in modo ottimale l'archiviazione della macchina virtuale sulle unità logiche del gruppo di replica. Azure Site Recovery gestisce inoltre la creazione di una macchina virtuale ombra nel sito secondario e alloca la capacità necessaria per consentire l'avvio della macchina virtuale di replica dopo il failover.
* **Macchina virtuale esistente**: se in VMM è già stata distribuita una macchina virtuale, è possibile abilitare la protezione di Azure Site Recovery e migrare l'archiviazione in un gruppo di replica. Al termine di questa operazione, VMM e Azure Site Recovery identificano la nuova macchina virtuale e iniziano a gestirla in Azure Site Recovery per abilitarne la protezione. Nel sito secondario viene creata una macchina virtuale ombra e viene allocata la capacità necessaria per consentire l'avvio della macchina virtuale di replica dopo il failover.
  
    ![Abilita protezione](./media/site-recovery-vmm-san/enable-protect.png)

Una volta abilitate per la protezione, le macchine virtuali vengono visualizzate nella console di Azure Site Recovery. È possibile ora visualizzare le proprietà delle macchine virtuali, monitorarne lo stato ed eseguire il failover dei gruppi di replica che contengono più macchine virtuali. Tenere presente che nella replica SAN tutte le macchine virtuali associate a un gruppo di replica devono essere sottoposte a failover insieme. Il failover, infatti, viene eseguito prima a livello di archiviazione. È importante, quindi, raggruppare correttamente i gruppi di replica e collocare insieme solo le macchine virtuali associate.

> [!NOTE]
> Dopo avere abilitato la replica per un computer, è consigliabile non aggiungere VHD per il computer nei LUN che non si trovano in un gruppo di replica di Site Recovery. In caso contrario, non verranno rilevati da Site Recovery.
> 
> 

Tenere traccia dello stato dell'azione di abilitazione della protezione, inclusa la replica iniziale, nella scheda **Processi**. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover.

![Processo di protezione delle macchine virtuali](./media/site-recovery-vmm-san/job-props.png)

## Passaggio 8: Testare la distribuzione
Eseguire il test della distribuzione per verificare che il failover delle macchine virtuali e dei dati venga eseguito correttamente. Per effettuare questa operazione, creare un piano di ripristino selezionando i gruppi di replica, quindi eseguire un failover di test sul piano.

1. Nella scheda **Piani di ripristino** fare clic su **Crea piano di ripristino**.
2. Specificare un nome per il piano di ripristino e per i server VMM di origine e di destinazione. Sul server di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino. Selezionare **SAN** per visualizzare solo i cloud configurati per la replica SAN.
   3.
    ![Crea piano di ripristino](./media/site-recovery-vmm-san/r-plan.png)
3. In **Seleziona macchine virtuali** selezionare i gruppi di replica. Tutte le macchine virtuali associate al gruppo di replica verranno selezionate e aggiunte al piano di ripristino. In questa esercitazione, le macchine virtuali vengono aggiunte al gruppo predefinito del piano di ripristino Gruppo 1. Se necessario, è possibile aggiungere altri gruppi. Si noti che al termine della replica le macchine virtuali verranno avviate in base all'ordine dei gruppi del piano di ripristino.
   
    ![Aggiungi macchine virtuali.](./media/site-recovery-vmm-san/r-plan-vm.png)
4. Una volta creato, il piano di ripristino verrà visualizzato nell'elenco all'interno della scheda **Piani di ripristino**.
5. Nella scheda **Piani di ripristino** selezionare il piano e fare clic su **Failover di test**.
6. Nella pagina **Conferma failover di test** selezionare **Nessuno**. Se si abilita questa opzione, le macchine virtuali di replica su cui è stato eseguito il failover non verranno connesse in rete. Il test, infatti, controlla che il failover delle macchine virtuali venga eseguito correttamente ma non verifica l'ambiente di rete di replica. Osservare come [eseguire un failover di test](site-recovery-failover.md#run-a-test-failover) per ulteriori informazioni su come utilizzare le opzioni di rete diverse.

    ![Seleziona rete di test](./media/site-recovery-vmm-san/test-fail1.png)

1. La macchina virtuale di test viene creata sullo stesso host in cui è presente la macchina virtuale di replica, ma non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.
2. Al termine del processo di replica, alla macchina virtuale di replica verrà assegnato un indirizzo IP diverso dall'indirizzo IP della macchina virtuale primaria. Se gli indirizzi vengono emessi da un protocollo DHCP, l'aggiornamento verrà eseguito automaticamente. Se invece non si usa il protocollo DHCP e si desidera indirizzi IP identici, sarà necessario eseguire alcuni script.
3. Eseguire il seguente script di esempio per recuperare l'indirizzo IP.
   
       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  
4. Eseguire il seguente script di esempio per aggiornare il DNS e specificare l'indirizzo IP recuperato mediante il precedente script di esempio.
   
       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Passaggi successivi
Dopo aver eseguito un failover di test per verificare che l'ambiente funzioni come previsto, vedere [altre informazioni](site-recovery-failover.md) sui diversi tipi di failover.

<!---HONumber=AcomDC_0706_2016-->