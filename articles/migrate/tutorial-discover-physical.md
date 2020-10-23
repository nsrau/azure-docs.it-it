---
title: Individuare i server fisici con Valutazione server di Azure Migrate
description: Informazioni su come individuare i server fisici locali con Valutazione server di Azure Migrate.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: e7cbd7939248686a251fdf56bf1a5f1acc952a3a
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314074"
---
# <a name="tutorial-discover-physical-servers-with-server-assessment"></a>Esercitazione: Individuare i server fisici con Valutazione server

Il percorso di migrazione ad Azure prevede l'individuazione dei server per la valutazione e la migrazione.

Questa esercitazione mostra come individuare server fisici locali con lo strumento Azure Migrate: Valutazione server, usando un'appliance leggera di Azure Migrate. L'appliance viene distribuita come server fisico, per individuare in modo continuativo i metadati relativi alle macchine virtuali e alle prestazioni.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un account Azure.
> * Preparare i server fisici per l'individuazione.
> * Creare un progetto di Azure Migrate.
> * Configurare l'appliance di Azure Migrate.
> * Avviare l'individuazione continua.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e prevedono l'uso delle opzioni predefinite.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, verificare che siano rispettati i prerequisiti seguenti.

**Requisito** | **Dettagli**
--- | ---
**Appliance** | È necessario un computer in cui eseguire l'appliance di Azure Migrate. Il computer deve soddisfare i requisiti seguenti:<br/><br/> - Windows Server 2016 installato. _Attualmente la distribuzione di appliance è supportata solo in Windows Server 2016._<br/><br/> - 16-GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco<br/><br/> - Un indirizzo IP statico o dinamico con accesso a Internet, direttamente o tramite un proxy.
**Server Windows** | Consentire le connessioni in ingresso sulla porta WinRM 5985 (HTTP), in modo che l'appliance possa recuperare i metadati relativi a configurazione e prestazioni.
**Server Linux** | Consentire le connessioni in ingresso sulla porta 22 (TCP).

## <a name="prepare-an-azure-user-account"></a>Preparare un account utente Azure

Per creare un progetto di Azure Migrate e registrare l'appliance di Azure Migrate, è necessario un account con:
- Autorizzazioni di collaboratore o proprietario per una sottoscrizione di Azure.
- Autorizzazioni per la registrazione di app Azure Active Directory.

Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione. Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare le autorizzazioni nel modo seguente:

1. Nel portale di Azure cercare "sottoscrizioni" e in **Servizi** selezionare **Sottoscrizioni**.

    ![Casella di ricerca per cercare la sottoscrizione di Azure](./media/tutorial-discover-physical/search-subscription.png)

