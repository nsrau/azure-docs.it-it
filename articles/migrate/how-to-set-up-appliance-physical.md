---
title: Configurare un'appliance Azure Migrate per i server fisici
description: Informazioni su come configurare un appliance Azure Migrate per la valutazione del server fisico.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 1b4e875a81c92f74cd7d2db96cf1c313157297eb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923559"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurare un'appliance per i server fisici

Questo articolo descrive come configurare l'appliance Azure Migrate se si sta valutando server fisici con lo strumento di valutazione Azure Migrate: Server.

Il Azure Migrate Appliance è un'appliance semplice, usata da Azure Migrate Assessment server per eseguire le operazioni seguenti:

- Individuare i server locali.
- Inviare i metadati e i dati sulle prestazioni per i server individuati a Azure Migrate server assessment.

[Altre](migrate-appliance.md) informazioni sull'appliance Azure migrate.


## <a name="appliance-deployment-steps"></a>Passaggi di distribuzione dell'appliance

Per configurare l'appliance occorre:
- Specificare un nome per il dispositivo e generare una chiave del progetto Azure Migrate nel portale.
- Scaricare un file compresso con lo script del programma di installazione di Azure Migrate dal portale di Azure.
- Estrarre il contenuto del file compresso. Avviare la console PowerShell con privilegi amministrativi.
- Eseguire lo script di PowerShell per avviare l'applicazione Web dell'appliance.
- Configurare il dispositivo per la prima volta e registrarlo con il progetto Azure Migrate usando la chiave del progetto Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Genera la chiave del progetto Azure Migrate

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
2. In **individua macchine**  >  **virtuali i computer sono virtualizzati?** selezionare **fisico o altro (AWS, GCP, Xen e così via)**.
3. In **1: generare Azure migrate chiave del progetto**, specificare un nome per il dispositivo Azure migrate da configurare per l'individuazione di server fisici o virtuali. Il nome deve essere alfanumerico con un massimo di 14 caratteri.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Discover machines durante la creazione delle risorse.
1. Una volta completata la creazione delle risorse di Azure, viene generata una **chiave del progetto Azure migrate** .
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la relativa configurazione.

### <a name="download-the-installer-script"></a>Scaricare lo script del programma di installazione

