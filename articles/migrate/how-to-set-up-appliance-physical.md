---
title: Configurare un'appliance Azure Migrate per i server fisici
description: Informazioni su come configurare un appliance Azure Migrate per la valutazione del server fisico.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 6d9cc071ad5d81a09a14b12fe2acdf564c2ea6c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331781"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurare un'appliance per i server fisici

Questo articolo descrive come configurare l'appliance Azure Migrate se si sta valutando server fisici con lo strumento di valutazione Azure Migrate: Server.

Il Azure Migrate Appliance è un'appliance semplice, usata da Azure Migrate Assessment server per eseguire le operazioni seguenti:

- Individuare i server locali.
- Inviare i metadati e i dati sulle prestazioni per i server individuati a Azure Migrate server assessment.

[Altre](migrate-appliance.md) informazioni sull'appliance Azure migrate.


## <a name="appliance-deployment-steps"></a>Passaggi di distribuzione dell'appliance

Per configurare l'appliance occorre:
- Scaricare un file compresso con lo script del programma di installazione di Azure Migrate dal portale di Azure.
- Estrarre il contenuto del file compresso. Avviare la console PowerShell con privilegi amministrativi.
- Eseguire lo script di PowerShell per avviare l'applicazione Web dell'appliance.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate.

## <a name="download-the-installer-script"></a>Scaricare lo script del programma di installazione

Scaricare il file compresso per l'appliance.

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** fare clic su **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3. Fare clic su **Scarica** per scaricare il file compresso.

    ![Scaricare la VM](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per il file compresso:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo per il cloud pubblico: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Esempio di utilizzo per il cloud per enti pubblici: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Verificare la versione più recente dell'appliance e i valori hash:
 
    - Per il cloud pubblico:

        **Scenario** | **Download*** | **Valore hash**
        --- | --- | ---
        Dispositivi fisici (63,1 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0

    - Per Azure per enti pubblici:

        **Scenario** | **Download*** | **Valore hash**
        --- | --- | ---
        Dispositivi fisici (63,1 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1


## <a name="run-the-azure-migrate-installer-script"></a>Eseguire lo script del programma di installazione di Azure Migrate
Lo script del programma di installazione esegue le operazioni seguenti:

- Installa gli agenti e un'applicazione Web per l'individuazione e la valutazione dei server fisici.
- Installa i ruoli di Windows, tra cui il Servizio Attivazione Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo di IIS riscrivibile. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM) con i dettagli delle impostazioni permanenti per Azure Migrate.
- Crea i seguenti file nei relativi percorsi:
    - **File di configurazione**: %Programdata%\Microsoft Azure\Config
    - **File di log**: %Programdata%\Microsoft Azure\Logs

Eseguire lo script nel modo seguente:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance.  Assicurarsi di non eseguire lo script in un computer in un'appliance di Azure Migrate esistente.
2. Avviare PowerShell nello stesso server con privilegi amministrativi (elevati).
3. Modificare la directory di PowerShell nella cartella in cui è stato estratto il contenuto del file compresso scaricato.
4. Eseguire lo script denominato **AzureMigrateInstaller.ps1** eseguendo il comando seguente:

    - Per il cloud pubblico: ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Per Azure per enti pubblici: ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Lo script avvierà l'applicazione Web dell'appliance al termine dell'operazione.

In caso di problemi, è possibile accedere ai log degli script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log per la risoluzione dei problemi.



### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che la macchina virtuale dell'appliance possa connettersi agli URL di Azure per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).

## <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

1. Aprire un browser in un computer in grado di connettersi alla VM, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop facendo clic sul relativo collegamento.
2. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **License** (Licenza): Accettare le condizioni di licenza e leggere le informazioni di terze parti.
    - **Connectivity** (Connettività): l'app verifica che la macchina virtuale abbia accesso a Internet. Se la VM usa un proxy:
        - Fare clic su **Proxy settings** (Impostazioni proxy) e specificare l'indirizzo e la porta di ascolto del proxy in formato http://ProxyIPAddress o http://ProxyFQDN.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
    - **Time sync** (Sincronizzazione ora): viene verificata l'ora. Per il corretto funzionamento dell'individuazione di VM, l'ora dell'appliance deve essere sincronizzata con l'ora di Internet.
    - **Install updates** (Installa aggiornamenti): Valutazione server di Azure Migrate verifica che nell'appliance siano installati gli aggiornamenti più recenti.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Fare clic su **Log in** (Accedi). Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
2. Nella nuova scheda accedere con le credenziali di Azure.
    - Accedere con il nome utente e la password.
    - L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web.
4. Selezionare la sottoscrizione in cui è stato creato il progetto di Azure Migrate. Quindi selezionare il progetto.
5. Specificare un nome per l'appliance. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
6. Fare clic su **Register**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

Connettersi dal dispositivo ai server fisici e avviare l'individuazione.

1. Fare clic su **Aggiungi credenziali** per specificare le credenziali dell'account che verranno usate dall'appliance per individuare i server.  
2. Specificare il **Sistema operativo**, un nome descrittivo per le credenziali e il nome utente e la password. Fare quindi clic su **Aggiungi**.
È possibile aggiungere un set di credenziali per ogni server Windows e Linux.
4. Fare clic su **Aggiungi server** e specificarne i dettagli, FQDN/indirizzo IP e nome descrittivo delle credenziali (una voce per riga), per eseguire la connessione.
3. Fare clic su **Convalida**. Dopo la convalida, viene visualizzato l'elenco dei server che è possibile individuare.
    - Se la convalida non riesce per un server, esaminare l'errore passando il puntatore del mouse sull'icona nella colonna **Stato**. Risolvere i problemi e ripetere la convalida.
    - Per rimuovere un server, selezionare > **Elimina**.
4. Dopo la convalida fare clic su **Salva e avvia individuazione** per avviare il processo di individuazione.

Viene avviata l'individuazione. Per visualizzare i metadati delle VM individuate nel portale di Azure, sono necessari circa 15 minuti.

## <a name="verify-servers-in-the-portal"></a>Verificare i server nel portale

Al termine dell'individuazione, è possibile verificare che i server vengano visualizzati nel portale.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** fare clic sull'icona che mostra il numero di **Server individuati**.


## <a name="next-steps"></a>Passaggi successivi

Provare [la valutazione dei server fisici](tutorial-assess-physical.md) con Azure migrate Assessment server.
