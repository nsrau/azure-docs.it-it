---
title: Configurare un'appliance Azure Migrate per VMware
description: Informazioni su come configurare un appliance Azure Migrate per la valutazione e la migrazione di macchine virtuali VMware.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: c9a9f1567f984fc5770b47d3998610cb69643360
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923619"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurare un'appliance per le macchine virtuali VMware

Seguire questo articolo per configurare il Azure Migrate Appliance per la valutazione con lo strumento di [valutazione Azure migrate: server](migrate-services-overview.md#azure-migrate-server-assessment-tool) e per la migrazione senza agenti usando lo strumento di [migrazione Azure migrate: server](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Il [Azure migrate Appliance](migrate-appliance.md) è un'appliance semplice usata da Azure migrate: valutazione del server e migrazione del server per individuare le macchine virtuali VMware locali, inviare i dati sulle prestazioni e i metadati delle VM in Azure e per la replica di macchine virtuali VMware durante la migrazione senza agenti.

È possibile distribuire l'appliance usando due metodi:

- Configurare l'appliance in una macchina virtuale VMware usando un modello OVA scaricato. Questo è il metodo descritto in questo articolo.
- Configurare l'appliance in una macchina virtuale VMware o in un computer fisico con uno script di installazione di PowerShell. [Questo metodo](deploy-appliance-script.md) deve essere usato se non è possibile configurare una macchina virtuale con un modello OVA o se si usa Azure per enti pubblici.

Dopo aver creato l'appliance, verificare che sia in grado di connettersi ad Azure Migrate: Valutazione server, configurarla per la prima volta e registrarla nel progetto di Azure Migrate.


## <a name="appliance-deployment-ova"></a>Distribuzione di Appliance (OVA)

Per configurare l'appliance usando un modello OVA è necessario:
- Specificare un nome per il dispositivo e generare una chiave del progetto Azure Migrate nel portale
- Scaricare un file modello OVA e importarlo nel server vCenter.
- Creare l'appliance e verificare che riesca a connettersi allo strumento Valutazione server di Azure Migrate.
- Configurare il dispositivo per la prima volta e registrarlo con il progetto Azure Migrate usando la chiave del progetto Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Genera la chiave del progetto Azure Migrate

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con VMware vSphere Hypervisor**.
3. In **1: generare Azure migrate chiave del progetto**, specificare un nome per il dispositivo Azure migrate che verrà configurato per l'individuazione delle macchine virtuali VMware. il nome deve essere alfanumerico con un massimo di 14 caratteri.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Discover machines durante la creazione delle risorse.
1. Una volta completata la creazione delle risorse di Azure, viene generata una **chiave del progetto Azure migrate** .
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la relativa configurazione.

### <a name="download-the-ova-template"></a>Scaricare il modello OVA
In **2: scaricare Azure migrate Appliance**, selezionare il. File OVA e fare clic su **download**. 


   ![Selezioni per le macchine virtuali di individuazione](./media/tutorial-assess-vmware/servers-discover.png)


   ![Selezioni per genera chiave](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file OVA, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Per la versione più recente del dispositivo, l'hash generato deve corrispondere a queste [Impostazioni](./tutorial-assess-vmware.md#verify-security).



## <a name="create-the-appliance-vm"></a>Creare l'appliance VM

Importare il file scaricato e creare una macchina virtuale.

1. Nella console di vSphere client fare clic su **file**  >  **Distribuisci modello OVF**.
![Comando di menu per la distribuzione di un modello OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Nella procedura guidata Distribuire il modello OVF > **Origine** specificare il percorso del file con estensione ova.
3. In **Name** (Nome) e **Location** (Posizione) specificare un nome descrittivo per la VM. Selezionare l'oggetto inventario in cui verrà ospitata la VM.
5. In **Host/Cluster** specificare l'host o il cluster in cui verrà eseguita la macchina virtuale.
6. In **Storage** (Archiviazione) specificare la destinazione di archiviazione della macchina virtuale.
7. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
8. In **mapping di rete**specificare la rete a cui si connetterà la macchina virtuale. La rete richiede la connettività Internet per l'invio dei metadati allo strumento Valutazione server di Azure Migrate.
9. Rivedere e confermare le impostazioni e quindi fare clic su **Finish** (Fine).


## <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che la macchina virtuale dell'appliance possa connettersi agli URL di Azure per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).


### <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [script di PowerShell](deploy-appliance-script.md) invece del modello OVA scaricato, i primi due passaggi di questa procedura non sono pertinenti.

1. Nella console del client vSphere fare clic con il pulsante destro del mouse su sulla macchina virtuale e quindi scegliere **Open Console** (Apri console).
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance selezionando il relativo collegamento.
1. Accettare le **condizioni di licenza**e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
   - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
     - Fare clic su **Configura proxy** per specificare l'indirizzo del proxy, nel formato http://ProxyIPAddress o nella http://ProxyFQDN) porta di attesa.
     - Se il proxy richiede l'autenticazione, specificare le credenziali.
     - È supportato solo il proxy HTTP.
     - Se sono stati aggiunti dettagli del proxy o è stato disabilitato il proxy e/o l'autenticazione, fare clic su **Salva** per attivare nuovamente il controllo della connettività.
   - **Time sync** (Sincronizzazione ora): Per il corretto funzionamento dell'individuazione delle macchine virtuali, l'ora dell'appliance deve essere sincronizzata con l'ora Internet.
   - **Install updates** (Installa aggiornamenti): l'appliance verifica che siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **Visualizza servizi Appliance** per visualizzare lo stato e le versioni dei componenti in esecuzione nel dispositivo.
   - **Install VDDK** (Installa VDDK): l'appliance verifica che VMware vSphere Virtual Disk Development Kit (VDDK) sia installato. Se non è installato, scaricare VDDK 6,7 da VMware ed estrarre il contenuto del file zip scaricato nella posizione specificata nell'appliance, come indicato nelle istruzioni di **installazione**.

     Migrazione server di Azure Migrate usa VDDK per replicare le macchine virtuali durante la migrazione ad Azure. 
1. Se lo si desidera, è possibile **rieseguire i prerequisiti** in qualsiasi momento durante la configurazione dell'appliance per verificare se l'appliance soddisfa tutti i prerequisiti.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave di progetto Azure migrate** copiata dal portale. Se non si dispone della chiave, passare a **Server Assessment> individua> Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Fare clic su **Accedi**. Verrà aperto un prompt di accesso di Azure in una nuova scheda del browser. Se non viene visualizzato, assicurarsi di aver disattivato il blocco popup nel browser.
1. Nella nuova scheda accedere con nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web. 
4. Se l'account utente di Azure usato per la registrazione dispone delle [autorizzazioni](tutorial-prepare-vmware.md#prepare-azure) corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Dopo aver registrato correttamente il dispositivo, è possibile visualizzare i dettagli di registrazione facendo clic su **Visualizza dettagli**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

L'appliance deve connettersi al server vCenter per individuare la configurazione e i dati sulle prestazioni delle macchine virtuali.

1. In **passaggio 1: fornire le credenziali server vCenter**fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **nome utente** e **password** per l'account server vCenter che l'appliance userà per individuare le macchine virtuali nell'istanza di server vCenter.
    - È necessario aver configurato un account con le autorizzazioni necessarie nell'[esercitazione precedente](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Per definire l'ambito dell'individuazione di oggetti VMware specifici (server vCenter data center, cluster, una cartella di cluster, host, una cartella di host o singole VM), rivedere le istruzioni riportate in [questo articolo](set-discovery-scope.md) per limitare l'account usato da Azure Migrate.
1. In **passaggio 2:** specificare i dettagli della server vCenter fare clic su **Aggiungi origine individuazione** per selezionare il nome descrittivo per le credenziali dall'elenco a discesa, quindi specificare l' **indirizzo IP o** il nome di dominio completo dell'istanza di server vCenter. È possibile lasciare la **porta** predefinita (443) o specificare una porta personalizzata su cui server vCenter è in ascolto e fare clic su **Salva**.
1. Quando si fa clic su Salva, il dispositivo tenterà di convalidare la connessione al server vCenter con le credenziali specificate e indicherà lo **stato di convalida** nella tabella rispetto al server vCenter indirizzo IP/FQDN.
1. È possibile **rivalidare** la connettività a server vCenter in qualsiasi momento prima di avviare l'individuazione.
1. In **passaggio 3: fornire le credenziali della macchina virtuale per individuare le applicazioni installate e per eseguire il mapping delle dipendenze senza agenti**, fare clic su **Aggiungi credenziali**e specificare il sistema operativo per il quale vengono fornite le credenziali, il nome descrittivo per le credenziali e il nome **utente** e la **password**. Quindi fare clic su **Salva**.

    - Facoltativamente, è possibile aggiungere qui le credenziali se è stato creato un account da usare per la [funzionalità di individuazione delle applicazioni](how-to-discover-applications.md) o la [funzionalità di analisi delle dipendenze senza agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se non si vuole usare queste funzionalità, è possibile fare clic sul dispositivo di scorrimento per ignorare il passaggio. È possibile invertire lo scopo in qualsiasi momento in un secondo momento.
    - Esaminare le credenziali necessarie per l' [individuazione dell'applicazione](migrate-support-matrix-vmware.md#application-discovery-requirements)o per l' [analisi delle dipendenze senza agenti](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Fare clic su **Avvia individuazione per avviare**l'individuazione delle macchine virtuali. Dopo che l'individuazione è stata avviata correttamente, è possibile controllare lo stato di individuazione in base al server vCenter indirizzo IP/FQDN nella tabella.

L'individuazione funziona come segue:
- Sono necessari circa 15 minuti prima che i metadati delle VM individuate vengano visualizzati nel portale.
- L'individuazione di applicazioni, ruoli e funzionalità installati richiede tempo. La durata dipende dal numero di macchine virtuali individuate. Per 500 VM, è necessaria circa un'ora prima che l'inventario di applicazioni venga visualizzato nel portale di Azure Migrate.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le esercitazioni per la [valutazione VMware](tutorial-assess-vmware.md) e la [migrazione senza agenti](tutorial-migrate-vmware.md).
