---
title: Installare Azure FarmBeats
description: Questo articolo descrive come installare Azure FarmBeats nella sottoscrizione di Azure
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482452"
---
# <a name="install-azure-farmbeats"></a>Installare Azure FarmBeats

Questo articolo descrive come installare Azure FarmBeats nella sottoscrizione di Azure.

Azure FarmBeats è un'offerta business-to-business disponibile in Azure Marketplace. Consente l'aggregazione dei set di dati agricoli tra i provider e la generazione di informazioni dettagliate di utilità pratica. Azure FarmBeats consente di creare modelli di intelligenza artificiale (AI) o Machine Learning (ML) basati su set di dati fusi. I due componenti principali di Azure FarmBeats sono:

- **Datahub**: livello API che consente l'aggregazione, la normalizzazione e la contestualizzazione di diversi set di dati di agricoltura tra diversi provider.

- **Accelerator**: un'applicazione Web di esempio basata su datahub. Viene avviata la visualizzazione e lo sviluppo del modello. L'acceleratore usa le API FarmBeats di Azure per dimostrare la visualizzazione dei dati dei sensori inseriti come grafici e la visualizzazione dell'output del modello come Maps.

## <a name="before-you-start"></a>Prima di iniziare
### <a name="components-installed"></a>Componenti installati

Quando si installa Azure FarmBeats, viene eseguito il provisioning delle risorse seguenti nella sottoscrizione di Azure:

| Risorse di Azure installate  | Componente Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Acceleratore & datahub      |
| Servizio app     |     Acceleratore & datahub     |
| Piano di servizio app   | Acceleratore & datahub  |
| Connessione API    |  Datahub       |
| Cache Redis di Azure       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Acceleratore & datahub      |
| Account Azure Batch    | Datahub   |
| Azure Key Vault |  Acceleratore & datahub        |
| Account Azure Maps       |     Acceleratore    |
| Spazio dei nomi dell'hub eventi    |     Datahub      |
| App per la logica      |  Datahub       |
| Account di archiviazione      |     Acceleratore & datahub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Costi sostenuti

Il costo di Azure FarmBeats è un'aggregazione del costo dei servizi di Azure sottostanti. Le informazioni sui prezzi per i servizi di Azure possono essere calcolate usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator). Il costo effettivo dell'installazione totale può variare in base all'utilizzo. Il costo di stato costante per i due componenti è il seguente:

* Datahub-inferiore a $10 al giorno
* Acceleratore-inferiore a $2 al giorno

### <a name="regions-supported"></a>Aree supportate

Azure FarmBeats è attualmente supportato in ambienti cloud pubblici nelle aree seguenti:
* Australia orientale
* Stati Uniti centrali
* Stati Uniti orientali
* Stati Uniti orientali 2
* Stati Uniti occidentali
* Stati Uniti occidentali 2
* Europa settentrionale
* Europa occidentale
* Asia sud-orientale

### <a name="time-taken"></a>Tempo impiegato

L'intera configurazione di Azure FarmBeats, inclusa la preparazione e l'installazione, importerà meno di un'ora.

## <a name="prerequisites"></a>Prerequisiti    

Prima di avviare l'installazione effettiva di Azure FarmBeats, è necessario completare i passaggi seguenti:

