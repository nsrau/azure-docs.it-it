---
title: Individuare le macchine virtuali VMware con Valutazione server di Azure Migrate
description: Informazioni su come individuare macchine virtuali VMware locali con lo strumento Valutazione server di Azure Migrate
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: cbe1561f58af8f65285ffb005b0232bff8225d3b
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604054"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Esercitazione: Individuare le macchine virtuali VMware con Valutazione server

Il percorso di migrazione ad Azure prevede l'individuazione dei carichi di lavoro e dell'inventario locale. 

Questa esercitazione mostra come individuare macchine virtuali VMware locali con lo strumento Azure Migrate: Valutazione server, usando un'appliance leggera di Azure Migrate. L'appliance viene distribuita come macchina virtuale VMware, per individuare in modo continuativo i metadati relativi alle macchine virtuali e alle prestazioni, le app in esecuzione nelle macchine virtuali e le dipendenze delle macchine virtuali.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un account Azure.
> * Preparare l'ambiente VMware per l'individuazione.
> * Creare un progetto di Azure Migrate.
> * Configurare l'appliance di Azure Migrate.
> * Avviare l'individuazione continua.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e, laddove possibile, prevedono l'uso delle opzioni predefinite.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, verificare che siano rispettati i prerequisiti seguenti.


**Requisito** | **Dettagli**
--- | ---
**Server vCenter/Host ESXi** | È necessario un server vCenter che esegue la versione 5.5, 6.0, 6.5 o 6.7.<br/><br/> Le macchine virtuali devono essere ospitate in un host ESXi che esegue la versione 5.5 o successiva.<br/><br/> Nel server vCenter consentire le connessioni in ingresso sulla porta TCP 443 in modo che l'appliance possa raccogliere i dati delle valutazioni.<br/><br/> Per impostazione predefinita, l'appliance si connette a vCenter sulla porta 443. Se il server vCenter è in ascolto su una porta diversa, è possibile modificare la porta quando ci si connette al server dall'appliance per avviare l'individuazione.<br/><br/> Nel server EXSi che ospita le macchine virtuali, assicurarsi che l'accesso in ingresso sia consentito sulla porta TCP 443 per l'individuazione delle app.
**Appliance** | Il server vCenter ha bisogno di risorse per allocare una macchina virtuale per l'appliance di Azure Migrate:<br/><br/> - Windows Server 2016<br/><br/> - 32 GB di RAM, otto CPU virtuali e circa 80 GB di spazio di archiviazione su disco.<br/><br/> - Un commutatore virtuale esterno e accesso a Internet attivo per la macchina virtuale, direttamente o tramite un proxy.
**Macchine virtuali** | Per usare questa esercitazione, le macchine virtuali Windows devono eseguire Windows Server 2016, 2012 R2, 2012 o 2008 R2.<br/><br/> Le macchine virtuali Linux devono eseguire Red Hat Enterprise Linux 7/6/5, Ubuntu Linux 14.04/16.04, Debian 7/8, Oracle Linux 6/7 o CentOS 5/6/7.<br/><br/> Nelle macchine virtuali devono essere installati e in esecuzione gli strumenti VMware (versione successiva alla 10.2.0).<br/><br/> Nelle macchine virtuali Windows deve essere installato Windows PowerShell 2.0 o versione successiva.


## <a name="prepare-an-azure-user-account"></a>Preparare un account utente Azure

Per creare un progetto di Azure Migrate e registrare l'appliance di Azure Migrate, è necessario un account con:
- Autorizzazioni di collaboratore o proprietario per una sottoscrizione di Azure.
- Autorizzazioni per la registrazione di app Azure Active Directory.

Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione. Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare le autorizzazioni nel modo seguente:

1. Nel portale di Azure cercare "sottoscrizioni" e in **Servizi** selezionare **Sottoscrizioni**.

    ![Casella di ricerca per cercare la sottoscrizione di Azure](./media/tutorial-discover-vmware/search-subscription.png)

