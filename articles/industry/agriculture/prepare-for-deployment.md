---
title: Distribuire Azure FarmBeats
description: Viene descritto come distribuire FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c609285b727414b4849c9ef6654406a035005bb1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797725"
---
# <a name="deploy-farmbeats"></a>Distribuire FarmBeats

Questo articolo descrive come configurare Azure FarmBeats.

Azure FarmBeats è una soluzione estendibile e specifica del settore per la produzione basata sui dati che consente il provisioning e la connettività dei dispositivi sensori al cloud di Azure, alla raccolta di dati di telemetria e alle aggregazioni. Azure FarmBeats offre diversi sensori, ad esempio fotocamere, drone, sensori di suolo e gestione dei dispositivi dal cloud, che include l'infrastruttura e i servizi in Azure per i dispositivi pronti per l'uso (Internet delle cose) a un'app Web e per dispositivi mobili estendibile da fornire visualizzazione, avvisi e informazioni dettagliate.

> [!NOTE]
> Azure FarmBeats è supportato solo in ambienti di cloud pubblico. Per ulteriori informazioni sull'ambiente cloud, vedere [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Azure FarmBeats include i due componenti seguenti:

- **Hub dati** : Data Hub è il livello piattaforma di Azure FarmBeats che consente di compilare, archiviare ed elaborare dati e di ottenere informazioni dettagliate da pipeline di dati nuove o esistenti. Questo livello piattaforma è utile per l'esecuzione e la compilazione di pipeline di dati e modelli di agricoltura.

- **Accelerator** -Accelerator è il livello della soluzione di Azure FarmBeats che offre un'applicazione incorporata per illustrare le funzionalità di Azure FarmBeats usando i modelli di agricoltura creati in precedenza. Questo livello di soluzione consente di creare limiti della farm e di ottenere informazioni dettagliate dai dati agricoli all'interno del contesto del limite della farm.

Una distribuzione rapida di Azure FarmBeats dovrebbe richiedere meno di un'ora. I costi per l'hub dati e l'acceleratore variano in base all'utilizzo.

## <a name="deployed-resources"></a>Risorse distribuite

La distribuzione di Azure FarmBeats crea le risorse elencate di seguito nella sottoscrizione:

|Numero S.  |Nome risorsa  |Componente Azure FarmBeats  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Hub dati       |
|2  |    Application Insights      |     Hub dati/acceleratore     |
|3  |Cache Redis di Azure   |Hub dati   |
|4  |       Azure KeyVault    |  Hub dati/acceleratore        |
|5  |    Time Series Insights       |     Hub dati      |
|6 |      Spazio dei nomi EventHub    |  Hub dati       |
|7  |    Azure Data Factory V2       |     Hub dati/acceleratore      |
|8  |Account Batch    |Hub dati   |
|9  |       Account di archiviazione     |  Hub dati/acceleratore        |
|10  |    App per la logica        |     Hub dati      |
|11  |    Connessione API        |     Hub dati      |
|12|      Servizio app      |  Hub dati/acceleratore       |
|13 |    Piano di servizio app        |     Hub dati/acceleratore      |
|14 |Account Azure Maps     |Acceleratore    |
|15 |       Time Series Insights      |  Hub dati     |

Azure FarmBeats è disponibile per il download da Azure Marketplace. È possibile accedervi direttamente dalla portale di Azure.  

## <a name="prepare"></a>Preparazione

Per la distribuzione di Azure FarmBeats sono necessarie le autorizzazioni seguenti:

- Tenant: accesso in lettura
- Sottoscrizione: collaboratore al proprietario
- Gruppo di risorse: proprietario

## <a name="before-you-begin"></a>Prima di iniziare

Prima di avviare la distribuzione, assicurarsi di disporre di quanto segue:

- Account Sentinel
- Azure Active Directory (registrazione app)
- Azure FarmBeats

## <a name="create-a-sentinel-account"></a>Creare un account Sentinel    

Un account con Sentinel consente di scaricare le immagini satellite Sentinel dal sito web ufficiale al dispositivo. Per creare un account gratuito, seguire questa procedura:

1. Passare a https://scihub.copernicus.eu/dhus/#/self-registration. Nella pagina di registrazione specificare un nome, un cognome, un nome utente, una password e un indirizzo di posta elettronica.
2. Nel menu a discesa **Seleziona dominio** selezionare l'opzione **terra**.
3. Nel menu a discesa **Seleziona utilizzo** selezionare l'opzione **istruzione**.
4. Nel menu a discesa **Seleziona paese** selezionare il paese.
5. Selezionare **Register (registra** ) per completare il processo di registrazione.

