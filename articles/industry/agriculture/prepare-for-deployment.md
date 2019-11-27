---
title: Distribuire Azure FarmBeats
description: Viene descritto come distribuire FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ccd8fc293d284150d7318242b71a46a99fec12fa
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406314"
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
|1  |       Azure Cosmos DB   |  Hub di dati       |
|2  |    Application Insights      |     Hub dati/acceleratore     |
|3  |Cache Redis di Azure   |Hub di dati   |
|4  |       Azure KeyVault    |  Hub dati/acceleratore        |
|5  |    Time Series Insights       |     Hub di dati      |
|6 |      Spazio dei nomi EventHub    |  Hub di dati       |
|7  |    Azure Data Factory V2       |     Hub dati/acceleratore      |
|8  |Account Batch    |Hub di dati   |
|9  |       Account di archiviazione     |  Hub dati/acceleratore        |
|10  |    App per la logica        |     Hub di dati      |
|11  |    Connessione API        |     Hub di dati      |
|12|      Servizio app      |  Hub dati/acceleratore       |
|13 |    Piano di servizio app        |     Hub dati/acceleratore      |
|14 |Account Azure Maps     |Acceleratore    |
|15 |       Time Series Insights      |  Hub di dati     |

Azure FarmBeats è disponibile per il download da Azure Marketplace. È possibile accedervi direttamente dalla portale di Azure.  

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Crea un'offerta Azure FarmBeats nel Marketplace

Seguire questa procedura per creare un'offerta Azure FarmBeats nel Marketplace:

1. Accedere al portale di Azure e selezionare l'account nell'angolo in alto a destra e passare al tenant di Azure AD in cui si vuole distribuire Microsoft Azure FarmBeats.
2. Azure FarmBeats è disponibile in Azure Marketplace. Nella pagina Marketplace selezionare "Get it Now".
3. Selezionare Crea e immettere le informazioni seguenti:
    - nome della sottoscrizione.
    - un nome del gruppo di risorse esistente (solo gruppo di risorse vuoto) o creare un nuovo gruppo di risorse per la distribuzione di Azure FarmBeats. Prendere nota di questo gruppo di risorse nelle sezioni successive.
4. Area in cui si vuole installare Azure FarmBeats. Azure FarmBeats è attualmente supportato nelle aree seguenti: Stati Uniti centrali, Europa occidentale, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti occidentali, Asia sudorientale, Stati Uniti orientali, Australia orientale, Stati Uniti occidentali 2.
5. Selezionare **OK**.
Viene visualizzata la pagina Condizioni per l'utilizzo. Esaminare le condizioni standard del Marketplace oppure selezionare il collegamento ipertestuale per esaminare le condizioni per l'utilizzo.
6. Selezionare **Chiudi**, quindi la casella di controllo "Accetto" e quindi selezionare **Crea**.
7. Il contratto di licenza con l'utente finale (EULA) di Azure FarmBeats è stato firmato sul Marketplace.  
7. Per continuare con la distribuzione, seguire i passaggi successivi di questa guida.

## <a name="prepare"></a>Preparare

Per la distribuzione di Azure FarmBeats sono necessarie le autorizzazioni seguenti:

- Tenant: accesso in lettura
- Sottoscrizione: collaboratore o proprietario
- Gruppo di risorse: proprietario

## <a name="before-you-begin"></a>Prima di iniziare

Prima di avviare la distribuzione, assicurarsi di disporre di quanto segue:

- Account Sentinel
- Registrazione dell'app Azure Active Directory (AD)

## <a name="create-a-sentinel-account"></a>Creare un account Sentinel    

Un account con Sentinel consente di scaricare le immagini satellite Sentinel dal sito web ufficiale al dispositivo. Per creare un account gratuito, seguire questa procedura:

Passare a https://scihub.copernicus.eu/dhus/#/self-registration. Nella pagina di registrazione specificare un nome, un cognome, un nome utente, una password e un indirizzo di posta elettronica.
Un messaggio di posta elettronica di verifica verrà inviato all'indirizzo di posta elettronica registrato per la conferma. Selezionare il collegamento e confermare. Il processo di registrazione è stato completato.

## <a name="create-azure-ad-app-registration"></a>Creazione della registrazione dell'app Azure AD

Per l'autenticazione e l'autorizzazione in Azure FarmBeats, è necessario avere una registrazione dell'applicazione Azure Active Directory che:

- Caso 1: il programma di installazione può creare automaticamente (purché si disponga delle autorizzazioni di accesso necessarie per tenant, sottoscrizione e gruppo di risorse).
- Caso 2: è possibile creare e configurare prima di distribuire Azure FarmBeats (richiede passaggi manuali).

**Caso 1**:: se si ha accesso per creare una registrazione dell'app AAD, è possibile ignorare questo passaggio e consentire al programma di installazione di creare la registrazione dell'app. Passare alla sezione successiva: preparare il [file input. JSON](#prepare-input-json-file)

Se si dispone già di una sottoscrizione, è possibile spostarla direttamente alla procedura successiva.

**Caso 2**: questo metodo è il passaggio preferito quando non si dispone di diritti sufficienti per creare e configurare un Azure ad la registrazione dell'app all'interno della sottoscrizione. Richiedere all'amministratore di usare lo [script personalizzato](https://aka.ms/FarmBeatsAADScript), che consentirà all'amministratore IT di generare e configurare automaticamente la registrazione dell'app Azure AD nel portale di Azure. Come output per l'esecuzione di questo script personalizzato con l'ambiente PowerShell, l'amministratore IT deve condividere un ID client dell'applicazione Azure Active Directory e il segreto della password. Prendere nota dei valori seguenti.

Per eseguire lo script di registrazione dell'applicazione Azure AD, attenersi alla procedura seguente:

1. Scaricare [lo script](https://aka.ms/FarmBeatsAADScript).
2. Accedere a portale di Azure e selezionare la sottoscrizione e il tenant di AD.
3. Avviare Cloud Shell dal riquadro di spostamento in alto nel portale di Azure.

    ![Beat Farm progetto](./media/prepare-for-deployment/navigation-bar-1.png)


4. Per la prima volta agli utenti verrà richiesto di selezionare una sottoscrizione per creare un account di archiviazione e Microsoft Azure condivisione file. Selezionare **Create storage** (Crea risorsa di archiviazione).
5. Per la prima volta verrà richiesto agli utenti di scegliere l'esperienza shell preferita: bash o PowerShell. Scegliere PowerShell.
6. Caricare lo script (dal passaggio 1) nel Cloud Shell e annotare il percorso del file caricato.

    > [!NOTE]
    > Per impostazione predefinita, viene caricato nella Home Directory.

    Usare lo script seguente:

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Prendere nota dell'ID applicazione Azure AD e del segreto client da condividere con la persona che distribuisce Azure FarmBeats.

### <a name="prepare-input-json-file"></a>Preparare il file JSON di input

Come parte dell'installazione, creare un file input. JSON come indicato di seguito:

```json
    {  
       "sku":"both",
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
       "datahubResourceGroup":"dummy-test-dh1",
       "location":"westus2",
       "datahubWebsiteName":"dummy-test-dh1",
       "acceleratorResourceGroup":" dummy-test-acc1",
       "acceleratorWebsiteName":" dummy-test-acc1",
       "sentinelUsername":"dummy-dev",
       "notificationEmailAddress":"dummy@yourorg.com",
       "updateIfExists":true
    }
```

Questo file è il file di input per Azure Cloud Shell e i parametri i cui valori vengono usati durante l'installazione. Tutti i parametri in JSON devono essere sostituiti con valori appropriati o rimossi. Se rimosso, il programma di installazione richiederà l'utente durante l'installazione


Verificare i parametri prima di preparare il file.

|Comando | DESCRIZIONE|
|--- | ---|
|sku  | Offre la possibilità di scaricare uno o entrambi i componenti di Azure FarmBeats. Specifica i componenti da scaricare. Per installare solo l'hub dati, usare "onlydatabhub". Per installare hub dati e acceleratore, usare "both"|
|subscriptionId | Specifica la sottoscrizione per l'installazione di Azure FarmBeats|
|datahubResourceGroup| Nome del gruppo di risorse per le risorse dell'hub dati|
|location |Località in cui si desidera creare le risorse|
|acceleratorWebsiteName |Prefisso URL univoco per assegnare un nome all'hub dati|
|acceleratorResourceGroup  | Prefisso URL univoco per assegnare un nome al sito Web Accelerator.|
|datahubWebsiteName  | Prefisso URL UUnique per assegnare un nome al sito Web dell'hub dati. |
|sentinelUsername | nome utente a cui accedere: https://scihub.copernicus.eu/dhus/#/self-registration.|
|notificationEmailAddress  | Indirizzo di posta elettronica per ricevere le notifiche per gli avvisi configurati all'interno dell'hub dati.|
|updateIfExists|Opzionale Parametro da includere in input. JSON solo se si vuole aggiornare un'istanza di Azure FarmBeats esistente. Per l'aggiornamento, altri dettagli, ad esempio. i nomi dei gruppi di risorse, le località e così via devono essere uguali.|
|aadAppClientId | [**Facoltativo**] Parametro da includere in input. JSON solo se Azure AD app esiste già.  |
|aadAppClientSecret  | [**Facoltativo**] Parametro da includere in input. JSON solo se Azure AD app esiste già.|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Distribuire all'interno di Cloud Shell riga di comando basata su browser

Come parte del flusso di lavoro del Marketplace precedente, è necessario aver creato un gruppo di risorse e aver firmato il contratto di licenza con l'utente finale, che può essere rivisto una sola volta nell'ambito della distribuzione effettiva. La distribuzione può essere eseguita tramite Azure Cloud Shell (riga di comando basata su browser) usando l'ambiente bash. Passare alle sezioni successive per eseguire la distribuzione tramite il Cloud Shell.

> [!NOTE]
> Le sessioni di Cloud Shell inattive scadono dopo 20 minuti. Provare a completare la distribuzione entro questo intervallo di tempo.

1. Accedere a portale di Azure e selezionare la sottoscrizione desiderata e il tenant di Active Directory.
2. Avviare Cloud Shell dal riquadro di spostamento in alto nel portale di Azure.
3. Se si usa il Cloud Shell per la prima volta, verrà richiesto di selezionare una sottoscrizione per creare un account di archiviazione e Microsoft Azure condivisione file.
4. Selezionare **Crea archiviazione**.  

Selezionare l'ambiente come bash (e non PowerShell).

## <a name="deployment-scenario-1"></a>Scenario di distribuzione 1

Il programma di installazione crea la registrazione App Azure AD (caso 1 precedente)

1. Copiare il modello seguente e denominarlo in input. JSON.  
Input JSON di esempio:

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

2. Salvare il file e prendere nota del percorso (nel computer locale).
3. Passare a Azure Cloud Shell e, dopo l'autenticazione, selezionare il caricamento (vedere l'icona evidenziata nell'immagine seguente) e caricare il file input. JSON in Cloud Shell archiviazione.  

    ![Beat Farm progetto](./media/prepare-for-deployment/bash-2-1.png)

4. Passare alla Home directory in cloud Shell. Per impostazione predefinita, è/Home/<username>
5. Digitare o incollare il comando seguente nella Cloud Shell. Assicurarsi di modificare il percorso di input. File JSON e premere INVIO.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Il programma di installazione scarica automaticamente tutte le dipendenze e compila il deployer. Verrà richiesto di accettare il contratto di licenza con l'utente finale (EULA) di Azure FarmBeats.

     - Immettere "Y" Se si accettano e si procederà al passaggio successivo.
     - Immettere ' n'se non si accettano le condizioni e la distribuzione terminerà.

6. Verrà quindi richiesto di immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere a https://microsoft.com/devicelogin con le credenziali di Azure.

    > [!NOTE]
    > Il token scade dopo 60 minuti. Alla scadenza è possibile riavviare digitando di nuovo il comando di distribuzione.

7. Quando richiesto, immettere la password dell'account Sentinel.
8. Il programma di installazione convalida ora e avvia la distribuzione, operazione che può richiedere circa 20 minuti.
9. Una volta completata la distribuzione, si riceveranno i collegamenti di output seguenti:

 - **URL dell'hub dati**: collegamento di spavalderia per provare le API FarmBeats di Azure.
 - **URL acceleratore**: interfaccia utente per esplorare l'acceleratore Smart Farm di Azure FarmBeats.
 - File di log del **deployer**-file di log creato durante la distribuzione. Può essere usato per la risoluzione dei problemi, se necessario.

## <a name="deployment-scenario-2"></a>Scenario di distribuzione 2

La registrazione dell'app Azure Active Directory esistente viene usata per la distribuzione (caso 2 precedente)

1. Copiare il file JSON seguente, che include il applicazione Azure ID client e la password in input. JSON e salvarlo.

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

4. Passare alla Home directory in cloud Shell. Per impostazione predefinita, è/Home/<username>
5. Digitare o incollare il comando seguente nella Cloud Shell. Assicurarsi di modificare il percorso di input. File JSON e premere INVIO.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

Seguire le istruzioni visualizzate.

6. Lo script Scarica automaticamente tutte le dipendenze e compila il deployer.
7. Verrà richiesto di leggere e accettare il contratto di licenza con l'utente finale (EULA) di Azure FarmBeats.

    - Immettere "Y" Se si accettano e si continuerà a eseguire il passaggio successivo.
    - Immettere ' n'se non si accettano le condizioni e la distribuzione terminerà.

8. Verrà richiesto di immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere per https://microsoft.com/devicelogin con le credenziali di Azure.
9. Il programma di installazione convaliderà ora e inizierà a creare le risorse, che possono richiedere circa 20 minuti. Durante questo periodo, la sessione viene mantenuta attiva Cloud Shell.
10. Quando la distribuzione viene completata correttamente, si riceveranno i collegamenti di output seguenti:

 - **URL dell'hub dati**: collegamento di spavalderia per provare le API FarmBeats di Azure.
 - **URL acceleratore**: interfaccia utente per esplorare Azure FarmBeats Accelerator.
 - **File di log del deployer**: file di log creato durante la distribuzione. Può essere usato per la risoluzione dei problemi, se necessario.

Se si verificano problemi, vedere la sezione relativa alla [risoluzione dei](troubleshoot-project-farmbeats.md)problemi.


## <a name="validate-deployment"></a>Convalida distribuzione

### <a name="data-hub"></a>Hub di dati

Al termine dell'installazione dell'hub dati, si riceverà l'URL per accedere alle API FarmBeats di Azure tramite l'interfaccia spavalderia nel formato: https://\<yourdatahub-website-name >. azurewebsites. NET/spavalderia

1. Per accedere tramite spavalderia, copiare e incollare l'URL nel browser.
2. Accedere con le credenziali portale di Azure.
3. Test di integrità (facoltativo)

     - È possibile accedere al portale di spavalderia usando il collegamento Data Hub, che è stato ricevuto come output per una distribuzione corretta.
     - Tipi estesi ottenere l'API: selezionare "try it out/Execute Accoda"
     - Si dovrebbe ricevere il codice di risposta del server 200 e non un'eccezione, ad esempio 403 "utente non autorizzato".

### <a name="accelerator"></a>Acceleratore

Al termine dell'installazione dell'acceleratore, si riceverà l'URL per accedere all'interfaccia utente di Azure FarmBeats nel formato: https://\<Accelerator-website-name >. azurewebsites. NET

1. Per accedere da Accelerator, copiare e incollare l'URL nel browser.
2. Accedere con le credenziali portale di Azure.

## <a name="upgrade"></a>Aggiornamento

I passaggi per l'aggiornamento sono simili alla prima installazione. Seguire questa procedura:

1. Accedere a portale di Azure e selezionare la sottoscrizione desiderata e il tenant di Active Directory.
2. Avviare Cloud Shell dal riquadro di spostamento in alto nel portale di Azure.

   ![Beat Farm progetto](./media/prepare-for-deployment/navigation-bar-1.png)

3. Selezionare l'ambiente come "bash" dall'elenco a discesa a sinistra della shell.
4. Apportare modifiche al file input. JSON solo se necessario e caricarle nel Azure Cloud Shell. Ad esempio, è possibile aggiornare l'indirizzo di posta elettronica per la notifica che si vuole ricevere.
5. Caricare il file input. JSON in Azure Cloud Shell.
6. Digitare o incollare i due comandi seguenti nel Cloud Shell. Assicurarsi di modificare il percorso del file input. JSON e premere INVIO.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
Seguire le istruzioni visualizzate:

7. Il programma di installazione richiede automaticamente gli input richiesti in fase di esecuzione:
8. Immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere per https://microsoft.com/devicelogin con le credenziali di Azure.
9. Password Sentinel
10. Il programma di installazione ora convalida e avvia la creazione delle risorse, operazione che può richiedere circa 20 minuti.
11. Una volta completata la distribuzione, si riceveranno i collegamenti di output seguenti:
 - **URL dell'hub dati**: collegamento di spavalderia per provare le API FarmBeats di Azure.
 - **URL acceleratore**: interfaccia utente per esplorare Azure FarmBeats Accelerator.
 - **File di log del deployer**: Salva i log durante la distribuzione. Può essere usato per la risoluzione dei problemi.

> [!NOTE]
> Prendere nota dei valori precedenti per un uso futuro.


## <a name="uninstall"></a>Disinstallare

Attualmente non è supportata la disinstallazione automatica di Azure FarmBeats tramite il programma di installazione. Per rimuovere l'hub dati o l'acceleratore, nella portale di Azure eliminare il gruppo di risorse in cui sono installati questi componenti o eliminare manualmente le risorse.

Ad esempio, se l'hub dati e l'acceleratore sono stati distribuiti in due gruppi di risorse diversi, i gruppi di risorse vengono eliminati come indicato di seguito:

1. Accedere al portale di Azure.
2. Selezionare il proprio account nell'angolo in alto a destra e passare al tenant Azure AD desiderato in cui si vuole distribuire Azure FarmBeats.

   > [!NOTE]
   > Per il corretto funzionamento dell'acceleratore è necessario l'hub dati. Non è consigliabile disinstallare Data Hub senza disinstallare Accelerator.

3. Selezionare gruppi di risorse e digitare il nome del gruppo di risorse dell'hub dati o del tasto di scelta rapida che si vuole eliminare.
4. Selezionare il nome del gruppo di risorse. Digitare di nuovo il nome per eseguire il doppio controllo e selezionare Elimina per rimuovere il gruppo di risorse e tutte le risorse sottostanti.
5. In alternativa, è possibile eliminare ogni risorsa manualmente, operazione sconsigliata.
7. Per eliminare/disinstallare l'hub dati, passare al gruppo di risorse direttamente in Azure ed eliminare il gruppo di risorse da questa posizione.

## <a name="next-steps"></a>Passaggi successivi

Azure FarmBeats è stato distribuito. A questo punto, informazioni su come [creare farm](manage-farms.md#create-farms).