2. Nella pagina **Sottoscrizioni** selezionare la sottoscrizione in cui creare un progetto di Azure Migrate. 
3. Nella sottoscrizione selezionare **Controllo di accesso (IAM)**  > **Verifica l'accesso**.
4. In **Verifica l'accesso** cercare l'account utente pertinente.
5. In **Aggiungi un'assegnazione di ruolo** fare clic su **Aggiungi**.

    ![Cercare un account utente per verificare l'accesso e assegnare un ruolo](./media/tutorial-discover-vmware/azure-account-access.png)

6. In **Aggiungi un'assegnazione di ruolo** selezionare il ruolo Collaboratore o Proprietario e selezionare l'account (azmigrateuser nell'esempio). Fare quindi clic su **Salva**.

    ![Viene aperta la pagina Aggiungi assegnazione di ruolo per assegnare un ruolo all'account](./media/tutorial-discover-vmware/assign-role.png)

7. Nel portale cercare gli utenti e in **Servizi** selezionare **Utenti**.
8. In **Impostazioni utente** verificare che gli utenti di Azure AD possano registrare le applicazione (impostato su **Sì** per impostazione predefinita).

    ![Verificare che in Impostazioni utente che gli utenti possano registrare le app Active Directory](./media/tutorial-discover-vmware/register-apps.png)


## <a name="prepare-vmware"></a>Preparare VMware

Nel server vCenter creare un account utilizzabile dall'appliance per accedere al server vCenter e verificare che le porte necessarie siano aperte. È anche necessario un account utilizzabile dall'appliance per accedere alle macchine virtuali. 

### <a name="create-an-account-to-access-vcenter"></a>Creare un account per l'accesso a vCenter

In vSphere Web Client configurare un account come descritto di seguito:

1. Usando un account con privilegi di amministratore, in vSphere Web Client > selezionare **Administration** (Amministrazione).
2. Selezionare **Access** (Accesso) e quindi **SSO Users and Groups** (Utenti e gruppi SSO).
3. In **Users** (Users) aggiungere un nuovo utente.
4. In **New User** (Nuovo utente) digitare i dettagli dell'account. Fare quindi clic su **OK**.
5. In **Global Permissions** (Autorizzazioni globali) selezionare l'account utente e assegnare all'account il ruolo **Read-only** (Sola lettura). Fare quindi clic su **OK**.
6. In **Roles** (Ruoli) selezionare il ruolo **Read-only** (Sola lettura) e in **Privileges** (Privilegi) selezionare **Guest Operations** (Operazioni guest). Questi privilegi sono necessari per individuare le app in esecuzione nelle macchine virtuali e analizzare le dipendenze delle macchine virtuali.
 
    ![Casella di controllo per consentire le operazioni guest sul ruolo Read-only](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Creare un account per l'accesso alle macchine virtuali

L'appliance accede alle macchine virtuali per individuare le app e analizzare le dipendenze delle macchine virtuali. L'appliance non installa alcun agente nelle macchine virtuali.

1. Creare un account amministratore locale utilizzabile dall'appliance per individuare le app e le dipendenze nelle macchine virtuali Windows.
2. Per i computer Linux creare un account utente con privilegi radice o, in alternativa, un account utente con queste autorizzazioni per i file /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

> [!NOTE]
> Azure Migrate supporta una credenziale per l'individuazione di app in tutti i server Windows e una credenziale per l'individuazione di app in tutti i computer Linux.


## <a name="set-up-a-project"></a>Configurare un progetto

Configurare un nuovo progetto di Azure Migrate.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Panoramica** selezionare **Crea progetto**.
5. In **Crea progetto** selezionare il gruppo di risorse e la sottoscrizione di Azure. Creare un gruppo di risorse, se non è presente.
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caselle per il nome del progetto e l'area](./media/tutorial-discover-vmware/new-project.png)

7. Selezionare **Crea**.
8. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate.

Lo strumento **Azure Migrate: Valutazione server** viene aggiunto per impostazione predefinita al nuovo progetto.

![Pagina che mostra lo strumento Valutazione server aggiunto per impostazione predefinita](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>Configurare l'appliance

Per configurare l'appliance con un modello OVA:
- Specificare un nome di appliance e generare una chiave del progetto di Azure Migrate nel portale
- Scaricare un file modello OVA e importarlo nel server vCenter.
- Creare l'appliance e verificare che riesca a connettersi allo strumento Valutazione server di Azure Migrate.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate con la chiave del progetto di Azure Migrate.

> [!NOTE]
> Se per qualche motivo non è possibile usare il modello, è possibile configurare l'appliance usando uno script di PowerShell. [Altre informazioni](deploy-appliance-script.md#set-up-the-appliance-for-vmware)


### <a name="deploy-with-ova"></a>Distribuire con OVA

Per configurare l'appliance con un modello OVA:
- Specificare un nome di appliance e generare una chiave del progetto di Azure Migrate nel portale
- Scaricare un file modello OVA e importarlo nel server vCenter.
- Creare l'appliance e verificare che riesca a connettersi allo strumento Valutazione server di Azure Migrate.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate con la chiave del progetto di Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Generare la chiave del progetto Azure Migrate

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere Hypervisor**.
3. In **1: Generare la chiave del progetto Azure Migrate** specificare un nome per l'appliance Azure Migrate che verrà configurata per l'individuazione delle macchine virtuali VMware. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Individua macchine virtuali durante la creazione delle risorse.
1. Al termine della creazione delle risorse di Azure, viene generata una **Chiave progetto Azure Migrate**.
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="download-the-ova-template"></a>Scaricare il modello OVA

In **2: Scaricare l'appliance di Azure Migrate** selezionare il file OVA e fare clic su **Scarica**. 


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file OVA, verificarne la sicurezza:

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per il file OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Verificare le versioni più recenti dell'appliance e i valori hash:

    - Per il cloud pubblico di Azure:
    
        **Algoritmo** | **Scaricare** | **SHA256**
        --- | --- | ---
        VMware (11,6 GB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Per Azure per enti pubblici:
    
        **Algoritmo** | **Scaricare** | **SHA256**
        --- | --- | ---
        VMware (85 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140337) | 47179f47eba2842337bbe533c424dd1da56baccdcf68b1d87b71a5a4280108c2




### <a name="create-the-appliance-vm"></a>Creare l'appliance VM

Importare il file scaricato e creare una macchina virtuale.

1. Nella console di vSphere Client fare clic su **File** > **Deploy OVF Template** (Distribuisci modello OVF).
2. Nella procedura guidata Distribuire il modello OVF > **Origine** specificare il percorso del file con estensione ova.
3. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la VM. Selezionare l'oggetto inventario in cui verrà ospitata la VM.
5. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale.
6. In **Storage** (Archiviazione) specificare la destinazione di archiviazione della macchina virtuale.
7. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
8. In **Network Mapping** (Mapping di rete) specificare la rete a cui si connetterà la macchina virtuale. La rete richiede la connettività Internet per l'invio dei metadati allo strumento Valutazione server di Azure Migrate.
9. Rivedere e confermare le impostazioni e quindi fare clic su **Finish** (Fine).


### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che la macchina virtuale dell'appliance possa connettersi agli URL di Azure per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).


### <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [script di PowerShell](deploy-appliance-script.md) invece del modello OVA scaricato, i primi due passaggi di questa procedura non sono pertinenti.

1. Nella console del client vSphere fare clic con il pulsante destro del mouse su sulla macchina virtuale e quindi scegliere **Open Console** (Apri console).
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance selezionando il relativo collegamento.
1. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
   - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
     - Fare clic su **Set up proxy** (Configura proxy) per specificare l'indirizzo proxy (nel formato http://ProxyIPAddress o http://ProxyFQDN) e la porta di ascolto.
     - Se il proxy richiede l'autenticazione, specificare le credenziali.
     - È supportato solo il proxy HTTP.
     - Se sono stati aggiunti dettagli del proxy o sono stati disabilitati il proxy e/o l'autenticazione, fare clic su **Save** (Salva) per attivare di nuovo il controllo della connettività.
   - **Time sync** (Sincronizzazione ora): Per il corretto funzionamento dell'individuazione delle macchine virtuali, l'ora dell'appliance deve essere sincronizzata con l'ora Internet.
   - **Install updates** (Installa aggiornamenti): l'appliance verifica che siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **View appliance services** (Visualizza servizi appliance) per visualizzare lo stato e le versioni dei componenti in esecuzione nel dispositivo.
   - **Install VDDK** (Installa VDDK): l'appliance verifica che VMware vSphere Virtual Disk Development Kit (VDDK) sia installato. Se non è già installato, scaricare VDDK 6.7 da VMware ed estrarre il contenuto dello ZIP scaricato nel percorso specificato nell'appliance, come indicato nelle **istruzioni di installazione**.

     Migrazione server di Azure Migrate usa VDDK per replicare le macchine virtuali durante la migrazione ad Azure. 
1. Se lo si desidera, è possibile **rieseguire i prerequisiti** in qualsiasi momento durante la configurazione dell'appliance per verificare se l'appliance soddisfa tutti i prerequisiti.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave del progetto Azure Migrate** copiata dal portale. Se non si dispone della chiave, passare a **Valutazione server > Individua > Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Fare clic su **Accedi**. Verrà aperto un prompt di accesso di Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella nuova scheda accedere con nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web. 
4. Se l'account utente di Azure usato per la registrazione ha le [autorizzazioni](tutorial-prepare-vmware.md#prepare-azure) corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Al termine della registrazione dell'appliance è possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.



## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

L'appliance deve connettersi al server vCenter per individuare la configurazione e i dati sulle prestazioni delle macchine virtuali.

1. In **Passaggio 1: Specificare le credenziali del server vCenter**, fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **Nome utente** e **Password** per l'account del server vCenter che verrà usato dall'appliance per individuare le macchine virtuali nell'istanza del server vCenter.
    - È necessario aver configurato un account con le autorizzazioni necessarie nell'[esercitazione precedente](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Per definire l'ambito dell'individuazione di oggetti VMware specifici (server vCenter data center, cluster, una cartella di cluster, host, una cartella di host o singole VM), rivedere le istruzioni riportate in [questo articolo](set-discovery-scope.md) per limitare l'account usato da Azure Migrate.
1. In **Passaggio 2: Specificare i dettagli del server vCenter**, fare clic su **Aggiungi origine di individuazione** per selezionare il nome descrittivo per le credenziali nell'elenco a discesa, specificare **Indirizzo IP/FQDN** dell'istanza del server vCenter. È possibile lasciare l'impostazione predefinita per **Porta** (443) o specificare una porta personalizzata sulla quale sarà in ascolto il server vCenter e fare clic su **Salva**.
1. Quando si fa clic su Salva, l'appliance tenterà di convalidare la connessione al server vCenter con le credenziali specificate e visualizzerà lo **Stato di convalida** nella tabella per ogni indirizzo IP/FQDN di server vCenter.
1. È possibile **riconvalidare** la connettività al server vCenter in qualsiasi momento prima di avviare l'individuazione.
1. In **Passaggio 3: Specificare le credenziali della macchina virtuale per individuare le applicazioni installate e per eseguire il mapping delle dipendenze senza agente**, fare clic su **Aggiungi credenziali** e specificare il sistema operativo per il quale vengono fornite le credenziali, il nome descrittivo per le credenziali e **Nome utente** e **Password**. Fare quindi clic su **Salva**.

    - Facoltativamente, è possibile aggiungere qui le credenziali se è stato creato un account da usare per la [funzionalità di individuazione delle applicazioni](how-to-discover-applications.md) o la [funzionalità di analisi delle dipendenze senza agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se non si vogliono usare queste funzionalità, è possibile fare clic sul dispositivo di scorrimento per ignorare il passaggio. È possibile cambiare idea in qualsiasi momento in un secondo momento.
    - Esaminare le credenziali necessarie per l'[individuazione delle applicazioni](migrate-support-matrix-vmware.md#application-discovery-requirements) o per l'[analisi delle dipendenze senza agente](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Fare clic su **Avvia individuazione** per iniziare l'individuazione delle macchine virtuali. Dopo l'avvio dell'individuazione, è possibile controllare lo stato dell'individuazione per ogni indirizzo IP/FQDN del server vCenter nella tabella.

L'individuazione funziona come segue:
- Sono necessari circa 15 minuti prima che i metadati delle VM individuate vengano visualizzati nel portale.
- L'individuazione di applicazioni, ruoli e funzionalità installati richiede tempo. La durata dipende dal numero di macchine virtuali individuate. Per 500 VM, è necessaria circa un'ora prima che l'inventario di applicazioni venga visualizzato nel portale di Azure Migrate.


## <a name="next-steps"></a>Passaggi successivi

- [Valutare le macchine virtuali VMware](tutorial-assess-vmware.md) per la migrazione a macchine virtuali di Azure.
- [Esaminare i dati](migrate-appliance.md#collected-data---vmware) raccolti dall'appliance durante l'individuazione.