In **2: scaricare Azure migrate Appliance**, fare clic su **download**.

   ![Selezioni per le macchine virtuali di individuazione](./media/tutorial-assess-physical/servers-discover.png)


   ![Selezioni per genera chiave](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per il file compresso:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo per il cloud pubblico: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Esempio di utilizzo per il cloud per enti pubblici: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Verificare la versione più recente dell'appliance e [le impostazioni](./tutorial-assess-physical.md#verify-security)dei valori hash.
 

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

    - Per il cloud pubblico: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Per Azure per enti pubblici: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Lo script avvierà l'applicazione Web dell'appliance al termine dell'operazione.

In caso di problemi, è possibile accedere ai log degli script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log per la risoluzione dei problemi.



### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che la macchina virtuale dell'appliance possa connettersi agli URL di Azure per i cloud [pubblico](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

1. Aprire un browser in un computer in grado di connettersi all'appliance, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop facendo clic sul relativo collegamento.
2. Accettare le **condizioni di licenza**e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **Connectivity** (Connettività): l'app verifica che il server abbia accesso a Internet. Se il server usa un proxy:
        - Fare clic su **Configura proxy** per e specificare l'indirizzo del proxy, nel formato http://ProxyIPAddress o nella http://ProxyFQDN) porta di ascolto.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
        - Se sono stati aggiunti dettagli del proxy o è stato disabilitato il proxy e/o l'autenticazione, fare clic su **Salva** per attivare nuovamente il controllo della connettività.
    - **Time sync** (Sincronizzazione ora): viene verificata l'ora. Per il corretto funzionamento dell'individuazione di server, l'ora dell'appliance deve essere sincronizzata con l'ora di Internet.
    - **Installare gli aggiornamenti**: Azure migrate server Assessment verifica che nel dispositivo siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **Visualizza servizi Appliance** per visualizzare lo stato e le versioni dei componenti in esecuzione nel dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave di progetto Azure migrate** copiata dal portale. Se non si dispone della chiave, passare a **Server Assessment> individua> Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Fare clic su **Accedi**. Verrà aperto un prompt di accesso di Azure in una nuova scheda del browser. Se non viene visualizzato, assicurarsi di aver disattivato il blocco popup nel browser.
1. Nella nuova scheda accedere con nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web. 
4. Se l'account utente di Azure usato per la registrazione dispone delle [autorizzazioni](tutorial-prepare-physical.md) corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Dopo aver registrato correttamente il dispositivo, è possibile visualizzare i dettagli di registrazione facendo clic su **Visualizza dettagli**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

A questo punto, connettersi dall'appliance ai server fisici da individuare e avviare l'individuazione.

1. In **passaggio 1: fornire le credenziali per l'individuazione di server fisici o virtuali Windows e Linux**, fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **nome utente** e **password** per un server Windows o Linux. Fare clic su **Save**.
1. Se si desidera aggiungere più credenziali contemporaneamente, fare clic su **Aggiungi altro** per salvare e aggiungere altre credenziali. Sono supportate più credenziali per l'individuazione di server fisici.
1. In **passaggio 2: specificare i dettagli del server fisico o virtuale**, fare clic su **Aggiungi origine individuazione** per specificare l' **indirizzo IP del server/FQDN** e il nome descrittivo per le credenziali per la connessione al server.
1. È possibile **aggiungere un singolo elemento** alla volta o **aggiungere più elementi** in un'unica posizione. È disponibile anche un'opzione per fornire i dettagli del server tramite l' **importazione di CSV**.

    ![Selezioni per l'aggiunta dell'origine di individuazione](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Se si sceglie **Aggiungi singolo elemento**, è possibile scegliere il tipo di sistema operativo, specificare il nome descrittivo per le credenziali, aggiungere l' **indirizzo IP del server/FQDN** e fare clic su **Salva**.
    - Se si sceglie **Aggiungi più elementi**, è possibile aggiungere più record contemporaneamente, specificando l' **indirizzo IP del server/FQDN** con il nome descrittivo per le credenziali nella casella di testo. **Verificare** i record aggiunti e fare clic su **Salva**.
    - Se si sceglie **Importa CSV** _(selezionato per impostazione predefinita)_, è possibile scaricare un file di modello CSV, compilare il file con l' **indirizzo IP del server/FQDN** e il nome descrittivo per le credenziali. Si importa quindi il file nell'appliance, si **verificano** i record nel file e si fa clic su **Save (Salva**).

1. Quando si fa clic su Salva, il dispositivo tenterà di convalidare la connessione ai server aggiunti e visualizzerà lo **stato di convalida** nella tabella rispetto a ogni server.
    - Se la convalida non riesce per un server, esaminare l'errore facendo clic su **convalida non riuscita** nella colonna stato della tabella. Risolvere il problema e convalidare di nuovo.
    - Per rimuovere un server, fare clic su **Elimina**.
1. È possibile **rivalidare** la connettività ai server in qualsiasi momento prima di avviare l'individuazione.
1. Fare clic su **Avvia individuazione per avviare**l'individuazione dei server convalidati correttamente. Dopo che l'individuazione è stata avviata correttamente, è possibile controllare lo stato di individuazione per ogni server della tabella.


Viene avviata l'individuazione. Sono necessari circa 2 minuti per ogni server per visualizzare i metadati del server individuato nella portale di Azure.

## <a name="verify-servers-in-the-portal"></a>Verificare i server nel portale

Al termine dell'individuazione, è possibile verificare che i server vengano visualizzati nel portale.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** fare clic sull'icona che mostra il numero di **Server individuati**.


## <a name="next-steps"></a>Passaggi successivi

Provare [la valutazione dei server fisici](tutorial-assess-physical.md) con Azure migrate Assessment server.
