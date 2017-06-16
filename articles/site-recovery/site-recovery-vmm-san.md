---
title: Eseguire la replica di macchine virtuali Hyper-V in VMM con SAN usando Azure Site Recovery | Documentazione Azure
description: Questo articolo descrive come eseguire la replica di macchine virtuali Hyper-V tra due siti con Azure Site Recovery tramite la replica SAN.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 67b4861ac564565b2a36932ae15141a1e1f56035
ms.openlocfilehash: 29084a52aca22b31a2fa42660ec2cdf050aec7ca
ms.contentlocale: it-it
ms.lasthandoff: 02/23/2017


---
# <a name="replicate-hyper-v-vms-in-vmm-clouds-to-a-secondary-site-with-azure-site-recovery-by-using-san"></a>Eseguire la replica di VM Hyper-V nei cloud VMM in un sito secondario con Azure Site Recovery tramite SAN


Questo articolo descrive come distribuire [Azure Site Recovery](site-recovery-overview.md) per gestire la replica di macchine virtuali Hyper-V (gestiti nei cloud di System Center Virtual Machine Manager) in un sito VMM secondario, usando Azure Site Recovery nel portale classico. Questo scenario non è disponibile nel nuovo portale di Azure.



Inviare eventuali commenti alla fine di questo articolo. In caso di domande tecniche, visitare il [forum di Azure Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="why-replicate-with-san-and-site-recovery"></a>Vantaggi della replica con SAN e Site Recovery

* SAN offre una soluzione di replica scalabile a livello aziendale che consente a un sito primario contenente Hyper-V con SAN di eseguire repliche di LUN in un sito secondario con SAN. L'archiviazione viene gestita da VMM e la replica e il failover vengono orchestrati con Site Recovery.
* Site Recovery ha interagito con alcuni [partner di archiviazione SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) per eseguire la replica su Fibre Channel e archiviazione iSCSI.  
* Usare l'infrastruttura SAN esistente per proteggere le app mission-critical distribuite in cluster Hyper-V. Le macchine virtuali possono essere replicate come gruppo, in modo da consentire il failover coerente delle app di livello N.
* La replica SAN garantisce la replica coerente tra i diversi livelli di un'applicazione grazie a una replica sincronizzata con valori RTO e RPO bassi e una replica non sincronizzata per elevata flessibilità, in base alle caratteristiche dell'array di archiviazione.  
* È possibile gestire l'archiviazione SAN nell'infrastruttura di VMM e usare SMI-S in VMM per individuare l'archiviazione esistente.  

Si noti che:
- La replica da sito a sito con SAN non è disponibile nel portale di Azure. È disponibile solo nel portale classico. Non è possibile creare nuovi insiemi di credenziali nel portale classico. Gli insiemi di credenziali esistenti possono essere mantenuti.
- La replica da SAN ad Azure non è supportata.
- Non è possibile eseguire la replica di VHDX condivisi o unità logiche (LUN, Logical Unit) connessi direttamente alle macchine virtuali tramite iSCSI o Fibre Channel. I cluster guest possono essere replicati.


## <a name="architecture"></a>Architettura

![Architettura SAN](./media/site-recovery-vmm-san/architecture.png)

- **Azure**: configurare un insieme di credenziali di Site Recovery nel portale di Azure.
- **Archiviazione SAN**: l'archiviazione SAN viene gestita nell'infrastruttura di VMM. È possibile aggiungere il provider di archiviazione, creare classificazioni di archiviazione e configurare i pool di archiviazione.
- **VMM e Hyper-V**: è consigliabile un server VMM in ogni sito. Configurare cloud privati VMM e inserire cluster Hyper-V in tali cloud. Durante la distribuzione, il provider di Azure Site Recovery viene installato in ogni server VMM e il server viene registrato nell'insieme di credenziali. Il provider comunica con il servizio Site Recovery per gestire la replica, il failover e il failback.
- **Replica**: dopo la configurazione dell'archiviazione in VMM e la configurazione della replica in Site Recovery, la replica viene eseguita tra l'archiviazione SAN primaria e secondaria. Nessun dato di replica viene inviato a Site Recovery.
- **Failover**: abilitare il failover nel portale di Site Recovery. Non si verifica alcuna perdita di dati durante il failover perché la replica è sincrona.
- **Failback**: per eseguire il failback, si abilita la replica inversa in modo da trasferire le modifiche differenziali dal sito secondario a quello primario. Al termine della replica inversa, eseguire un failover pianificato dal sito secondario al sito primario. Il failover pianificato arresta le macchine virtuali di replica nel sito secondario e le avvia nel sito primario.


## <a name="before-you-start"></a>Prima di iniziare


**Prerequisiti** | **Dettagli**
--- | ---
**Azzurro** |È necessario un account [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery. Creare un insieme di credenziali di Azure Site Recovery per configurare e gestire la replica e il failover.
**VMM** | È possibile usare un singolo server VMM ed eseguire la replica tra cloud diversi, ma è consigliabile usare un server VMM nel sito primario e uno nel sito secondario. Un server VMM può essere distribuito come server autonomo fisico o virtuale oppure come cluster. <br/><br/>Il server VMM deve eseguire System Center 2012 R2 o versione successiva con gli ultimi aggiornamenti cumulativi.<br/><br/> È necessario che in ogni server sia configurato almeno un cloud nel server VMM primario che si vuole proteggere e un cloud nel server VMM secondario da usare per il failover.<br/><br/> Il cloud di origine deve contenere uno o più gruppi host VMM.<br/><br/> Tutti i cloud VMM devono avere il profilo di capacità Hyper-V impostato.<br/><br/> Per altre informazioni sulla configurazione di cloud VMM, vedere [Distribuire un cloud privato in VMM](https://technet.microsoft.com/en-us/system-center-docs/vmm/scenario/cloud-overview).
**Hyper-V** | Sono necessari uno o più cluster Hyper-V nei cloud VMM primario e secondario.<br/><br/> Il cluster Hyper-V di origine deve contenere una o più macchine virtuali.<br/><br/> I gruppi host VMM nei siti primari e secondari devono contenere almeno uno dei cluster Hyper-V.<br/><br/>I server Hyper-V host e di destinazione devono eseguire Windows Server 2012 o versione successiva con il ruolo Hyper-V e gli ultimi aggiornamenti installati.<br/><br/> Se si esegue Hyper-V in un cluster e si usa un cluster basato su indirizzi IP statici, il broker del cluster non viene creato automaticamente, ma è necessario configurarlo manualmente. Per altre informazioni, vedere [Preparing host clusters for Hyper-V replica](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) (Preparazione di cluster host per la replica Hyper-V).
**Archiviazione SAN** | È possibile replicare macchine virtuali in cluster guest con l'archiviazione iSCSI o Fibre Channel o usando dischi rigidi virtuali condivisi (vhdx).<br/><br/> Sono necessari due array SAN, uno nel sito primario e l'altro in quello secondario.<br/><br/> L'infrastruttura di rete deve essere configurata tra array. Devono essere configurati processi di peering e di replica. Le licenze di replica devono essere configurate in base ai requisiti dell'array di archiviazione.<br/><br/>È necessario configurare connessioni di rete tra i server host Hyper-V e l'array di archiviazione, in modo che gli host possano comunicare con le unità logiche di archiviazione usando iSCSI o Fibre Channel.<br/><br/> Vedere il post di blog sugli [array di archiviazione supportati](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/> I provider SMI-S, implementati dai produttori degli array di archiviazione, devono essere installati e gli array SAN devono essere gestiti dal provider. Configurare il provider in base alle istruzioni del produttore.<br/><br/>Verificare che il provider SMI-S dell'array si trovi in un server accessibile dal server VMM tramite rete, con un indirizzo IP o un nome di dominio completo (FQDN).<br/><br/> Ogni array SAN deve avere uno o più pool di archiviazione disponibili.<br/><br/> Il server VMM primario deve gestire l'array primario, mentre il server VMM secondario deve gestire l'array secondario.
**Mapping di rete** | Configurare il mapping di rete in modo che le macchine virtuali replicate siano correttamente posizionate su server host Hyper-V secondari dopo il failover e che possano essere connesse a reti VM appropriate. Se non si configura la replica del mapping di rete, le VM di replica non si connetteranno ad alcuna rete dopo il failover.<br/><br/> È necessario verificare che le reti VMM siano configurate correttamente, in modo che sia possibile configurare il mapping di rete durante la distribuzione di Site Recovery. In VMM le macchine virtuali dell'host Hyper-V di origine devono essere connesse alla rete di macchine virtuali di VMM. È necessario che tale rete sia collegata a una rete logica associata al cloud.<br/><br/> Il cloud di destinazione deve avere una rete VM corrispondente che, a sua volta, deve essere collegata a una rete logica corrispondente associata al cloud di destinazione.<br/><br/>.

## <a name="step-1-prepare-the-vmm-infrastructure"></a>Passaggio 1: preparare l'infrastruttura VMM
Per preparare l'infrastruttura VMM, è necessario eseguire queste operazioni:

1. Verificare i cloud VMM.
2. Integrare e classificare l'archiviazione SAN in VMM.
3. Creare unità logiche e allocare spazio di archiviazione.
4. Creare gruppi di replica.
5. Configurare reti VM.

### <a name="verify-vmm-clouds"></a>Verificare i cloud VMM

Assicurarsi che i cloud VMM siano configurati correttamente prima di iniziare la distribuzione di Site Recovery.

### <a name="integrate-and-classify-san-storage-in-the-vmm-fabric"></a>Integrare e classificare l'archiviazione SAN nell'infrastruttura di VMM

1. Nella console VMM fare clic su **Infrastruttura** > **Archiviazione** > **Aggiungi risorse** > **Dispositivi di archiviazione**.
2. Nell'**Aggiunta guidata dispositivi di archiviazione** selezionare **Seleziona un tipo di provider di archiviazione** e quindi seleziona **Dispositivi SAN e NAS individuati e gestiti da un provider SMI-S**.

    ![Tipo di provider](./media/site-recovery-vmm-san/provider-type.png)

3. Nella pagina **Specifica il protocollo e l'indirizzo del provider SMI-S di archiviazione** selezionare **SMI-S CIMXML** e specificare le impostazioni per la connessione al provider.
4. Nei campi **FQDN o indirizzo IP del provider** e **Porta TCP/IP** specificare le impostazioni per la connessione al provider. È possibile usare una connessione SSL solo per SMI-S CIMXML.

    ![Connessione provider](./media/site-recovery-vmm-san/connect-settings.png)
5. In **Account RunAs** specificare un account RunAs VMM in grado di accedere al provider o creare un account.
6. VMM tenta di individuare e importare automaticamente nella pagina **Raccolta informazioni** alcune informazioni sul dispositivo di archiviazione. Per provare a recuperare altre informazioni fare clic su **Analizza provider**. Se il tentativo ha esito positivo, nella pagina vengono elencati gli array di archiviazione individuati, i pool di archiviazione, il produttore, il modello e la capacità.

    ![Individua l'archiviazione](./media/site-recovery-vmm-san/discover.png)
7. In **Selezionare i pool di archiviazione da gestire e assegnare una classificazione**selezionare i pool di archiviazione che dovranno essere gestiti da VMM e assegnare loro una classificazione. Le informazioni LUN vengono importate dai pool di archiviazione. Creare unità logiche in base alle applicazioni da proteggere, ai requisiti di capacità relativi e ai requisiti sugli elementi da replicare insieme.

    ![Classifica l’archiviazione](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>Creare unità logiche e allocare spazio di archiviazione.

Creare unità logiche in base alle applicazioni da proteggere, ai requisiti di capacità e ai requisiti sugli elementi da replicare insieme.

1. Dopo la visualizzazione della risorsa di archiviazione nell'infrastruttura di VMM, è possibile [effettuare il provisioning di LUN](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-storage-host-groups#create-a-lun-in-vmm).

     > [!NOTE]
     > Non aggiungere dischi rigidi virtuali per la macchina virtuale che siano abilitati per la replica nelle unità logiche, Se le unità logiche non si trovano in un gruppo di replica di Site Recovery, non vengono rilevate da quest'ultimo componente.
     >

2. Allocare capacità di archiviazione al cluster host Hyper-V, in modo che VMM possa distribuire i dati delle macchine virtuali nell'archiviazione sottoposta a provisioning:

   * Prima di allocare capacità di archiviazione al cluster, è necessario allocarla al gruppo host VMM in cui risiede il cluster. Per altre informazioni, vedere [Come allocare unità logiche di archiviazione a un gruppo host in VMM](https://technet.microsoft.com/library/gg610686.aspx) e [Come allocare pool di archiviazione a un gruppo host in VMM](https://technet.microsoft.com/library/gg610635.aspx).
   * Allocare la capacità di archiviazione al cluster, come descritto in [Come configurare l'archiviazione in un cluster host Hyper-V in VMM](https://technet.microsoft.com/library/gg610692.aspx).

    ![Tipo di provider](./media/site-recovery-vmm-san/provider-type.png)
3. In **Specifica il protocollo e l'indirizzo del provider SMI-S di archiviazione** selezionare **SMI-S CIMXML**. Specificare le impostazioni per la connessione al. È possibile usare una connessione SSL solo per SMI-S CIMXML.

    ![Connessione provider](./media/site-recovery-vmm-san/connect-settings.png)
4. In **Account RunAs** specificare un account RunAs VMM in grado di accedere al provider o creare un account.
5. In **Raccogli informazioni** VMM prova automaticamente a individuare e importare le informazioni sui dispositivi di archiviazione. Se è necessario riprovare, fare clic su **Provider di analisi**. Quando il tentativo ha esito positivo, nella pagina vengono elencati gli array di archiviazione, i pool di archiviazione, il produttore, il modello e la capacità.

    ![Individua l'archiviazione](./media/site-recovery-vmm-san/discover.png)
7. In **Selezionare i pool di archiviazione da includere nella gestione e assegnare una classificazione** selezionare i pool di archiviazione che dovranno essere gestiti da VMM e assegnare loro una classificazione. Le informazioni LUN vengono importate dai pool di archiviazione.

    ![Classifica l’archiviazione](./media/site-recovery-vmm-san/classify.png)


### <a name="create-replication-groups"></a>Creare gruppi di replica

Creare un gruppo di replica contenente tutte le unità logiche da replicare insieme.

1. Nella console VMM aprire la scheda **Gruppi di replica** nelle proprietà dell'array di archiviazione e quindi fare clic su **Nuovo**.
2. Creare il gruppo di replica.

    ![Gruppo di replica SAN](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>Configurare le reti

Se si intende configurare il mapping di rete, seguire questa procedura:

1. Vedere il mapping della rete di Site Recovery.
2. Preparare le reti VM in VMM:

   * [Configurare le reti logiche](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-logical-networks).
   * [Configurare le reti VM](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-vm-networks).


## <a name="step-2-create-a-vault"></a>Passaggio 2: creare un insieme di credenziali

1. Accedere al [portale di Azure](https://portal.azure.com) dal server VMM in cui si vuole registrare l'insieme di credenziali.
2. Espandere **Servizi dati** > **Servizi di ripristino** e quindi **Insieme di credenziali di Site Recovery**.
3. Fare clic su **Creare nuovo** > **Creazione rapida**.
4. In **Nome**immettere un nome descrittivo per identificare l'insieme di credenziali.
5. In **Region**selezionare l'area geografica per l'insieme di credenziali. Per verificare le aree supportate, vedere [Prezzi di Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Fare clic su **Create vault**.

    ![New Vault](./media/site-recovery-vmm-san/create-vault.png)

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di **Servizi di ripristino**.

### <a name="register-the-vmm-servers"></a>Registrare i server VMM

1. Aprire la pagina **Avvio rapido** dalla pagina **Servizi di ripristino**. La pagina Avvio rapido può essere aperta in qualsiasi momento anche tramite l'icona.

    ![Quick Start Icon](./media/site-recovery-vmm-san/quick-start-icon.png)
2. Nella casella di riepilogo a discesa selezionare **Between Hyper-V on-premises site using array replication** (Tra siti Hyper-V locali tramite la replica di array).

    ![Chiave di registrazione](./media/site-recovery-vmm-san/select-san.png)
3. In **Preparare i server VMM**scaricare la versione più recente del file di installazione del provider di Azure Site Recovery.
4. Eseguire il file nel server VMM di origine. Se VMM viene distribuito in un cluster e si installa il provider per la prima volta, installarlo in un nodo attivo nel cluster e completare l'installazione per registrare il server VMM nell'insieme di credenziali. Quindi, installare il provider sugli altri nodi. Se si sta aggiornando il provider, è necessario eseguire l'aggiornamento su tutti i nodi, in modo che eseguano tutti la stessa versione del provider.
5. Il programma di installazione verifica i prerequisiti e chiede l'autorizzazione per arrestare il servizio VMM e avviare l'installazione del provider. Il servizio verrà riavviato automaticamente al termine dell'installazione. In un cluster VMM verrà richiesto di interrompere il ruolo Cluster.
6. In **Microsoft Update** è possibile acconsentire esplicitamente agli aggiornamenti e gli aggiornamenti del provider verranno installati in base ai criteri di Microsoft Update.

    ![Microsoft Updates](./media/site-recovery-vmm-san/ms-update.png)

7. Per impostazione predefinita, il percorso di installazione per il provider è <SystemDrive>\Programmi\Microsoft System Center 2012 R2\Virtual Machine Manager\bin. Per iniziare, fare clic su **Installa**.

    ![Percorso di installazione](./media/site-recovery-vmm-san/install-location.png)

8. Dopo l'installazione del provider, fare clic su **Registra** per registrare il server VMM nell'insieme di credenziali.

    ![Installazione completata](./media/site-recovery-vmm-san/install-complete.png)

9. Nella pagina **Connessione Internet** specificare la modalità di connessione a Internet del provider. Selezionare **Use default system proxy settings** (Usa impostazioni proxy del sistema predefinite) per usare le impostazioni di connessione a Internet predefinite configurate nel server.

    ![Internet Settings](./media/site-recovery-vmm-san/proxy-details.png)

   * Se si vuole usare un proxy personalizzato, configurarlo prima di installare il provider. Quando si configurano impostazioni proxy personalizzate, viene eseguito un test per verificare la connessione proxy.
   * Se si usa un proxy personalizzato o se il proxy predefinito richiede l'autenticazione, è necessario immettere i dettagli del proxy, inclusi l'indirizzo e la porta.
   * Gli URL richiesti devono essere accessibili dal server VMM.
   * Se si usa un proxy personalizzato, viene creato automaticamente un account RunAs di VMM (DRAProxyAccount) usando le credenziali del proxy specificate. Configurare il server proxy in modo che l'account possa eseguire l'autenticazione. È possibile modificare le impostazioni dell'account RunAs nella console di VMM (**Impostazioni** > **Sicurezza** > **Account RunAs** > **DRAProxyAccount**). Per applicare la modifica, è necessario riavviare il servizio VMM.
10. In **Chiave di registrazione** selezionare il codice scaricato dal portale e copiato nel server VMM.
11. In **Vault name**verificare il nome dell'insieme di credenziali in cui verrà registrato il server.

    ![Server registration](./media/site-recovery-vmm-san/vault-creds.png)
12. L'impostazione di crittografia viene usata solo per VMM nella replica di Azure. È possibile ignorarla.

    ![Server registration](./media/site-recovery-vmm-san/encrypt.png)
13. In **Nome server**specificare un nome descrittivo per identificare il server VMM nell'insieme di credenziali. In una configurazione cluster specificare il nome del ruolo relativo al cluster VMM.
14. In **Initial cloud metadata sync** (Sincronizzazione iniziale dei metadati cloud) selezionare l'opzione relativa alla sincronizzazione dei metadati per tutti i cloud presenti sul server VMM. È necessario eseguire questa azione solo una volta in ogni server. Se non si vogliono sincronizzare tutti i cloud, è possibile lasciare deselezionata questa opzione e sincronizzare ogni cloud singolarmente nelle proprietà del cloud nella console VMM.

    ![Server registration](./media/site-recovery-vmm-san/friendly-name.png)

15. Fare clic su **Avanti** per completare il processo. Dopo la registrazione, i metadati del server VMM vengono recuperati da Azure Site Recovery. Il server viene visualizzato in **Server** > **Server VMM** nell'insieme di credenziali.

### <a name="command-line-installation"></a>Installazione dalla riga di comando

Il provider di Azure Site Recovery può essere installato anche dalla riga di comando seguente. Questo metodo può essere usato per installare il provider in un Server Core per Windows Server 2012 R2.

1. Scaricare il file di installazione del provider e il codice di registrazione in una cartella, ad esempio C:\ASR.
2. Arrestare il servizio VMM.
3. Estrarre il programma di installazione del provider: Eseguire questi comandi come amministratore:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Installare il provider:

        C:\ASR> setupdr.exe /i
5. Registrare il provider:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Parametri

* **/Credentials**: parametro obbligatorio per il percorso in cui si trova il file della chiave di registrazione.  
* **/FriendlyName**: parametro obbligatorio per il nome del server host Hyper-V visualizzato nel portale di Azure Site Recovery.
* **/EncryptionEnabled**: parametro facoltativo usato solo durante la replica da VMM ad Azure.
* **/proxyAddress**: parametro facoltativo che specifica l'indirizzo del server proxy.
* **/proxyport**: parametro facoltativo che specifica la porta del server proxy.
* **/proxyUsername**: parametro facoltativo che specifica il nome utente del proxy (se il proxy richiede autenticazione).
* **/proxyPassword**: parametro facoltativo che specifica la password per l'autenticazione con il server proxy (se il proxy richiede autenticazione).

## <a name="step-3-map-storage-arrays-and-pools"></a>Passaggio 3: Eseguire il mapping di array e pool di archiviazione

Eseguire il mapping degli array primari e secondari per specificare quale pool di archiviazione secondario dovrà ricevere i dati di replica dal pool primario. Il mapping delle unità di archiviazione deve essere eseguito prima di configurare la replica poiché le informazioni di mapping sono necessarie quando si abilita la protezione per i gruppi di replica.

Prima di iniziare, verificare che i cloud VMM siano visibili nell'insieme di credenziali. I cloud vengono rilevati durante la sincronizzazione di tutti i cloud nella fase di installazione del provider o durante la sincronizzazione di un cloud specifico nella console di VMM.

1. Fare clic su **Risorse** > **Archiviazione server** > **Mappa array di origine e di destinazione**.
    ![Registrazione del server](./media/site-recovery-vmm-san/storage-map.png)

2. Selezionare gli array di archiviazione presenti nel sito primario e mapparli agli array di archiviazione del sito secondario. In **Pool di archiviazione** selezionare un pool di archiviazione di origine e uno di destinazione di cui eseguire il mapping.

    ![Server registration](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-replication-settings"></a>Passaggio 4: Configurare le impostazioni di replica

Dopo la registrazione dei server VMM, configurare le impostazioni di protezione del cloud.

1. Nella pagina **Avvio rapido** fare clic su **Configurare la protezione per i cloud VMM**.
2. Nella scheda **Elementi protetti** selezionare il valore di **Configurazione** del cloud.
3. In **Destinazione** selezionare **VMM**.
4. In **Percorso di destinazione** selezionare il server VMM che gestisce il cloud da usare per il ripristino.
5. In **Cloud di destinazione** selezionare il cloud di destinazione da usare per il failover delle macchine virtuali.
   * È consigliabile selezionare un cloud di destinazione che soddisfi i requisiti di ripristino per le macchine virtuali da proteggere.
   * Un cloud può appartenere solo a una singola coppia di cloud, ad esempio cloud primario o di destinazione.
6. Site Recovery verifica che i cloud abbiano accesso all'archiviazione SAN e che sia stato eseguito il mapping degli array di archiviazione.
7. Se la verifica ha esito positivo, in **Tipo di replica** selezionare **SAN**.

Dopo il salvataggio delle impostazioni, viene creato un processo che può essere monitorato nella scheda **Processi**. È possibile modificare le impostazioni nella scheda **Configura**. Se si vuole modificare il percorso o il cloud di destinazione, è necessario rimuovere la configurazione del cloud e, successivamente, riconfigurare il cloud.

## <a name="step-5-enable-network-mapping"></a>Passaggio 5: Abilitare il mapping di rete

1. Nella pagina **Avvio rapido** fare clic su **Mapping reti**.
2. Selezionare il server VMM di origine e quindi il server VMM di destinazione a cui verrà eseguito il mapping delle reti. Vengono visualizzati l'elenco delle reti di origine e le relative reti di destinazione associate. Per le reti non mappate viene visualizzato un valore vuoto. Fare clic sull'icona delle informazioni accanto ai nomi delle reti di origine e di destinazione per visualizzare le subnet per ogni rete.
3. Selezionare una rete in **Rete nell'origine** e quindi fare clic su **Esegui mapping**. Il servizio rileva le reti VM nel server di destinazione e le visualizza.

    ![Architettura SAN](./media/site-recovery-vmm-san/network-map1.png)
4. Selezionare una delle reti VM dal server VMM di destinazione.

    ![Architettura SAN](./media/site-recovery-vmm-san/network-map2.png)

5. Quando si seleziona una rete di destinazione, vengono visualizzati i cloud protetti in cui viene usata la rete di origine. Vengono visualizzate anche le reti di destinazione disponibili. È consigliabile selezionare una rete di destinazione disponibile per tutti i cloud che vengono usati per la replica.
6. Fare clic sul segno di spunta per completare il processo di mapping. Viene avviato un processo che tiene traccia dell'avanzamento. È possibile visualizzarlo nella scheda **Processi**.

## <a name="step-6-enable-replication-for-replication-groups"></a>Passaggio 6: Abilitare la replica per i gruppi di replica

Prima di abilitare la protezione per le macchine virtuali è necessario abilitare la replica per i gruppi di replica di archiviazione.

1. Nella pagina **Proprietà** del cloud primario nel portale di Site Recovery aprire la scheda **Macchine virtuali** e fare clic su **Aggiungi gruppo di replica**.
2. Selezionare uno o più gruppi di replica VMM associati al cloud, verificare gli array di origine e di destinazione e specificare la frequenza di replica.

Site Recovery, VMM e i provider SMI-S eseguono il provisioning delle unità logiche di archiviazione del sito di destinazione e abilitano la replica di archiviazione. Se il gruppo di replica è già stato replicato, Site Recovery usa nuovamente la relazione di replica esistente e aggiorna le informazioni.

## <a name="step-7-enable-protection-for-virtual-machines"></a>Passaggio 7: Abilitare la protezione per le macchine virtuali


Quando si esegue la replica di un gruppo di archiviazione, abilitare la protezione per le macchine virtuali nella console di VMM in un uno dei modi seguenti:

* **Nuova macchina virtuale**: quando si crea una macchina virtuale, si abilita la replica e si associa la macchina virtuale al gruppo di replica.
  Se si sceglie questa opzione, VMM usa la funzione di posizionamento intelligente per posizionare in modo ottimale l'archiviazione della macchina virtuale sulle unità logiche del gruppo di replica. Site Recovery gestisce inoltre la creazione di una macchina virtuale ombra nel sito secondario e alloca la capacità necessaria per consentire l'avvio della macchina virtuale di replica dopo il failover.
* **Macchina virtuale esistente**: se in VMM è già stata distribuita una macchina virtuale, è possibile abilitare la replica ed eseguire la migrazione dell'archiviazione in un gruppo di replica. Al termine dell'operazione, VMM e Site Recovery rilevano la nuova macchina virtuale e iniziano a gestirla in Site Recovery. Nel sito secondario viene creata una macchina virtuale ombra e viene allocata la capacità necessaria per consentire l'avvio della macchina virtuale di replica dopo il failover.

![Abilitare la protezione](./media/site-recovery-vmm-san/enable-protect.png)

Dopo l'abilitazione delle macchine virtuali per la replica, le VM vengono visualizzate nella console di Site Recovery. È possibile ora visualizzare le proprietà delle macchine virtuali e monitorare lo stato e i gruppi di replica di failover che contengono più macchine virtuali. Nella replica SAN tutte le macchine virtuali associate a un gruppo di replica devono essere sottoposte a failover insieme. Il failover, infatti, viene eseguito prima a livello di archiviazione. È quindi importante raggruppare in modo corretto i gruppi di replica e collocare insieme solo le macchine virtuali associate.

> [!NOTE]
> Dopo avere abilitato la replica per una macchina virtuale, non aggiungere i relativi dischi rigidi virtuali ai numeri di unità logiche a meno che non si trovino in un gruppo di replica di Site Recovery. I dischi rigidi virtuali vengono rilevati da Site Recovery sono se si trovano in un gruppo di replica relativo.
>
>

È possibile tenere traccia dell'avanzamento, inclusa la replica iniziale, nella scheda **Processi**. Dopo l'esecuzione del processo di finalizzazione della protezione la macchina virtuale è pronta per il failover.

![Processo di protezione delle macchine virtuali](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>Passaggio 8: Testare la distribuzione

Testare la distribuzione per assicurarsi che il failover delle macchine virtuali venga eseguito come previsto. A questo scopo, creare un piano di ripristino ed eseguire un failover di test.

1. Nella scheda **Piani di ripristino** fare clic su **Crea piano di ripristino**.
2. Specificare un nome per il piano di ripristino e selezionare i server VMM di origine e di destinazione. Sul server di origine devono essere eseguite macchine virtuali abilitate per il failover e il ripristino. Selezionare **SAN** per visualizzare solo i cloud configurati per la replica SAN.

    ![Crea piano di ripristino](./media/site-recovery-vmm-san/r-plan.png)

3. In **Seleziona macchine virtuali** selezionare i gruppi di replica. Tutte le macchine virtuali associate al gruppo vengono aggiunte al piano di ripristino. Le macchine virtuali verranno aggiunte al gruppo predefinito del piano di ripristino (Gruppo 1). Se necessario, è possibile aggiungere più gruppi. Dopo la replica, le macchine virtuali vengono numerate in base all'ordine dei gruppi di piano di ripristino.

    ![Selezionare le macchine virtuali](./media/site-recovery-vmm-san/r-plan-vm.png)
4. Dopo la creazione, il piano di ripristino viene visualizzato nell'elenco all'interno della scheda **Piani di ripristino**. Selezionare il piano e scegliere **Failover di test**.
5. Nella pagina **Conferma failover di test** selezionare **Nessuno**. Se si abilita questa opzione, le macchine virtuali di replica su cui è stato eseguito il failover non verranno connesse in rete. Viene testato il funzionamento corretto del failover delle macchine virtuali, ma non l'ambiente di rete. Per altre informazioni su altre opzioni di rete, vedere [Failover in Site Recovery](site-recovery-failover.md).

    ![Seleziona rete di test](./media/site-recovery-vmm-san/test-fail1.png)

6. La macchina virtuale di test viene creata nello stesso host in cui è presente la macchina virtuale di replica, ma non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.
2. Dopo la replica, la macchina virtuale di replica avrà un indirizzo IP diverso da quello della macchina virtuale primaria. Se gli indirizzi vengono emessi da un protocollo DHCP, l'aggiornamento viene eseguito automaticamente. Se non si usa DHCP e si vogliono usare gli stessi indirizzi, è necessario eseguire alcuni script.
3. Eseguire lo script seguente per recuperare l'indirizzo IP:

       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  

4. Eseguire lo script di esempio per aggiornare DNS. Specificare l'indirizzo IP recuperato.

       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito un failover di test per verificare che l'ambiente funzioni nel modo previsto, vedere [Failover in Site Recovery](site-recovery-failover.md) per altre informazioni sui diversi tipi di failover.

