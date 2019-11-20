---
title: Configurare un'appliance Azure Migrate per VMware
description: Informazioni su come configurare un appliance Azure Migrate per la valutazione e la migrazione di macchine virtuali VMware.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 1489d29f854b02cf493493fe022c73dc050e2615
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185864"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configurare un'appliance per le macchine virtuali VMware

Questo articolo descrive come configurare l'appliance Azure Migrate se si valutano macchine virtuali VMware con lo strumento Azure Migrate server assessment oppure si esegue la migrazione di macchine virtuali VMware in Azure con migrazione senza agenti usando lo strumento di migrazione Azure Migrate server.

L'appliance di macchine virtuali VMware è un dispositivo leggero usato dalla valutazione/migrazione del server Azure Migrate per eseguire le operazioni seguenti:

- individuare le macchine virtuali VMware locali.
- Inviare i metadati e i dati sulle prestazioni per le macchine virtuali individuate a Azure Migrate valutazione/migrazione del server.

[Altre](migrate-appliance.md) informazioni sull'appliance Azure migrate.


## <a name="appliance-deployment-steps"></a>Passaggi di distribuzione dell'appliance

Per configurare l'appliance occorre:
- Scaricare un file modello OVA e importarlo nel server vCenter.
- Creare l'appliance e verificare che riesca a connettersi allo strumento Valutazione server di Azure Migrate.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate.

## <a name="download-the-ova-template"></a>Scaricare il modello OVA

1. In **obiettivi di migrazione** > **Server** > **Azure migrate: server Assessment**, fare clic su **individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** fare clic su **Sì, con VMware vSphere Hypervisor**.
3. Fare clic su **Scarica** per scaricare il file di modello OVA.



### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file OVA, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Per la versione più recente del dispositivo, l'hash generato deve corrispondere a queste impostazioni.

  **Algoritmo** | **Valore hash**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


## <a name="create-the-appliance-vm"></a>Creare l'appliance VM

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

Assicurarsi che l'appliance VM sia in grado di connettersi agli [URL di Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

1. Nella console di vSphere Client fare clic con il pulsante destro del mouse su VM > **Open Console** (Apri console).
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in qualsiasi computer in grado di connettersi alla macchina virtuale e aprire l'URL dell'app Web dell'appliance: ***nome dell'appliance https://o indirizzo IP*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance facendo clic sul relativo collegamento.
4. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **Licenza**: accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - **Connettività**: l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
        - Fare clic su **Proxy settings** (Impostazioni proxy) e specificare l'indirizzo e la porta di ascolto del proxy in formato http://ProxyIPAddress o http://ProxyFQDN.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
    - **Sincronizzazione dell'ora**: viene verificata l'ora. Il tempo necessario per l'individuazione del dispositivo dovrebbe essere sincronizzato con il tempo Internet per il corretto funzionamento dell'individuazione.
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
2. In **Nome utente** e **Password** specificare le credenziali dell'account di sola lettura che verranno usate dall'appliance per individuare le macchine virtuali nel server vCenter. Verificare che l'account disponga delle [autorizzazioni necessarie per l'individuazione](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). È possibile definire l'ambito dell'individuazione limitando l'accesso all'account vCenter di conseguenza; Altre informazioni sull'individuazione dell'ambito sono disponibili [qui](tutorial-assess-vmware.md#scoping-discovery).
3. Fare clic su **Convalida connessione** per verificare che l'appliance sia in grado di connettersi al server vCenter.

### <a name="specify-vm-credentials"></a>Specificare le credenziali della VM
Per individuare le applicazioni, i ruoli, le funzionalità e visualizzare le dipendenze delle macchine virtuali, è possibile specificare le credenziali della VM che hanno accesso alle macchine virtuali VMware. È possibile aggiungere credenziali per le macchine virtuali Windows e per le macchine virtuali Linux. [Altre informazioni](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) sui privilegi di accesso necessari.

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