### <a name="create-sentinel-account"></a>Crea account Sentinel
Il programma di installazione di Azure FarmBeats consente di ottenere immagini satellite gratuite dalla Mission di [Sentinel-2](https://scihub.copernicus.eu/) satellite dell'Agenzia dello spazio europeo per la farm. Per configurare questa configurazione, è necessario un account Sentinel.

Per creare un account gratuito con Sentinel, seguire questa procedura:

1. Passare alla pagina di [iscrizione](https://scihub.copernicus.eu/dhus/#/self-registration) ufficiale.
2. Specificare i dettagli richiesti (nome, cognome, nome utente, password e ID di posta elettronica) e completare il modulo.
3. Verrà inviato un collegamento di verifica all'ID di posta elettronica registrato. Selezionare il collegamento fornito nel messaggio di posta elettronica e completare la verifica.

Il processo di registrazione è stato completato dopo aver completato la verifica. Prendere nota del **nome utente** e della **password**Sentinel.

### <a name="decide-subscription-and-region"></a>Decidere la sottoscrizione e l'area geografica

Sono necessari l'ID sottoscrizione di Azure e l'area in cui si vuole installare Azure FarmBeats. Scegliere una delle aree elencate nella sezione [aree supportate](#regions-supported) .

Prendere nota dell' **ID sottoscrizione di Azure** e dell' **area di Azure**.

### <a name="verify-permissions"></a>Verificare le autorizzazioni

È necessario verificare se si dispone di privilegi e autorizzazioni sufficienti nel tenant di Azure che si sta cercando di installare Azure FarmBeats.

È possibile verificare le autorizzazioni di accesso nel portale di Azure seguendo le istruzioni sul [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Per l'installazione di Azure FarmBeats, sono necessarie le autorizzazioni seguenti:
- Accesso in lettura al tenant
- Sottoscrizione-collaboratore o proprietario
- Gruppo di risorse: proprietario.

Inoltre, Azure FarmBeats richiede la registrazione dell'applicazione Azure Active Directory. Sono disponibili due modi per completare il programma di installazione di Azure AD necessario:

**Caso 1**: si dispone delle autorizzazioni di **scrittura** nel tenant di Azure in cui si esegue l'installazione. Questo caso significa che si hanno le autorizzazioni necessarie per creare la registrazione dell'app AAD in modo dinamico durante l'installazione.

È possibile passare direttamente alla sezione [completa iscrizione al Marketplace](#complete-azure-marketplace-sign-up) .


**Caso 2**: non si dispone delle autorizzazioni di **scrittura** nel tenant di Azure. Questo caso è comune quando si sta provando a installare Azure FarmBeats nella sottoscrizione di Azure della società e l'accesso in **scrittura** è limitato solo al gruppo di risorse di cui si è proprietari.
In questo caso, richiedere all'amministratore IT di seguire questa procedura per generare e completare automaticamente la registrazione dell'app Azure AD nel portale di Azure.

1. Scaricare ed estrarre lo [script del generatore di app AAD](https://aka.ms/FarmBeatsAADScript) nel computer locale.
2. Accedere al portale di Azure e selezionare la sottoscrizione e Azure AD tenant.
3. Avviare Cloud Shell dalla barra degli strumenti nella parte superiore della portale di Azure.

    ![FarmBeats progetto](./media/install-azure-farmbeats/navigation-bar-1.png)

4. Scegliere PowerShell come esperienza shell preferita. Per la prima volta viene richiesto agli utenti di selezionare una sottoscrizione e creare un account di archiviazione. Completare l'installazione come indicato.
5. Caricare lo script (dal passaggio 1) in Cloud Shell e annotare il percorso del file caricato.

    > [!NOTE]
    > Per impostazione predefinita, il file viene caricato nella Home Directory.

6. Passare alla Home directory usando il comando "CD" ed eseguire lo script seguente:

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. Immettere il nome del **sito Web datahub** e il nome del **sito Web Accelerator** . Prendere nota dell'output dello script e condividerlo con la persona che installa Azure FarmBeats.

Una volta che l'amministratore IT fornisce i dettagli richiesti, annotare l' **ID client AAD, il segreto client AAD, il nome del sito Web Datahub & il nome del sito Web Accelerator**.

   > [!NOTE]
   > Se si seguono le istruzioni del caso 2, non dimenticare di aggiungere l'ID client AAD & il segreto client AAD come parametri distinti nel [file dei parametri](#prepare-parameters-file)

A questo punto, sono disponibili tutte le informazioni necessarie per passare alla sezione successiva.

### <a name="complete-azure-marketplace-sign-up"></a>Completa iscrizione ad Azure Marketplace

Per poter avviare il processo di installazione basato su cloud Shell, è necessario completare l'iscrizione all'offerta Azure FarmBeats in Azure Marketplace. Per completare l'iscrizione, attenersi alla procedura seguente:

1.  Accedere al portale di Azure. Selezionare il proprio account nell'angolo in alto a destra e passare al tenant Azure AD in cui si vuole installare Azure FarmBeats.

2.  Passare ad Azure Marketplace nel portale e cercare **Azure FarmBeats** nel Marketplace

3.  Viene visualizzata una nuova finestra con una panoramica di Azure FarmBeats. Selezionare **Crea**.

4.  Viene visualizzata una nuova finestra. Completare il processo di iscrizione scegliendo la sottoscrizione, il gruppo di risorse e la posizione corretti in cui si vuole installare Azure FarmBeats.

5.  Una volta convalidati i dettagli immessi, fare clic su **OK**. Viene visualizzata la pagina Condizioni per l'utilizzo. Esaminare i termini e selezionare **Crea** per completare la procedura di iscrizione.

Questo passaggio completa il processo di iscrizione in Azure Marketplace. A questo punto è possibile avviare la preparazione del file dei parametri.

### <a name="prepare-parameters-file"></a>Preparare il file dei parametri
Il passaggio finale della fase dei prerequisiti consiste nella creazione di un file JSON che fungerà da input durante l'installazione del Cloud Shell. I parametri nel file JSON dovranno essere sostituiti con i valori appropriati.

Di seguito è riportato un file JSON di esempio. Scaricare l'esempio e aggiornare i dettagli richiesti.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

È possibile fare riferimento alla tabella dei parametri riportata di seguito per comprendere meglio ogni parametro.

| Parametro | Valore|
|--- | ---|
|sku  | Offre all'utente la possibilità di installare datahub e Accelerator o solo datahub. Per installare solo datahub, usare "datahub". Per installare datahub e Accelerator, usare "both"|
|subscriptionId | Specifica la sottoscrizione di Azure per installare Azure FarmBeats|
|datahubResourceGroup| Specifica il nome del gruppo di risorse per le risorse di datahub. Immettere qui il nome del gruppo di risorse creato in Azure Marketplace|
|posizione |Località/area di Azure in cui si vuole installare Azure FarmBeats|
|datahubWebsiteName  | Prefisso URL univoco per l'applicazione Web datahub |
|acceleratorResourceGroup  | Specifica il nome del gruppo di risorse per le risorse del tasto di scelta rapida|
|acceleratorWebsiteName |Prefisso URL univoco per l'applicazione Web Accelerator|
|sentinelUsername | Nome utente per ottenere immagini satellite Sentinel|
|notificationEmailAddress  | Indirizzo di posta elettronica per ricevere le notifiche per gli avvisi configurati all'interno della datahub|
|updateIfExists| Opzionale Parametro da includere nel file di parametri solo se si vuole aggiornare un'istanza di Azure FarmBeats esistente. Per un aggiornamento, altri dettagli, ad esempio i nomi dei gruppi di risorse e le località, devono essere uguali|
|aadAppClientId | Opzionale Parametro da includere nel file di parametri solo se si usa un'app AAD creata in precedenza. Per altri dettagli, vedere il caso 2 nella sezione [verificare le autorizzazioni](#verify-permissions) |
|aadAppClientSecret  | Opzionale Opzionale Parametro da includere nel file di parametri solo se si usa un'app AAD creata in precedenza. Per altri dettagli, vedere il caso 2 nella sezione [verificare le autorizzazioni](#verify-permissions)|

In base alla tabella precedente e al file JSON di esempio, creare il file JSON dei parametri e salvarlo nel computer locale.

## <a name="install"></a>Installazione

L'installazione effettiva delle risorse di Azure FarmBeats avviene in Cloud Shell interfaccia della riga di comando basata su browser usando l'ambiente bash. Seguire le istruzioni seguenti per installare Azure FarmBeats:

1. Accedere al portale di Azure. Selezionare la sottoscrizione di Azure e il tenant in cui si vuole installare Azure FarmBeats.
2. Avviare **cloud Shell** dalla barra degli strumenti nell'angolo superiore destro della portale di Azure.
3. Scegliere bash come esperienza shell preferita. Selezionare il pulsante **carica** (evidenziato nell'immagine seguente) e caricare il file JSON dei parametri preparati.

      ![FarmBeats progetto](./media/install-azure-farmbeats/bash-2-1.png)

4. **Copiare** il comando seguente e **sostituire il nome utente \<** con il valore corretto in modo che il comando punti al percorso corretto del file caricato.

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. Eseguire il comando modificato per avviare il processo di installazione. Verrà richiesto di:
 - Accettare le condizioni di **licenza di Azure FarmBeats** . Se si accettano le Condizioni per l'utilizzo, immettere "Y" per procedere al passaggio successivo. Immettere "N" per terminare l'installazione, se non si accettano le condizioni per l'utilizzo.

 - Successivamente, verrà richiesto di immettere un token di accesso per l'installazione. Copiare il codice generato e accedere alla [pagina di accesso del dispositivo](https://microsoft.com/devicelogin) con le credenziali di **Azure**.

 - Una volta completato correttamente l'accesso, il programma di installazione richiederà la password dell'account Sentinel. Immettere la **password dell'account Sentinel**.

6. Il file dei parametri viene convalidato e viene avviata l'installazione delle risorse di Azure. Per completare l'installazione sono necessari circa **25 minuti** .    
> [!NOTE]
> Le sessioni di Cloud Shell inattive scadono dopo **20 minuti**. Tenere attiva la sessione di Cloud Shell durante la distribuzione delle risorse di Azure da parte del programma di installazione. Se si verifica il timeout della sessione, sarà necessario riavviare il processo di installazione.

Al termine dell'installazione, si otterranno i collegamenti di output seguenti:
* **URL di datahub**: collegamento di spavalderia per accedere alle API di datahub.
* **URL acceleratore**: l'applicazione Web per l'esplorazione dell'acceleratore FarmBeats di Azure.
* **File di log del programma di installazione**: il file di log che contiene i dettagli dell'installazione. Questo file di log può essere usato per la risoluzione dei problemi relativi all'installazione, se necessario.

È possibile verificare il completamento dell'installazione di Azure FarmBeats eseguendo i controlli seguenti:

**Datahub**
1. Accedere all'URL del tasto di scelta rapida fornito (nel formato **https://\<yourdatahub-website-name >. azurewebsites. NET/spavalderia**) con le credenziali di Azure.
2. Dovrebbe essere possibile visualizzare i diversi oggetti API di FarmBeats ed eseguire operazioni REST sulle API.

**Acceleratore**
1. Accedere all'URL del tasto di scelta rapida fornito (nel formato **https://\<youraccelerator-website-name >. azurewebsites. NET/spavalderia**) con le credenziali di Azure.
2. Dovrebbe essere possibile visualizzare l'interfaccia utente del tasto di scelta rapida con un'opzione per creare farm nel browser.

La possibilità di eseguire le operazioni precedenti indica una corretta installazione di Azure FarmBeats.

## <a name="upgrade"></a>Aggiornamento
Nella versione di anteprima pubblica, per aggiornare un'installazione esistente di Azure FarmBeats, è necessario eseguire di nuovo il comando di installazione in Cloud Shell, con un altro parametro "**updateIfExists**" nel file dei parametri impostato su "**true**". Vedere l'ultima riga dell'esempio JSON seguente per il parametro Update.

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
Il comando Aggiorna l'installazione esistente di Azure FarmBeats alla versione più recente e fornisce i collegamenti di output.

## <a name="uninstall"></a>Disinstallare

Per disinstallare Azure FarmBeats datahub o Accelerator, seguire questa procedura:

1.  Accedere al portale di Azure ed **eliminare i gruppi di risorse** in cui sono installati questi componenti.

2.  Passare a Azure Active Directory & **eliminare l'applicazione Azure ad** collegata all'installazione di Azure FarmBeats. L'installazione di Azure FarmBeats verrà rimossa dalla sottoscrizione di Azure.

## <a name="next-steps"></a>Passaggi successivi
Si è appreso come installare Azure FarmBeats nella sottoscrizione di Azure. A questo punto, informazioni su come [aggiungere utenti](manage-users-in-azure-farmbeats.md#manage-users) all'istanza di Azure FarmBeats.