Un messaggio di posta elettronica di verifica verrà inviato all'indirizzo di posta elettronica registrato per la conferma. Selezionare il collegamento e confermare. Il processo di registrazione è stato completato.

## <a name="create-azure-ad-app-registration"></a>Creazione della registrazione dell'app Azure AD

Per l'autenticazione e l'autorizzazione in Azure FarmBeats, è necessario avere una registrazione dell'applicazione Azure Active Directory che:

- Caso 1: il programma di installazione può creare automaticamente (purché si disponga delle autorizzazioni di accesso necessarie per tenant, sottoscrizione e gruppo di risorse).
- Caso 2: è possibile creare e configurare prima di distribuire Azure FarmBeats (richiede passaggi manuali).

**Caso 1**: il programma di installazione presuppone che si disponga dei diritti per creare una registrazione dell'applicazione Azure Active Directory nella sottoscrizione desiderata. Per eseguire la registrazione, accedere al portale, passare ad **Azure Active directory** > **registrazione dell'app** > **nuova registrazione**.

Se si dispone già di una sottoscrizione, è possibile spostarla direttamente alla procedura successiva.

**Caso 2**: questo metodo è il passaggio preferito quando non si dispone di diritti sufficienti per creare e configurare un Azure ad la registrazione dell'app all'interno della sottoscrizione. Richiedere all'amministratore di usare lo [script personalizzato](https://aka.ms/FarmBeatsAADScript), che consentirà all'amministratore IT di generare e configurare automaticamente la registrazione dell'app Azure AD nel portale di Azure. Come output per l'esecuzione di questo script personalizzato con l'ambiente PowerShell, l'amministratore IT deve condividere un ID client dell'applicazione Azure Active Directory e il segreto della password. Prendere nota dei valori seguenti.

Per eseguire lo script di registrazione dell'applicazione Azure AD, attenersi alla procedura seguente:

