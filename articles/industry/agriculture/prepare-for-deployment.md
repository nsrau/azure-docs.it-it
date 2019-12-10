---
title: Distribuire Azure FarmBeats
description: Questo articolo descrive come distribuire Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 27bf62cb328273db1f7bdd44117853b00feca9ae
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941577"
---
# <a name="deploy-azure-farmbeats"></a>Distribuire Azure FarmBeats

Questo articolo descrive come configurare Azure FarmBeats.

Azure FarmBeats è una soluzione estendibile e specifica del settore per l'allevamento basato sui dati che consente il provisioning e la connettività del dispositivo sensore al cloud di Azure, alla raccolta dei dati di telemetria e alle aggregazioni. Azure FarmBeats include un'ampia gamma di sensori, ad esempio fotocamere, droni e sensori di suolo. Con Azure FarmBeats è possibile gestire i dispositivi dal cloud, che include la gestione dell'infrastruttura e dei servizi in Azure per i dispositivi pronti per l'uso e l'uso di un'app Web e per dispositivi mobili estendibile per fornire visualizzazione, avvisi e informazioni dettagliate.

> [!NOTE]
> Azure FarmBeats è supportato solo in ambienti di cloud pubblico. Per ulteriori informazioni, vedere [che cos'è un cloud pubblico?](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Azure FarmBeats include i due componenti seguenti:

- **Datahub**: il livello piattaforma di Azure FarmBeats, che consente di compilare, archiviare, elaborare dati e di ottenere informazioni dettagliate da pipeline di dati nuove o esistenti. Questo livello piattaforma è utile per l'esecuzione e la compilazione di pipeline di dati e modelli di agricoltura.

- **Acceleratore**: il livello della soluzione di Azure FarmBeats, che include un'applicazione incorporata per illustrare le funzionalità di Azure FarmBeats usando i modelli di agricoltura creati in precedenza. Questo livello di soluzione consente di creare limiti della farm e di ottenere informazioni dettagliate dai dati agricoli all'interno del contesto del limite della farm.

Una distribuzione rapida di Azure FarmBeats dovrebbe richiedere meno di un'ora. I costi per datahub e Accelerator variano a seconda dell'utilizzo.

## <a name="deployed-resources"></a>Risorse distribuite

La distribuzione di Azure FarmBeats crea le risorse seguenti nella sottoscrizione:

| Serie. di serie  | Nome risorsa  | Componente FarmBeats  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Datahub       |
|2  |    Application Insights      |     Datahub/acceleratore     |
|3  |Cache Redis di Azure   |Datahub   |
|4  |       Azure Key Vault    |  Datahub/acceleratore        |
|5  |    Azure Time Series Insights       |     Datahub      |
|6 |      Spazio dei nomi dell'hub eventi di Azure    |  Datahub       |
|7  |    Azure Data Factory V2       |     Datahub/acceleratore      |
|8  |Account Batch    |Datahub   |
|9  |       Account di archiviazione     |  Datahub/acceleratore        |
|10  |    App per la logica        |     Datahub      |
|11  |    Connessione API        |     Datahub      |
|12|      Servizio app di Azure      |  Datahub/acceleratore       |
|13 |    Piano di servizio app        |     Datahub/acceleratore      |
|14 |Account Azure Maps     |Acceleratore    |

Azure FarmBeats è disponibile per il download in Azure Marketplace, a cui è possibile accedere direttamente dalla portale di Azure.  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>Creare un'offerta Azure FarmBeats in Azure Marketplace

Per creare un'offerta Azure FarmBeats in Azure Marketplace, seguire questa procedura:

1. Accedere al portale di Azure, selezionare il proprio account in alto a destra e quindi passare al tenant di Azure Active Directory (Azure AD) in cui si vuole distribuire Azure FarmBeats.
1. Azure FarmBeats è disponibile in Azure Marketplace. Nella pagina di **Azure Marketplace** selezionare **Get it Now (Ottieni ora**).
1. Selezionare **Create** (Crea).

> [!NOTE]
> Il completamento dell'offerta in Azure Marketplace è solo una parte dell'installazione. Per completare la distribuzione di Azure FarmBeats nella sottoscrizione di Azure, seguire le istruzioni riportate nelle sezioni successive.

## <a name="prepare"></a>Prepara

Prima di poter distribuire Azure FarmBeats, sono necessarie le autorizzazioni seguenti:

- **Tenant**: accesso in lettura
- **Sottoscrizione**: collaboratore o proprietario
- **Gruppo di risorse**: proprietario

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la distribuzione, verificare che siano soddisfatti i prerequisiti seguenti:

- Un account Sentinel
- Registrazione di un'app Azure AD

## <a name="create-a-sentinel-account"></a>Creare un account Sentinel    

Un account con Sentinel consente di scaricare le immagini satellite Sentinel dal sito web ufficiale al dispositivo. Per creare un account gratuito, procedere come segue:

1. Andare alla [pagina di registrazione dell'account Sentinel](https://scihub.copernicus.eu/dhus/#/self-registration).
1. Nel modulo di registrazione, specificare il nome, il cognome, il nome utente, la password e l'indirizzo di posta elettronica.

Un messaggio di posta elettronica di verifica verrà inviato all'indirizzo di posta elettronica registrato per la conferma. Selezionare il collegamento per confermare l'indirizzo di posta elettronica. Il processo di registrazione è stato completato.

## <a name="create-an-azure-ad-app-registration"></a>Creare una registrazione dell'app Azure AD

Per l'autenticazione e l'autorizzazione in Azure FarmBeats, è necessario disporre di una registrazione dell'app Azure AD. È possibile crearlo in uno dei due modi seguenti:

* **Opzione 1**: il programma di installazione può creare automaticamente la registrazione, purché si disponga delle autorizzazioni di accesso necessarie per tenant, sottoscrizione e gruppo di risorse. Se queste autorizzazioni sono disponibili, passare alla sezione ["preparare il file input. JSON"](#prepare-the-inputjson-file) .

* **Opzione 2**: è possibile creare e configurare manualmente la registrazione prima di distribuire Azure FarmBeats. Questo metodo è consigliato se non si hanno le autorizzazioni necessarie per creare e configurare una registrazione dell'app Azure AD all'interno della sottoscrizione. Chiedere all'amministratore di usare lo [script personalizzato](https://aka.ms/FarmBeatsAADScript), che consentirà all'amministratore IT di generare e configurare automaticamente la registrazione dell'app Azure AD nel portale di Azure. Come output per l'esecuzione di questo script personalizzato con l'ambiente PowerShell, l'amministratore IT deve condividere un ID client dell'applicazione Azure AD e il segreto della password. Prendere nota dei valori seguenti.

    Per eseguire lo script di registrazione dell'app Azure AD, seguire questa procedura:

    1. Scaricare lo [script](https://aka.ms/FarmBeatsAADScript).
    1. Accedere al portale di Azure e selezionare la sottoscrizione e Azure AD tenant.
    1. Avviare Cloud Shell dalla barra degli strumenti nella parte superiore della portale di Azure.

        ![FarmBeats progetto](./media/prepare-for-deployment/navigation-bar-1.png)

    1. Se si è un utente per la prima volta, viene richiesto di selezionare una sottoscrizione per creare un account di archiviazione e Microsoft Azure condivisione file. Selezionare **Create storage** (Crea risorsa di archiviazione).
    1. Per la prima volta gli utenti possono anche scegliere tra esperienza Shell, bash o PowerShell. Scegliere PowerShell.
    1. Caricare lo script (dal passaggio 1) in Cloud Shell e annotare il percorso del file caricato.

        > [!NOTE]
        > Per impostazione predefinita, il file viene caricato nella Home Directory.

        Usare lo script seguente:

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. Prendere nota dell'ID dell'applicazione Azure AD e del segreto client da condividere con la persona che distribuisce Azure FarmBeats.

### <a name="prepare-the-inputjson-file"></a>Preparare il file input. JSON

Come parte dell'installazione, creare un file *input. JSON* , come illustrato di seguito:

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

Questo file è il file di input da Azure Cloud Shell. Contiene i parametri i cui valori vengono utilizzati durante l'installazione. Tutti i parametri nel file JSON devono essere sostituiti con valori appropriati o rimossi. Vengono rimossi, il programma di installazione richiederà l'utente durante l'installazione.

Prima di preparare il file, esaminare i parametri elencati nella tabella seguente:

|Comando | Description|
|--- | ---|
|sku  | Offre la possibilità di scaricare uno o entrambi i componenti di Azure FarmBeats. Specifica i componenti da scaricare. Per installare solo datahub, usare "onlydatabhub". Per installare datahub e Accelerator, usare "both".|
|subscriptionId | Specifica la sottoscrizione per l'installazione di Azure FarmBeats.|
|datahubResourceGroup| Nome del gruppo di risorse per le risorse di datahub.|
|location |Il percorso in cui si desidera creare le risorse.|
|acceleratorWebsiteName |Prefisso URL univoco per assegnare un nome all'istanza di datahub.|
|acceleratorResourceGroup  | Prefisso URL univoco per assegnare un nome al sito Web Accelerator.|
|datahubWebsiteName  | Prefisso URL univoco per assegnare un nome al sito Web datahub. |
|sentinelUsername | Nome utente con cui accedere, ad esempio `https://scihub.copernicus.eu/dhus/#/self-registration`.|
|notificationEmailAddress  | Indirizzo di posta elettronica per ricevere le notifiche per gli avvisi configurati all'interno dell'istanza di datahub.|
|updateIfExists| Opzionale Parametro da includere nel file *input. JSON* solo se si vuole aggiornare un'istanza di Azure FarmBeats esistente. Per un aggiornamento, altri dettagli, ad esempio i nomi dei gruppi di risorse e le località, devono essere uguali.|
|aadAppClientId | Opzionale Parametro da includere nel file *input. JSON* solo se l'app Azure ad esiste già.  |
|aadAppClientSecret  | Opzionale Parametro da includere nel file *input. JSON* solo se l'app Azure ad esiste già.|

## <a name="deploy-the-app-within-cloud-shell"></a>Distribuire l'app all'interno Cloud Shell

Come parte del flusso di lavoro del Marketplace discusso in precedenza, è necessario aver creato un gruppo di risorse e aver firmato il contratto di licenza, che è possibile riesaminare come parte della distribuzione effettiva. È possibile distribuire l'app tramite l'interfaccia della riga di comando Cloud Shell basata su browser usando l'ambiente bash. Per eseguire la distribuzione tramite Cloud Shell, continuare con le sezioni successive.

> [!NOTE]
> Le sessioni di Cloud Shell inattive scadono dopo 20 minuti. Provare a completare la distribuzione entro questo intervallo di tempo.

1. Accedere al portale di Azure e selezionare la sottoscrizione e il tenant di Azure che si vuole usare.
1. Avviare Cloud Shell dalla barra degli strumenti nella parte superiore della portale di Azure.
1. Se si usa Cloud Shell per la prima volta, viene richiesto di selezionare una sottoscrizione per creare un account di archiviazione e una condivisione File di Azure.
1. Selezionare **Crea archiviazione**.  
1. Per l'ambiente selezionare **bash** (non PowerShell).

## <a name="deployment-scenario-1"></a>Scenario di distribuzione 1

In questo scenario, descritto in precedenza in "opzione 1", il programma di installazione crea automaticamente la registrazione dell'app Azure AD. Per distribuire FarmBeats, eseguire le operazioni seguenti:

1. Copiare il modello JSON di esempio seguente e salvarlo come *input. JSON*. Assicurarsi di prendere nota del percorso del file nel computer locale.

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

1. Aprire Azure Cloud Shell. Al termine dell'autenticazione, selezionare il pulsante **carica** (evidenziato nella figura seguente), quindi caricare il file *input. JSON* in cloud Shell archiviazione.  

    ![FarmBeats progetto](./media/prepare-for-deployment/bash-2-1.png)

1. Passare alla Home directory in Cloud Shell. Per impostazione predefinita, il nome della directory è */home/\<nome utente >* .
1. In Cloud Shell immettere il comando seguente. Assicurarsi di modificare il percorso del file *input. JSON* , quindi premere il tasto INVIO.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Il programma di installazione scarica automaticamente tutte le dipendenze e compila il deployer. Viene richiesto di accettare le condizioni di licenza di Azure FarmBeats.

     - Se si accetta, immettere **Y** per procedere al passaggio successivo.
     - Se non si accettano, immettere **N**e la distribuzione verrà terminata.

1. Al prompt dei comandi immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere alla [pagina di accesso del dispositivo](https://microsoft.com/devicelogin) con le credenziali di Azure.

    > [!NOTE]
    > Il token scade dopo 60 minuti. Se scade, è possibile riavviare digitando il comando di distribuzione.

1. Al prompt immettere la password dell'account Sentinel.

   Il programma di installazione convalida e avvia la distribuzione, operazione che può richiedere circa 20 minuti.

   Una volta completata la distribuzione, si riceveranno i collegamenti di output seguenti:

    - **URL di datahub**: collegamento di spavalderia per provare le API FarmBeats di Azure.
    - **URL acceleratore**: interfaccia utente per l'esplorazione dell'acceleratore Smart Farm di Azure FarmBeats.
    - **File di log del deployer**: il file di log creato durante la distribuzione. È possibile usarlo per la risoluzione dei problemi, se necessario.

## <a name="deployment-scenario-2"></a>Scenario di distribuzione 2

In questo scenario, descritto in precedenza in "opzione 2", si usa la registrazione dell'app Azure Active Directory esistente per distribuire FarmBeats seguendo questa procedura:

1. Copiare il file JSON seguente, che include il applicazione Azure ID client e la password nel file *input. JSON* e salvarlo. Assicurarsi di prendere nota del percorso del file nel computer locale.

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

1. Passare nuovamente a Azure Cloud Shell e l'autenticazione è stata completata.
1. Selezionare il pulsante **carica** (evidenziato nella figura seguente) e quindi caricare il file *input. JSON* in cloud Shell archiviazione.

    ![FarmBeats progetto](./media/prepare-for-deployment/bash-2-1.png)

1. Passare alla Home directory in Cloud Shell. Per impostazione predefinita, il nome della directory è */home/\<nome utente >* .
1. In Cloud Shell immettere il comando seguente. Assicurarsi di modificare il percorso del file *input. JSON* , quindi premere il tasto INVIO.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     Il programma di installazione scarica automaticamente tutte le dipendenze e compila il deployer. Viene richiesto di accettare le condizioni di licenza di Azure FarmBeats.

     - Se si accetta, immettere **Y** per procedere al passaggio successivo.
     - Se non si accettano, immettere **N**e la distribuzione verrà terminata.

1. Al prompt dei comandi immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere alla [pagina di accesso del dispositivo](https://microsoft.com/devicelogin) con le credenziali di Azure.

   Il programma di installazione convalida e avvia la distribuzione, operazione che può richiedere circa 20 minuti.

   Una volta completata la distribuzione, si riceveranno i collegamenti di output seguenti:

    - **URL di datahub**: collegamento di spavalderia per provare le API FarmBeats di Azure.
    - **URL acceleratore**: interfaccia utente per l'esplorazione dell'acceleratore Smart Farm di Azure FarmBeats.
    - **File di log del deployer**: il file di log creato durante la distribuzione. È possibile usarlo per la risoluzione dei problemi, se necessario.

Se si verificano problemi, vedere la sezione relativa alla [risoluzione dei](troubleshoot-project-farmbeats.md)problemi.


## <a name="validate-the-deployment"></a>Convalidare la distribuzione

### <a name="datahub"></a>Datahub

Al termine dell'installazione di datahub, si riceverà l'URL per accedere alle API FarmBeats di Azure tramite l'interfaccia spavalderia nel formato https://\<yourdatahub-website-name >. azurewebsites. NET/spavalderia.

1. Per accedere tramite spavalderia, copiare e incollare l'URL nel browser.
2. Accedere con le credenziali di portale di Azure.
3. È possibile visualizzare il spavalderia ed eseguire tutte le operazioni REST sulle API FarmBeats di Azure. Ciò indica la corretta distribuzione di Azure FarmBeats.

### <a name="accelerator"></a>Acceleratore

Al termine dell'installazione dell'acceleratore, si riceverà l'URL per accedere all'interfaccia utente di Azure FarmBeats nel formato https://\<Accelerator-website-name >. azurewebsites. NET.

1. Per accedere da Accelerator, copiare e incollare l'URL nel browser.
1. Accedere con le credenziali di portale di Azure.

## <a name="upgrade"></a>Aggiornamento

Le istruzioni per l'aggiornamento sono simili a quelle per la prima installazione. Eseguire le operazioni seguenti:

1. Accedere al portale di Azure, quindi selezionare la sottoscrizione desiderata e il tenant di Azure.
1. Aprire Cloud Shell dalla parte superiore dello spostamento della portale di Azure.

   ![FarmBeats progetto](./media/prepare-for-deployment/navigation-bar-1.png)

1. Nell'elenco a discesa **ambiente** a sinistra selezionare **bash**.
1. Se necessario, apportare modifiche al file *input. JSON* e quindi caricarlo in cloud Shell. Ad esempio, è possibile aggiornare l'indirizzo di posta elettronica per la notifica che si vuole ricevere.
1. Digitare o incollare i due comandi seguenti in Cloud Shell. Assicurarsi di modificare il percorso del file *input. JSON* , quindi premere il tasto INVIO.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   Il programma di installazione richiede automaticamente gli input necessari in fase di esecuzione.

1. Immettere un token di accesso per la distribuzione. Copiare il codice generato e accedere alla [pagina di accesso del dispositivo](https://microsoft.com/devicelogin) con le credenziali di Azure.
1. Immettere la password di Sentinel.

   Il programma di installazione ora convalida e avvia la creazione delle risorse, operazione che può richiedere circa 20 minuti.

   Una volta completata la distribuzione, si riceveranno i collegamenti di output seguenti:
    - **URL di datahub**: collegamento di spavalderia per provare le API FarmBeats di Azure.
    - **URL acceleratore**: interfaccia utente per l'esplorazione dell'acceleratore Smart Farm di Azure FarmBeats.
    - **File di log del deployer**: il file di log creato durante la distribuzione. È possibile usarlo per la risoluzione dei problemi, se necessario.

> [!NOTE]
> Prendere nota dei valori precedenti per un uso futuro.


## <a name="uninstall"></a>Disinstallare

Attualmente, non è supportata la disinstallazione automatica di Azure FarmBeats tramite il programma di installazione. Per rimuovere datahub o Accelerator, nella portale di Azure eliminare il gruppo di risorse in cui sono installati questi componenti oppure eliminare manualmente le risorse.

Se, ad esempio, sono stati distribuiti datahub e Accelerator in due gruppi di risorse diversi, è possibile eliminare i gruppi di risorse come indicato di seguito:

1. Accedere al portale di Azure.
1. Selezionare l'account in alto a destra e passare al tenant Azure AD in cui si vuole distribuire Azure FarmBeats.

   > [!NOTE]
   > Per il corretto funzionamento di Accelerator è necessario datahub. Non è consigliabile disinstallare datahub senza disinstallare anche l'acceleratore.

1. Selezionare **gruppi di risorse**e quindi immettere il nome del gruppo di risorse datahub o Accelerator che si vuole eliminare.
1. Selezionare il nome del gruppo di risorse. Digitare nuovamente il nome per confermarlo, quindi selezionare **Elimina** per rimuovere il gruppo di risorse e tutte le risorse sottostanti.
   In alternativa, è possibile eliminare ogni risorsa manualmente, un metodo sconsigliato.
1. Per eliminare o disinstallare datahub, passare al gruppo di risorse direttamente in Azure e quindi eliminare il gruppo di risorse da questa posizione.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come distribuire Azure FarmBeats. Successivamente, viene illustrato come [creare farm FarmBeats](manage-farms.md#create-farms).
