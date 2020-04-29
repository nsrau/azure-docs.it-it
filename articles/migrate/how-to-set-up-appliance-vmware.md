---
title: Configurare un'appliance Azure Migrate per VMware
description: Informazioni su come configurare un appliance Azure Migrate per la valutazione e la migrazione di macchine virtuali VMware.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: b32c6a9b703e4d341fe353d6b472ea7a18adadf3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538257"
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
- Scaricare un file modello OVA e importarlo nel server vCenter.
- Creare l'appliance e verificare che riesca a connettersi allo strumento Valutazione server di Azure Migrate.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate.

## <a name="download-the-ova-template"></a>Scaricare il modello OVA

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** fare clic su **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** fare clic su **Sì, con VMware vSphere Hypervisor**.
3. Fare clic su **Scarica** per scaricare il file di modello OVA.

  ![Selezioni per il download di un file OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file OVA, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Per la versione più recente del dispositivo, l'hash generato deve corrispondere a queste [Impostazioni](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security).



## <a name="create-the-appliance-vm"></a>Creare l'appliance VM

Importare il file scaricato e creare una macchina virtuale.

1. Nella console di vSphere client fare clic su **file** > **Distribuisci modello OVF**.
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


## <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta. Se si distribuisce l'appliance usando uno script anziché un modello OVA, i primi due passaggi della procedura non sono applicabili.

1. Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance facendo clic sul relativo collegamento.
4. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **License** (Licenza): Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
        - Fare clic su **Proxy settings** (Impostazioni proxy) e specificare l'indirizzo e la porta di ascolto del proxy in formato http://ProxyIPAddress o http://ProxyFQDN.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
    - **Time sync** (Sincronizzazione ora): viene verificata l'ora. Per il corretto funzionamento dell'individuazione delle macchine virtuali, l'ora dell'appliance deve essere sincronizzata con l'ora Internet.
    - **Installa aggiornamenti**: Azure migrate verifica che siano installati gli aggiornamenti più recenti dell'appliance.
    - **Installare VDDK**: Azure migrate verifica che sia installato VMware vSphere Virtual Disk Development Kit (VDDK).
        - Azure migrates USA VDDK per replicare i computer durante la migrazione ad Azure.
        - Scaricare VDDK 6.7 da VMware ed estrarre il contenuto dello ZIP scaricato nella posizione specificata nell'appliance.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Fare clic su **Log in** (Accedi). Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
2. Nella nuova scheda accedere con le credenziali di Azure.
    - Accedere con il nome utente e la password.
    - L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web.
2. Selezionare la sottoscrizione in cui è stato creato il progetto di Azure Migrate. Quindi selezionare il progetto.
3. Specificare un nome per l'appliance. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
4. Fare clic su **Register**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Avviare l'individuazione continua fornendo server vCenter e le credenziali della macchina virtuale

L'appliance deve connettersi al server vCenter per individuare la configurazione e i dati sulle prestazioni delle macchine virtuali.

### <a name="specify-vcenter-server-details"></a>Specificare i dettagli del server vCenter
1. In **Specificare i dettagli del server vCenter** specificare il nome di dominio completo o l'indirizzo IP del server vCenter. È possibile lasciare la porta predefinita o specificare una porta personalizzata su cui il server vCenter rimane in ascolto.
2. In **Nome utente** e **Password** specificare le credenziali dell'account di sola lettura che verranno usate dall'appliance per individuare le macchine virtuali nel server vCenter. È possibile ridurre l'ambito dell'individuazione limitando l'accesso all'account vCenter. [Altre informazioni](set-discovery-scope.md)
3. Fare clic su **Convalida connessione** per verificare che l'appliance sia in grado di connettersi al server vCenter.

### <a name="specify-vm-credentials"></a>Specificare le credenziali della VM
Per individuare le applicazioni, i ruoli, le funzionalità e visualizzare le dipendenze delle macchine virtuali, è possibile specificare le credenziali della VM che hanno accesso alle macchine virtuali VMware. È possibile aggiungere credenziali per le macchine virtuali Windows e per le macchine virtuali Linux. [Altre informazioni](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) sui privilegi di accesso necessari.

> [!NOTE]
> Questo input è facoltativo: è necessario per abilitare l'individuazione delle applicazioni e la visualizzazione delle dipendenze senza agenti.

1. In **Individua applicazioni e dipendenze nelle macchine virtuali** fare clic su **Aggiungi credenziali**.
2. Selezionare il **sistema operativo**.
3. Specificare un nome descrittivo per le credenziali.
4. In **nome utente** e **password**specificare un account che disponga almeno dell'accesso guest nelle macchine virtuali.
5. Fare clic su **Aggiungi**.

Dopo aver specificato le credenziali server vCenter e VM (facoltativo), fare clic su **Salva e avvia individuazione** per avviare l'individuazione dell'ambiente locale.

Per visualizzare i metadati delle VM individuate nel portale occorre attendere circa 15 minuti. L'individuazione di applicazioni, ruoli e funzionalità installate richiede tempo e la durata dipende dal numero di macchine virtuali individuate. Per le macchine virtuali 500, la visualizzazione dell'inventario delle applicazioni nel portale di Azure Migrate richiede circa 1 ora.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le esercitazioni per la [valutazione VMware](tutorial-assess-vmware.md) e la [migrazione senza agenti](tutorial-migrate-vmware.md).