2. Nella pagina **Sottoscrizioni** selezionare la sottoscrizione in cui creare un progetto di Azure Migrate. 
3. Nella sottoscrizione selezionare **Controllo di accesso (IAM)**  > **Verifica l'accesso**.
4. In **Verifica l'accesso** cercare l'account utente pertinente.
5. In **Aggiungi un'assegnazione di ruolo** fare clic su **Aggiungi**.

    ![Cercare un account utente per verificare l'accesso e assegnare un ruolo](./media/tutorial-discover-physical/azure-account-access.png)

6. In **Aggiungi un'assegnazione di ruolo** selezionare il ruolo Collaboratore o Proprietario e selezionare l'account (azmigrateuser nell'esempio). Fare quindi clic su **Salva**.

    ![Viene aperta la pagina Aggiungi assegnazione di ruolo per assegnare un ruolo all'account](./media/tutorial-discover-physical/assign-role.png)

7. Nel portale cercare gli utenti e in **Servizi** selezionare **Utenti**.
8. In **Impostazioni utente** verificare che gli utenti di Azure AD possano registrare le applicazione (impostato su **Sì** per impostazione predefinita).

    ![Verificare che in Impostazioni utente che gli utenti possano registrare le app Active Directory](./media/tutorial-discover-physical/register-apps.png)

9. In alternativa, l'amministratore tenant/globale può assegnare il ruolo **Sviluppatore applicazione** a un account per consentire la registrazione di app AAD. [Altre informazioni](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-physical-servers"></a>Preparare i server fisici

Configurare un account utilizzabile dall'appliance per accedere ai server fisici.

- Per i server Windows configurare un account utente locale in tutti i server Windows da includere nell'individuazione. Aggiungere l'account utente ai gruppi seguenti: - Remote Management Users - Performance Monitor Users - Performance Log Users.
- Per i server Linux, è necessario un account radice nei server Linux che si desidera individuare. In alternativa, impostare l'accesso nel modo seguente:
    - setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk
    - setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk (se /usr/sbin/fdisk non è presente)<br/> - setcap "cap_dac_override, cap_dac_read_search, cap_fowner,cap_fsetid, cap_setuid, cap_setpcap, cap_net_bind_service, cap_net_admin, cap_sys_chroot, cap_sys_admin, cap_sys_resource, cap_audit_control, cap_setfcap=+eip" /sbin/lvm
    - setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode chmod a+r /sys/class/dmi/id/product_uuid


## <a name="set-up-a-project"></a>Configurare un progetto

Configurare un nuovo progetto di Azure Migrate.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Panoramica** selezionare **Crea progetto**.
5. In **Crea progetto** selezionare il gruppo di risorse e la sottoscrizione di Azure. Creare un gruppo di risorse, se non è presente.
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caselle per il nome del progetto e l'area](./media/tutorial-discover-physical/new-project.png)

7. Selezionare **Create** (Crea).
8. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate.

Lo strumento **Azure Migrate: Valutazione server** viene aggiunto per impostazione predefinita al nuovo progetto.

![Pagina che mostra lo strumento Valutazione server aggiunto per impostazione predefinita](./media/tutorial-discover-physical/added-tool.png)


## <a name="set-up-the-appliance"></a>Configurare l'appliance

Per configurare l'appliance occorre:
- Specificare un nome di appliance e generare una chiave del progetto di Azure Migrate nel portale.
- Scaricare un file compresso con lo script del programma di installazione di Azure Migrate dal portale di Azure.
- Estrarre il contenuto del file compresso. Avviare la console PowerShell con privilegi amministrativi.
- Eseguire lo script di PowerShell per avviare l'applicazione Web dell'appliance.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate con la chiave del progetto di Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Generare la chiave del progetto Azure Migrate

1. In **Obiettivi della migrazione** > **Server** > **Azure Migrate: Valutazione server** selezionare **Individua**.
2. In **Individua macchine virtuali** > **I computer sono virtualizzati?** selezionare **Fisico o di altro tipo (AWS, GCP, Xen e così via)** .
3. In **1: Generare la chiave del progetto Azure Migrate** specificare un nome per l'appliance Azure Migrate che verrà configurata per l'individuazione dei server fisici o virtuali. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Individua macchine virtuali durante la creazione delle risorse.
1. Al termine della creazione delle risorse di Azure, viene generata una **Chiave progetto Azure Migrate**.
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="download-the-installer-script"></a>Scaricare lo script del programma di installazione

In **2: Scaricare l'appliance di Azure Migrate** fare clic su **Scarica**.


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per il file compresso:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo per il cloud pubblico: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Esempio di utilizzo per il cloud per enti pubblici: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Verificare le versioni più recenti dell'appliance e i valori hash:
    - Per il cloud pubblico:

        **Scenario** | **Download*** | **Valore hash**
        --- | --- | ---
        Server fisico (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140334) | ce5e6f0507936def8020eb7b3109173dad60fc51dd39c3bd23099bc9baaabe29

    - Per Azure per enti pubblici:

        **Scenario** | **Download*** | **Valore hash**
        --- | --- | ---
        Server fisico (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140338) | ae132ebc574caf231bf41886891040ffa7abbe150c8b50436818b69e58622276
 

### <a name="run-the-azure-migrate-installer-script"></a>Eseguire lo script del programma di installazione di Azure Migrate
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
2. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **Connectivity** (Connettività): l'app verifica che il server abbia accesso a Internet. Se il server usa un proxy:
        - Fare clic su **Set up proxy** (Configura proxy) e specificare l'indirizzo proxy (nel formato http://ProxyIPAddress o http://ProxyFQDN) e la porta di ascolto.
        - Se il proxy richiede l'autenticazione, specificare le credenziali.
        - È supportato solo il proxy HTTP.
        - Se sono stati aggiunti dettagli del proxy o sono stati disabilitati il proxy e/o l'autenticazione, fare clic su **Save** (Salva) per attivare di nuovo il controllo della connettività.
    - **Time sync** (Sincronizzazione ora): viene verificata l'ora. Per il corretto funzionamento dell'individuazione di server, l'ora dell'appliance deve essere sincronizzata con l'ora di Internet.
    - **Install updates** (Installa aggiornamenti): Valutazione server di Azure Migrate verifica che nell'appliance siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **Visualizza servizi dell'appliance** per visualizzare lo stato e le versioni dei componenti in esecuzione nell'appliance.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave del progetto Azure Migrate** copiata dal portale. Se non si dispone della chiave, passare a **Valutazione server > Individua > Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Fare clic su **Accedi**. Verrà aperto un prompt di accesso di Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella nuova scheda accedere con nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Dopo aver eseguito l'accesso, tornare all'app Web. 
4. Se l'account utente di Azure usato per la registrazione ha le [autorizzazioni]() corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Al termine della registrazione dell'appliance è possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

A questo punto, connettersi dall'appliance ai server fisici da individuare e avviare l'individuazione.

1. In **Passaggio 1: Specificare le credenziali per l'individuazione di server fisici o virtuali Windows e Linux**, fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **Nome utente** e **Password** per un server Windows o Linux. Fare clic su **Save**.
1. Se si vogliono aggiungere più credenziali contemporaneamente, fare clic su **Aggiungi altre** per salvare e aggiungere altre credenziali. Sono supportate più credenziali per l'individuazione di server fisici.
1. In **Passaggio 2: Specificare i dettagli del server fisico o virtuale** fare clic su **Aggiungi origine di individuazione** per specificare **Indirizzo IP/FQDN** del server e il nome descrittivo per le credenziali per la connessione al server.
1. È possibile selezionare **Add single item** (Aggiungi singolo elemento) o **Add multiple items** (Aggiungi più elementi). È disponibile anche un'opzione per fornire i dettagli del server tramite **Importa CSV**.


    - Se si sceglie **Add single item** (Aggiungi singolo elemento) è possibile scegliere il tipo di sistema operativo, specificare un nome descrittivo per le credenziali, aggiungere **Indirizzo IP/FQDN** del server e fare clic su **Salva**.
    - Se si sceglie **Add multiple items** (Aggiungi più elementi), è possibile aggiungere più record contemporaneamente specificando **Indirizzo IP/FQDN** del server con il nome descrittivo per le credenziali nella casella di testo. Fare clic su **Verifica** per controllare i record aggiunti e fare clic su **Salva**.
    - Se si sceglie **Importa CSV** _(selezionato per impostazione predefinita)_ , è possibile scaricare un file modello CSV, quindi compilare il file con **Indirizzo IP/FQDN** del server e il nome descrittivo per le credenziali. Importare quindi il file nell'appliance, **verificare** i record nel file e fare clic su **Salva**.

1. Quando si fa clic su Salva, l'appliance tenterà di convalidare la connessione ai server aggiunti e visualizzerà lo **Stato di convalida** nella tabella per ogni server.
    - Se la convalida non riesce per un server, esaminare l'errore facendo clic su **Convalida non riuscita** nella colonna Stato della tabella. Risolvere il problema e ripetere la convalida.
    - Per rimuovere un server, fare clic su **Elimina**.
1. È possibile **riconvalidare** la connettività ai server in qualsiasi momento prima di avviare l'individuazione.
1. Fare clic su **Avvia individuazione** per avviare l'individuazione dei server convalidati correttamente. Dopo l'avvio dell'individuazione, è possibile controllare lo stato dell'individuazione per ogni server nella tabella.


Viene avviata l'individuazione. Per la visualizzazione dei metadati del server individuato nel portale di Azure sono necessari circa 2 minuti per ogni server.

## <a name="verify-servers-in-the-portal"></a>Verificare i server nel portale

Dopo l'individuazione, è possibile verificare se i server vengono visualizzati nel portale.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure Migrate - Server** > **Azure Migrate: Valutazione server** fare clic sull'icona che mostra il numero di **Server individuati**.
## <a name="next-steps"></a>Passaggi successivi

- [Valutare i server fisici](tutorial-assess-physical.md) per la migrazione alle macchine virtuali di Azure.
- [Esaminare i dati](migrate-appliance.md#collected-data---physical) raccolti dall'appliance durante l'individuazione.