1. Ottenere lo [script](https://aka.ms/FarmBeatsAADScript)di registrazione.
2. Accedere a portale di Azure e selezionare la sottoscrizione e il tenant di AD.
3. Avviare Cloud Shell dal riquadro di spostamento in alto nel portale di Azure.

    ![Beat Farm progetto](./media/prepare-for-deployment/navigation-bar-1.png)


4. Per la prima volta agli utenti verrà richiesto di selezionare una sottoscrizione per creare un account di archiviazione e Microsoft Azure condivisione file. Selezionare **Create storage** (Crea risorsa di archiviazione).
5. Per la prima volta verrà richiesto agli utenti di scegliere l'esperienza shell preferita: bash o PowerShell. Scegliere PowerShell.
6. In Cloud Shell immettere i comandi seguenti per eseguire lo script.

    ```powershell
    ./create_aad_script.ps1
    ```
7. Prendere nota dell'ID applicazione Azure AD e del segreto client da condividere con la persona che distribuisce Azure FarmBeats.

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Crea un'offerta Azure FarmBeats nel Marketplace

Seguire questa procedura per creare un'offerta Azure FarmBeats nel Marketplace:

1. Accedere al **portale di Azure** e selezionare l'account nell'angolo in alto a destra e passare al tenant di Azure ad in cui si vuole distribuire Microsoft Azure FarmBeats.
2. Selezionare **Cerca/Marketplace** o **crea risorse**. Digitare **FarmBeats** per visualizzare i dettagli dell'offerta. Scaricare le guide disponibili tramite i collegamenti ipertestuali aka.ms in questa pagina prima di procedere con i passaggi successivi.
3. Selezionare **Crea** e immettere le informazioni seguenti:

   - Immettere il nome della sottoscrizione.
   - Immettere un nome del gruppo di risorse esistente (solo gruppo di risorse vuoto) o creare un nuovo gruppo di risorse per la distribuzione di Azure FarmBeats. Prendere nota del gruppo di risorse da immettere nel file input. JSON in una fase successiva.
   - Immettere l'area in cui si vuole installare Azure FarmBeats. Attualmente è possibile installare FarmBeats in queste aree: Stati Uniti centrali, Europa occidentale, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti occidentali, Asia sudorientale, Stati Uniti orientali, Australia orientale, Stati Uniti occidentali 2.
4. Selezionare **OK**per reindirizzare l'utente alla pagina condizioni per l'utilizzo. Esaminare le condizioni standard del Marketplace oppure selezionare il collegamento ipertestuale per esaminare le condizioni per l'utilizzo.
5. Selezionare **Chiudi**, quindi la casella di controllo "Accetto" e quindi selezionare **Crea**.
6. Il contratto di licenza con l'utente finale (EULA) di Azure FarmBeats è stato firmato sul Marketplace. Per continuare con la distribuzione, seguire i passaggi successivi di questa guida.

### <a name="prepare-inputjson-file"></a>Preparare il file input. JSON

Questo file è il file di input per Azure Cloud Shell e i parametri i cui valori sono specificati all'interno di questo file prima che il caricamento non venga richiesto durante la distribuzione, risparmiando quindi tempo.  

> [!NOTE]
> Questo file immette i valori da Azure Cloud Shell.  Per risparmiare tempo, durante la distribuzione non verranno richiesti i parametri aggiunti a questo file. Verranno richiesti parametri mancanti.

Verificare i parametri prima di preparare il file.

|Comando | Descrizione|
|--- | ---|
|SKU  | Offre la possibilità di scaricare uno o entrambi i componenti di Azure FarmBeats. Specifica i componenti da scaricare. Per installare solo l'hub dati, usare "onlydatabhub". Per installare hub dati e acceleratore, usare "both".|
|SubscriptionId  | Specifica la sottoscrizione per l'installazione di FarmBeats|
|"datahubResourceGroup"  | Nome del gruppo di risorse per le risorse dell'hub dati.|
|"datahubLocation" | Percorso in cui si vogliono archiviare le risorse dell'hub dati.|
|"acceleratorWebsiteName"  |Prefisso URL univoco per assegnare un nome all'hub dati
Sito Web di spavalderia. Il valore predefinito è il nome del gruppo di risorse dell'hub dati. Premere INVIO per continuare con il valore predefinito.|
|"acceleratorResourceGroup"  | Nome del gruppo di risorse per le risorse dell'hub dati. |
|"acceleratorLocation"  | Percorso per l'archiviazione delle risorse dell'hub dati
"acceleratorWebsiteName"  | Prefisso URL univoco per assegnare un nome al sito Web Accelerator. Il valore predefinito è Accelerator. Premere INVIO per continuare con il valore predefinito.|
|''sentinelUsername'' | nome utente a cui accedere: https://scihub.copernicus.eu/dhus/#/self-registration.|
|"sentinelPassword"  | Password per l'accesso: https://scihub.copernicus.eu/dhus/#/self-registration.|
|"farmbeatsAppId"  | Valori da condividere con il team di Azure FarmBeats.  |
|"farmbeatsPassword"  | Valori da condividere con il team di Azure FarmBeats.|
|"notificationEmailAddress"  | Indirizzo di posta elettronica per ricevere le notifiche per gli avvisi configurati all'interno dell'hub dati.|
|"aggiornamen" aadAppClientId ""  |[**Facoltativo**] Parametro da includere in input. JSON solo se Azure AD app esiste già.  -True/false. False per un'installazione per la prima volta e true per lo scenario di aggiornamento.|
|"aadAppClientId"  | [**Facoltativo**] Parametro da includere in input. JSON solo se Azure AD app esiste già.  |
|"aadAppClientSecret"   | [**Facoltativo**] Parametro da includere in input. JSON solo se Azure AD app esiste già.|


Input JSON di esempio:

```json
{  
   "sku":"both", 
   "subscriptionId": "da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
   "datahubResourceGroup": "dummy-test-dh1", 
   "datahubLocation": "westus2", 
   "datahubWebsiteName": "dummy-test-dh1", 
   "acceleratorResourceGroup": "dummy-test-acc1", 
   "acceleratorLocation": "westus2", 
   "acceleratorWebsiteName": "dummy-test-acc1", 
   "sentinelUsername": "dummy-dev", 
   "farmbeatsAppId": "c3cb3xxx-27xx-4xxb-8xx6-3xxx2xxdxxx5c", 
   "notificationEmailAddress": "dummy@microsoft.com", 
   "updateIfExists": true
}
```
## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Distribuire all'interno di Cloud Shell riga di comando basata su browser

Nell'ambito del flusso di lavoro del Marketplace è stato creato un gruppo di risorse e è stato firmato il contratto di licenza con l'utente finale, che può essere rivisto una volta nell'ambito della distribuzione effettiva. La distribuzione verrà eseguita tramite Azure Cloud Shell (riga di comando basata su browser) usando l'ambiente bash.  

> [!NOTE]
> Le sessioni di Cloud Shell inattive scadono dopo 20 minuti. Provare a completare la distribuzione entro questo intervallo di tempo.

1. Accedere al portale di **Azure** e selezionare la sottoscrizione desiderata e il tenant di Active Directory.
2. Avviare **cloud Shell** dalla finestra di spostamento in alto nel portale di **Azure** .

   ![Beat Farm progetto](./media/prepare-for-deployment/navigation-bar-1.png)

3. Selezionare una sottoscrizione per creare un account di archiviazione e una condivisione File di Microsoft Azure.
4. Selezionare **Create storage** (Crea risorsa di archiviazione).
5. Selezionare l'elenco a discesa ambiente dal lato sinistro della finestra della shell (bash).

   ![Beat Farm progetto](./media/prepare-for-deployment/bash-1-1.png)

### <a name="deployment-scenario-1"></a>Scenario di distribuzione 1

Il programma di installazione crea il Azure AD (si dispone delle autorizzazioni di lettura del tenant di Active Directory).  

1. Scaricare il modello [input. JSON](https://aka.ms/PPInputJsonTemplate) . Includere il applicazione Azure ID client e la password nel file input. JSON e salvarlo.
2. Aprire il file scaricato in un blocco note e popolare il file immettendo i valori.

    ```json
    {
    "sku":"both",
    "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
    "datahubResourceGroup":"dummy-test-dh1",  
    "location":"westus2",  
    "datahubWebsiteName":"dummy-test-dh1",  
    "acceleratorResourceGroup":"dummy-test-acc1",  
    "acceleratorWebsiteName":"dummy-test-acc1",  
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummyuser@org1.com",
    "updateIfExists":true
    }

    ```

    > [!NOTE]
    > È possibile ignorare questo passaggio e gli input richiederanno in fase di esecuzione.

3. Salvare il file e prendere nota del percorso (nel computer locale).  
4. Passare a Azure Cloud Shell e, dopo l'autenticazione, selezionare il caricamento (vedere l'icona evidenziata nell'immagine seguente) e caricare il file input. JSON in Cloud Shell archiviazione. Non è necessario passare il parametro Azure AD all'interno di JSON perché il programma di installazione creerà e configurerà l'Azure AD registrazione dell'app.

   ![Beat Farm progetto](./media/prepare-for-deployment/bash-2-1.png)

5. Digitare o incollare il "comando di distribuzione" nel Cloud Shell. Assicurarsi di modificare il percorso di input. File JSON e premere INVIO.  

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   Lo script Scarica automaticamente tutte le dipendenze e compila il deployer. Viene quindi richiesto di accettare il contratto di licenza con l'utente finale (EULA) di Azure FarmBeats.

   - Immettere "Y" Se si accettano e si procederà al passaggio successivo.
   - Immettere ' n'se non si accettano le condizioni e la distribuzione terminerà.

   Verrà quindi richiesto di immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere a https://microsoft.com/devicelogin con le credenziali di Azure.

   > [!NOTE]
   > Il codice scade dopo 60 minuti. Alla scadenza è possibile riavviare digitando di nuovo il comando di distribuzione.

6. Al prompt successivo immettere la password dell'account Sentinel.
7. Il programma di installazione convalida ora e avvia la distribuzione, operazione che può richiedere circa 20 minuti.
8. Una volta completata la distribuzione, si riceveranno i collegamenti di output seguenti:
    - **URL dell'hub dati**: collegamento di spavalderia per provare le API FarmBeats di Azure.
    - **URL acceleratore**: interfaccia utente per esplorare l'acceleratore Smart Farm di Azure FarmBeats.
    - File di log del **deployer**-file di log creato durante la distribuzione. Può essere usato per la risoluzione dei problemi.

    - Immettere "Y" Se si accettano e si procederà al passaggio successivo.
    - Immettere ' n'se non si accettano le condizioni e la distribuzione terminerà.

   Quindi verrà richiesto di immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere per https://microsoft.com/devicelogin con le credenziali di Azure.

   > [!NOTE]
    > Prendere nota di questi e tenere il percorso del file di log della distribuzione utile per un uso futuro.


### <a name="deployment-scenario-2"></a>Scenario di distribuzione 2

Per la distribuzione viene usata la registrazione di Azure Active Directory app esistente.

1. Scarica [input JSON](https://aka.ms/PPInputJsonTemplate) include l'ID client e la password applicazione Azure nel file input. JSON, salvarlo.

    ```json
       {
       "sku":"both",
       "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
       "datahubResourceGroup":"dummy-test-dh1",  
       "location":"westus2",  
       "datahubWebsiteName":"dummy-test-dh1",  
       "acceleratorResourceGroup":"dummy-test-acc1",  
       "acceleratorWebsiteName":"dummy-test-acc1",  
       "sentinelUsername":"dummy-dev",
       "notificationEmailAddress":"dummyuser@org1.com",
       "updateIfExists": true,
       "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
       "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"
       }
     ```

     Seguire i passaggi rimanenti:

2. Prendere nota del percorso del file input. JSON (nel computer locale).
3. Passare a Azure Cloud Shell ancora una volta e l'autenticazione è stata completata, selezionare il pulsante carica (vedere l'icona evidenziata nell'immagine seguente) e caricare il file input. JSON in Cloud Shell archiviazione.

    ![Beat Farm progetto](./media/prepare-for-deployment/bash-2-1.png)

4. Digitare o incollare il *comando di distribuzione* nel cloud Shell. Assicurarsi di modificare il percorso di input. File JSON e premere INVIO.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   Seguire i passaggi rimanenti:

5. Lo script Scarica automaticamente tutte le dipendenze e compila il deployer.
6. Verrà richiesto di leggere e accettare il contratto di licenza con l'utente finale (EULA) di Azure FarmBeats.

   - Immettere "Y" Se si accettano e si procederà al passaggio successivo.
   - Immettere ' n'se non si accettano le condizioni e la distribuzione terminerà.

7. Verrà richiesto di immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere per https://microsoft.com/devicelogin con le credenziali di Azure.
8. Il programma di installazione convaliderà ora e inizierà a creare le risorse, che possono richiedere circa 20 minuti. Durante questo periodo, la sessione viene mantenuta attiva Cloud Shell.
9. Quando la distribuzione viene completata correttamente, si riceveranno i collegamenti di output seguenti:
   - **URL dell'hub dati**: collegamento di spavalderia per provare le API FarmBeats.
   - **URL acceleratore**: interfaccia utente per esplorare l'acceleratore Smart Farm FarmBeats.
   - **File di log del deployer**: Salva i log durante la distribuzione e può essere usato per la risoluzione dei problemi.

Se si verificano problemi, vedere la sezione relativa alla [risoluzione dei](troubleshoot-project-farmbeats.md)problemi.


## <a name="validate-deployment"></a>Convalida distribuzione

### <a name="data-hub"></a>Hub dati

Al termine dell'installazione dell'hub dati, si riceverà l'URL per accedere alle API FarmBeats di Azure tramite l'interfaccia spavalderia nel formato: https://\<yourdatahub-website-name >. azurewebsites. NET

1. Per accedere tramite spavalderia, copiare e incollare l'URL nel browser.
2. Accedere con le credenziali portale di Azure.
3. Test di integrità (facoltativo)

     - È possibile accedere al portale di spavalderia usando il collegamento Data Hub, che è stato ricevuto come output per una distribuzione corretta.
     - Tipi estesi ottenere l'API: selezionare "try it out/Execute Accoda"
     - Si dovrebbe ricevere il codice di risposta del server 200 e non un'eccezione, ad esempio 403 "utente non autorizzato".

### <a name="accelerator"></a>Acceleratore

Al termine dell'installazione dell'acceleratore, si riceverà l'URL per accedere all'interfaccia utente di FarmBeats nel formato: https://\<Accelerator-website-name >. azurewebsites. NET

1. Per accedere da Accelerator, copiare e incollare l'URL nel browser.
2. Accedere con le credenziali portale di Azure.
3. Eseguire un test di integrità facoltativo.

    - Controllare se è possibile accedere al portale Accelerator usando il collegamento di accelerazione ricevuto come output per una distribuzione corretta.
    - Selezionare **Crea Farm**.
    - Sotto l'icona "?" aprire le guide FarmBeats usando il pulsante **Get Started** (attività iniziali).

## <a name="upgrade"></a>Aggiornamento

I passaggi per l'aggiornamento sono simili alla prima installazione. A tale scopo, seguire questa procedura:

1. Accedere a portale di Azure e selezionare la sottoscrizione desiderata e il tenant di Active Directory.
2. Avviare Cloud Shell dal riquadro di spostamento in alto nel portale di Azure.

   ![Beat Farm progetto](./media/prepare-for-deployment/navigation-bar-1.png)

3. Selezionare una sottoscrizione per creare un account di archiviazione e una condivisione file File di Azure.
4. Selezionare **Create storage** (Crea risorsa di archiviazione).
5. Selezionare l'ambiente dall'elenco a discesa a sinistra della shell (bash).
6. Apportare modifiche al file input. JSON solo se necessario e caricarle nel Azure Cloud Shell. Ad esempio, è possibile aggiornare l'indirizzo di posta elettronica per la notifica che si vuole ricevere.
7. Prendere nota del percorso del file input. JSON (nel computer locale).
8. Passare a Azure Cloud Shell. Una volta completata l'autenticazione, selezionare il pulsante carica (vedere l'icona evidenziata nell'immagine seguente) e caricare il file input. JSON in Cloud Shell archiviazione.

   ![Beat Farm progetto](./media/prepare-for-deployment/bash-2-1.png)

9. Digitare o incollare il **comando di distribuzione** nel cloud Shell. Assicurarsi di modificare il percorso di input. File JSON e premere INVIO.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
    Seguire i passaggi rimanenti:

10. Il programma di installazione richiede automaticamente gli input necessari in fase di esecuzione:

    - Immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere per https://microsoft.com/devicelogin con le credenziali di Azure.

     > [!NOTE]
     > Il codice scade dopo 60 minuti. Alla scadenza è possibile riavviare digitando di nuovo il comando di distribuzione.

     - Password Sentinel

11. Il programma di installazione ora convalida e avvia la creazione delle risorse, operazione che può richiedere circa 20 minuti.
12. Una volta completata la distribuzione, si riceveranno i collegamenti di output seguenti:

    - **URL dell'hub dati**: collegamento di spavalderia per provare le API FarmBeats.
    - **URL acceleratore**: interfaccia utente per esplorare l'acceleratore Smart Farm FarmBeats.
    - **File di log del deployer**: Salva i log durante la distribuzione. Può essere usato per la risoluzione dei problemi.

    > [!NOTE]
    > Prendere nota dei collegamenti precedenti e tenere il percorso del file di log della distribuzione utile per un uso futuro.

## <a name="uninstall"></a>Disinstallare

Attualmente non è supportata la disinstallazione automatizzata di FarmBeats tramite il programma di installazione. Per rimuovere l'hub dati o l'acceleratore, nella portale di Azure eliminare il gruppo di risorse in cui sono installati questi componenti o eliminare manualmente le risorse.

Ad esempio, se l'hub dati e l'acceleratore sono stati distribuiti in due gruppi di risorse diversi, i gruppi di risorse vengono eliminati come indicato di seguito:

1. Accedere al portale di Azure.
2. Selezionare il proprio account nell'angolo in alto a destra e passare al tenant Azure AD desiderato in cui si vuole distribuire Microsoft FarmBeats.

   > [!NOTE]
   > Per il corretto funzionamento dell'acceleratore è necessario l'hub dati. Non è consigliabile disinstallare Data Hub senza disinstallare Accelerator.

3. Selezionare gruppi di risorse e digitare il nome del gruppo di risorse dell'hub dati o del tasto di scelta rapida che si vuole eliminare.
4. Selezionare il nome del gruppo di risorse. Digitare di nuovo il nome per eseguire il doppio controllo e fare clic su Elimina per rimuovere il gruppo di risorse e tutte le risorse sottostanti.
5. In alternativa, è possibile eliminare ogni risorsa manualmente, operazione sconsigliata.
7. Per eliminare/disinstallare l'hub dati, passare al gruppo di risorse direttamente in Azure ed eliminare il gruppo di risorse da questa posizione.

## <a name="next-steps"></a>Passaggi successivi

Azure FarmBeats è stato distribuito. A questo punto, informazioni su come [creare farm](manage-farms.md#create-farms).
