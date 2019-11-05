---
title: Configurare un'appliance per la valutazione dei server fisici con Azure Migrate Assessment server
description: Viene descritto come configurare un'appliance per la valutazione dei server fisici utilizzando Azure Migrate server assessment.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/21/2019
ms.author: raynew
ms.openlocfilehash: ddd659e8cbcb54a36868848d0c6327f294d531b1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512626"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurare un'appliance per i server fisici

Questo articolo descrive come configurare l'appliance Azure Migrate se si sta valutando server fisici con lo strumento di valutazione Azure Migrate: Server.

Il Azure Migrate Appliance è un'appliance semplice, usata da Azure Migrate Assessment server per eseguire le operazioni seguenti:

- Individuare i server locali.
- Inviare i metadati e i dati sulle prestazioni per i server individuati a Azure Migrate server assessment.

[Altre](migrate-appliance.md) informazioni sull'appliance Azure migrate.


## <a name="appliance-deployment-steps"></a>Passaggi di distribuzione dell'appliance

Per configurare l'appliance occorre:
- Scaricare un file compresso con Azure Migrate script del programma di installazione dall'portale di Azure.
- Estrarre il contenuto dal file compresso. Avviare la console di PowerShell con privilegi amministrativi.
- Eseguire lo script di PowerShell per avviare l'applicazione Web Appliance.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate.

## <a name="download-the-installer-script"></a>Scaricare lo script del programma di installazione

Scaricare il file compresso per l'appliance.

1. In **obiettivi di migrazione** > **Server** > **Azure migrate: server Assessment**, fare clic su **individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3. Fare clic su **download** per scaricare il file compresso.

    ![Scaricare la VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare il codice hash per il disco rigido virtuale
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Per la versione appliance 1.19.05.10, l'hash generato deve corrispondere a queste impostazioni.

  **Algoritmo** | **Valore hash**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="run-the-azure-migrate-installer-script"></a>Eseguire lo script del programma di installazione Azure Migrate
= Lo script del programma di installazione esegue le operazioni seguenti:

- Installa gli agenti e un'applicazione Web per l'individuazione e la valutazione dei server fisici.
- Installare i ruoli di Windows, tra cui il servizio Attivazione Windows, IIS e PowerShell ISE.
- Scaricare e installare un modulo riscrivibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435).
- Aggiorna una chiave del registro di sistema (HKLM) con i dettagli delle impostazioni permanenti per Azure Migrate.
- Crea i file seguenti nel percorso:
    - **File di configurazione**:%ProgramData%\Microsoft Azure\Config
    - **File di log**:%ProgramData%\Microsoft Azure\Logs

Eseguire lo script nel modo seguente:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance.
2. Avviare PowerShell nel server precedente con privilegi amministrativi (elevati).
3. Modificare la directory di PowerShell nella cartella in cui è stato estratto il contenuto dal file compresso scaricato.
4. Eseguire lo script eseguendo il comando seguente:
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
Quando viene completata correttamente, lo script avvierà l'applicazione Web Appliance.



### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che la macchina virtuale dell'appliance possa connettersi agli [URL di Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)richiesti.

## <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

1. Aprire un browser in qualsiasi computer in grado di connettersi alla macchina virtuale e aprire l'URL dell'app Web dell'appliance: ***nome dell'appliance https://o indirizzo IP*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop facendo clic sul collegamento all'app.
2. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **Licenza**: accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - **Connettività**: l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
        - Fare clic su **Proxy settings** (Impostazioni proxy) e specificare l'indirizzo e la porta di ascolto del proxy in formato http://ProxyIPAddress o http://ProxyFQDN.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
    - **Sincronizzazione dell'ora**: viene verificata l'ora. Per il corretto funzionamento dell'individuazione di VM, l'ora dell'appliance deve essere sincronizzata con l'ora di Internet.
    - **Installare gli aggiornamenti**: Azure migrate server Assessment verifica che nel dispositivo siano installati gli aggiornamenti più recenti.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Fare clic su **Log in** (Accedi). Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
2. Nella nuova scheda accedere con le credenziali di Azure. 
    - Accedere con il proprio nome utente e la password.
    - L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web.
4. Selezionare la sottoscrizione in cui è stato creato il progetto di Azure Migrate. Quindi selezionare il progetto.
5. Specificare un nome per l'appliance. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
6. Fare clic su **Register**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

Connettersi dal dispositivo ai server fisici e avviare l'individuazione.

1. Fare clic su **Aggiungi credenziali** per specificare le credenziali dell'account che l'appliance utilizzerà per individuare i server.  
2. Specificare il **sistema operativo**, il nome descrittivo per le credenziali, il nome **utente** e la **password** e fare clic su **Aggiungi**.
È possibile aggiungere un set di credenziali per ogni server Windows e Linux.
4. Fare clic su **Aggiungi server**e specificare i dettagli del server-FQDN/indirizzo IP e nome descrittivo delle credenziali (una voce per riga) per connettersi al server.
3. Fare clic su **Convalida**. Dopo la convalida, viene visualizzato l'elenco dei server che è possibile individuare.
    - Se la convalida non riesce per un server, esaminare l'errore passando il puntatore del mouse sull'icona nella colonna **stato** . Risolvere i problemi e ripetere la convalida.
    - Per rimuovere un server, selezionare > **Elimina**.
4. Dopo la convalida fare clic su **Salva e avvia individuazione** per avviare il processo di individuazione.

Viene avviata l'individuazione. Per visualizzare i metadati delle VM individuate nel portale di Azure, sono necessari circa 15 minuti. 

## <a name="verify-servers-in-the-portal"></a>Verificare i server nel portale

Al termine dell'individuazione, è possibile verificare che i server vengano visualizzati nel portale.

1. Aprire il dashboard di Azure Migrate.
2. Nella pagina **Azure migrate-server** > **Azure migrate: server Assessment** fare clic sull'icona che Visualizza il numero di **server individuati**. 


## <a name="next-steps"></a>Passaggi successivi

Provare [la valutazione dei server fisici](tutorial-assess-physical.md) con Azure migrate Assessment server.
