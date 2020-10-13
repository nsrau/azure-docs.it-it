---
title: Eseguire l'integrazione con App per la logica
titleSuffix: Azure Digital Twins
description: Vedere come connettere le app per la logica ai dispositivi gemelli digitali di Azure usando un connettore personalizzato
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: d1288f117a6b6c9fb05fd29578be35c676453177
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975162"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Eseguire l'integrazione con app per la logica usando un connettore personalizzato

[App](../logic-apps/logic-apps-overview.md) per la logica di Azure è un servizio cloud che consente di automatizzare i flussi di lavoro tra app e servizi. Connettendo app per la logica alle API dei dispositivi gemelli digitali di Azure, è possibile creare flussi automatizzati per i dispositivi gemelli digitali di Azure e i relativi dati.

I dispositivi gemelli digitali di Azure attualmente non dispongono di un connettore certificato (predefinito) per le app per la logica. Al contrario, il processo corrente per l'uso di app per la logica con i dispositivi gemelli digitali di Azure consiste nel creare un connettore di app per la [**logica personalizzato**](../logic-apps/custom-connector-overview.md), usando un [personalizzato di Azure gemelli di Azure personalizzato](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) modificato per lavorare con le app per la logica

> [!NOTE]
> Sono disponibili più versioni di spavalderia contenute nell'esempio di spavalderia personalizzato collegato sopra. La versione più recente sarà disponibile nella sottocartella con la data più recente, ma anche le versioni precedenti contenute nell'esempio sono ancora supportate.

In questo articolo si userà il [portale di Azure](https://portal.azure.com) per **creare un connettore personalizzato** che può essere usato per connettere le app per la logica a un'istanza di Azure Digital gemelli. Si creerà quindi **un'app** per la logica che usa questa connessione per uno scenario di esempio, in cui gli eventi attivati da un timer aggiorneranno automaticamente un gemello nell'istanza di Azure Digital gemelli. 

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, **creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** prima di iniziare.
Accedere al [portale di Azure](https://portal.azure.com) con questo account. 

È anche necessario completare gli elementi seguenti come parte dell'installazione dei prerequisiti. Nella parte restante di questa sezione vengono illustrati i passaggi seguenti:
- Configurare un'istanza di Gemelli digitali di Azure
- Ottenere il segreto client di registrazione dell'app
- Aggiungere un dispositivo gemello digitale

### <a name="set-up-azure-digital-twins-instance"></a>Configurare l'istanza di Gemelli digitali di Azure

Per connettere un'istanza di Azure Digital Twins alle app per la logica in questo articolo, è necessario che l' **istanza di Azure Digital Twins** sia già configurata. 

Prima di tutto, configurare un'istanza di Gemelli digitali di Azure e l'autenticazione necessaria per poterla usare. A tale scopo, seguire le istruzioni in [*Procedura: Configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md). A seconda dell'esperienza preferita, l'articolo relativo alla configurazione è disponibile per il [portale di Azure](how-to-set-up-instance-portal.md), l'[interfaccia della riga di comando](how-to-set-up-instance-cli.md) o un [esempio di script di distribuzione automatizzato di Cloud Shell](how-to-set-up-instance-scripted.md). Tutte le versioni delle istruzioni contengono anche le operazioni da eseguire per verificare che ogni passaggio sia stato completato correttamente e sia quindi possibile passare all'uso della nuova istanza.

In questa esercitazione sono necessari diversi valori da quando si configura l'istanza di. Se è necessario raccogliere nuovamente questi valori, usare i collegamenti seguenti alle sezioni corrispondenti nell'articolo relativo alla configurazione per individuarli nel [portale di Azure](https://portal.azure.com).
* **_Nome host_** dell'istanza di Gemelli digitali di Azure ([trova nel portale](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* **_ID applicazione (client)_** della registrazione app Azure AD ([trova nel portale](how-to-set-up-instance-portal.md#collect-important-values))
* **_ID directory (tenant)_** della registrazione app Azure AD ([trova nel portale](how-to-set-up-instance-portal.md#collect-important-values))

### <a name="get-app-registration-client-secret"></a>Ottenere il segreto client di registrazione dell'app

Sarà inoltre necessario creare un **_segreto client_** per la registrazione dell'app Azure ad. A tale scopo, passare alla pagina [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) nella portale di Azure (è possibile usare questo collegamento o cercarla nella barra di ricerca del portale). Selezionare la registrazione nell'elenco per aprirne i dettagli. 

Premere i *certificati e i segreti* dal menu della registrazione e selezionare *+ nuovo segreto client*.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Immettere i valori desiderati per la descrizione e la scadenza, quindi fare clic su *Aggiungi*.

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Verificare ora che il segreto client sia visibile nella pagina _certificati & segreti_ con i campi _scadenza_ e _valore_ . Prendere nota del _valore_ da usare in un secondo momento. è anche possibile copiarlo negli Appunti con l'icona di copia.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

### <a name="add-a-digital-twin"></a>Aggiungere un dispositivo gemello digitale

Questo articolo usa app per la logica per aggiornare un dispositivo gemello nell'istanza di Azure Digital gemelli. Per continuare, è necessario aggiungere almeno un gemello nell'istanza. 

È possibile aggiungere i dispositivi gemelli usando le [API di DigitalTwins](how-to-use-apis-sdks.md), [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md). Per i passaggi dettagliati su come creare i dispositivi gemelli usando questi metodi, vedere [*How-to: Manage Digital gemells*](how-to-manage-twin.md).

È necessario l' **_ID gemello_** di un gemello nell'istanza creata.

## <a name="create-custom-logic-apps-connector"></a>Creare un connettore di app per la logica personalizzato

In questo passaggio verrà creato un [connettore personalizzato](../logic-apps/custom-connector-overview.md) per le app per la logica per le API dei dispositivi gemelli digitali di Azure. Al termine di questa operazione, sarà possibile associare i dispositivi gemelli digitali di Azure durante la creazione di un'app per la logica nella sezione successiva.

Passare alla pagina del [connettore personalizzato app](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) per la logica nel portale di Azure (è possibile usare questo collegamento o cercarlo nella barra di ricerca del portale). Premere *+ Aggiungi*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Nella pagina *Crea connettore personalizzato app* per la logica che segue selezionare la sottoscrizione e il gruppo di risorse e un nome e un percorso di distribuzione per il nuovo connettore. Hit *Review + crea*. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Verrà visualizzata la scheda *Verifica + crea* , in cui è possibile fare clic su *Crea* nella parte inferiore per creare la risorsa.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Si passerà alla pagina di distribuzione per il connettore. Al termine della distribuzione, fare clic sul pulsante *Vai alla risorsa* per visualizzare i dettagli del connettore nel portale.

### <a name="configure-connector-for-azure-digital-twins"></a>Configurare il connettore per i dispositivi gemelli digitali di Azure

Successivamente, verrà configurato il connettore creato per raggiungere i dispositivi gemelli digitali di Azure.

Prima di tutto, scaricare un'app personalizzata dei gemelli digitali di Azure che è stata modificata per lavorare con le app per la logica. Scaricare l'esempio di **spavalderia personalizzato di Azure Digital Twins** da [**questo collegamento**](https://docs.microsoft.com/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) facendo clic sul pulsante *Scarica zip* . Passare alla cartella *Azure_Digital_Twins_Custom_Swaggers.zip* scaricata e decomprimerla. 

Il spavalderia personalizzato per questa esercitazione si trova nella cartella _**Azure_Digital_Twins_Custom_Swaggers \logicapps**_ . Questa cartella contiene sottocartelle denominate *stable* e *Preview*, entrambe che contengono versioni diverse della spavalderia organizzata per data. La cartella con la data più recente conterrà la copia più recente della spavalderia. Indipendentemente dalla versione selezionata, il file di spavalderia è denominato _**digitaltwins.json**_.

> [!NOTE]
> A meno che non si stia lavorando a una funzionalità di anteprima, è in genere consigliabile usare la versione *stabile* più recente di spavalderia. Sono comunque supportate anche le versioni precedenti e le versioni di anteprima della spavalderia. 

Passare quindi alla pagina Panoramica del connettore nel [portale di Azure](https://portal.azure.com) e premere *modifica*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client" per il colore.
    - Descrizione: specificare tutti i valori desiderati.
    - Schema: HTTPS (lasciare l'impostazione predefinita)
    - Host: *nome host* dell'istanza di Azure Digital gemelli.
    - URL di base:/(lasciare l'impostazione predefinita)

Quindi, fare clic sul pulsante *sicurezza* nella parte inferiore della finestra per continuare con il passaggio di configurazione successivo.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Nel passaggio sicurezza, fare clic su *modifica* e configurare queste informazioni:
* **Tipo di autenticazione**: OAuth 2,0
* **OAuth 2,0**:
    - Provider di identità: Azure Active Directory
    - ID client: l' *ID dell'applicazione (client)* per la registrazione dell'app Azure ad
    - Segreto client: il *segreto client* creato in [*prerequisiti*](#prerequisites) per la registrazione dell'app Azure ad
    - URL di accesso: https://login.windows.net (lasciare l'impostazione predefinita)
    - ID tenant: l' *ID di directory (tenant)* per la registrazione dell'app Azure ad
    - URL della risorsa: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Ambito: directory. AccessAsUser. All
    - URL di reindirizzamento: (lasciare l'impostazione predefinita per ora)

Si noti che il campo URL di reindirizzamento dice *Salva il connettore personalizzato per generare l'URL di reindirizzamento*. A tale scopo, premere *Aggiorna connettore* nella parte superiore del riquadro per confermare le impostazioni del connettore.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

<!-- Success message? didn't see one -->

Tornare al campo URL di reindirizzamento e copiare il valore che è stato generato. Verrà usato nel passaggio successivo.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Si tratta di tutte le informazioni necessarie per creare il connettore (non è necessario continuare la protezione per la fase di definizione). È possibile chiudere il riquadro *modifica app per la logica del connettore personalizzato* .

>[!NOTE]
>Tornare alla pagina Panoramica del connettore in cui è stata avviata l'operazione *Edit (modifica*). si noti che se si preme nuovamente *Edit (modifica* ) viene riavviato l'intero processo di immissione delle scelte I valori non vengono popolati dall'ultima volta in cui si è passati, quindi se si desidera salvare una configurazione aggiornata con i valori modificati, è necessario immettere nuovamente anche tutti gli altri valori per evitare che vengano sovrascritti dalle impostazioni predefinite.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Concedere le autorizzazioni del connettore nell'app Azure AD

Usare quindi il valore dell'URL di *Reindirizzamento* del connettore personalizzato copiato nell'ultimo passaggio per concedere le autorizzazioni del connettore nella registrazione dell'app Azure ad.

Passare alla pagina [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) nella portale di Azure e selezionare la registrazione nell'elenco. 

In *autenticazione* dal menu della registrazione aggiungere un URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client"::: 

Immettere l' *URL di reindirizzamento* del connettore personalizzato nel nuovo campo e fare clic sull'icona *Salva* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

A questo punto è stata eseguita la configurazione di un connettore personalizzato che può accedere alle API dei dispositivi gemelli digitali di Azure. 

## <a name="create-logic-app"></a>Creare l'app per la logica

Si creerà quindi un'app per la logica che userà il nuovo connettore per automatizzare gli aggiornamenti dei dispositivi gemelli digitali di Azure.

Nella [portale di Azure](https://portal.azure.com)cercare app per la *logica* nella barra di ricerca del portale. Se si seleziona questa finestra, viene visualizzata la pagina app per la *logica* . Premere il pulsante *Crea app* per la logica per creare una nuova app per la logica.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Nella pagina dell'app per la *logica* che segue immettere la sottoscrizione e il gruppo di risorse. Scegliere anche un nome per l'app per la logica e selezionare il percorso di distribuzione.

Premere il pulsante _Verifica + crea_ .

Verrà visualizzata la scheda *Verifica + crea* , in cui è possibile esaminare i dettagli e fare clic su *Crea* nella parte inferiore per creare la risorsa.

Si passerà alla pagina di distribuzione per l'app per la logica. Al termine della distribuzione, fare clic sul pulsante *Vai a risorsa* per passare alla *finestra di progettazione delle app*per la logica, in cui verrà compilata la logica del flusso di lavoro.

### <a name="design-workflow"></a>Flusso di lavoro di progettazione

In *progettazione app*per la logica, in *inizia con un trigger comune*Selezionare _**ricorrenza**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Nella pagina della *finestra di progettazione di app* per la logica che segue, modificare la frequenza di **ricorrenza** in *secondo*, in modo che l'evento venga attivato ogni 3 secondi. In questo modo sarà più semplice visualizzare i risultati in un secondo momento senza dover attendere molto tempo.

Premere *+ nuovo passaggio*.

Verrà visualizzata una casella *scegliere un'azione* . Passare alla scheda *personalizzata* . Il connettore personalizzato verrà visualizzato in precedenza nella casella superiore.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

Selezionarlo per visualizzare l'elenco delle API contenute in tale connettore. Usare la barra di ricerca o scorrere l'elenco per selezionare **DigitalTwins_Add**. Questa è l'API usata in questo articolo, ma è anche possibile selezionare qualsiasi altra API come scelta valida per una connessione di app per la logica.

È possibile che venga richiesto di accedere con le credenziali di Azure per connettersi al connettore. Se si riceve una finestra di dialogo per le *autorizzazioni richieste* , seguire le istruzioni per concedere il consenso per l'app e accettarle.

Nella casella nuovo *DigitalTwinsAdd* compilare i campi come indicato di seguito:
* _ID_: inserire l' *ID* del dispositivo gemello digitale nell'istanza che si vuole aggiornare all'app per la logica.
* _gemello_: questo campo consente di immettere il corpo richiesto dalla richiesta API scelta. Per *DigitalTwinsUpdate*, questo corpo è sotto forma di codice patch JSON. Per altre informazioni sulla strutturazione di una patch JSON per l'aggiornamento del dispositivo gemello, vedere la sezione relativa all' [aggiornamento di un dispositivo gemello digitale](how-to-manage-twin.md#update-a-digital-twin) di *How-to: Manage Digital gemells*.
* _API-Version_: versione più recente dell'API. Nell'anteprima pubblica corrente, questo valore è *2020-05-31-Preview*

Fare clic su *Salva* nella finestra di progettazione di app per la logica.

È possibile scegliere altre operazioni selezionando _+ nuovo passaggio_ nella stessa finestra.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Visualizzazione del portale di una registrazione dell'app Azure AD. Nel menu delle risorse è presente un'evidenziazione dei certificati e dei segreti e un'evidenziazione nella pagina relativa alla nuova chiave privata del client":::

## <a name="query-twin-to-see-the-update"></a>Query gemelle per visualizzare l'aggiornamento

Ora che l'app per la logica è stata creata, l'evento di aggiornamento del dispositivo gemello definito nella finestra di progettazione delle app per la logica dovrebbe ripetersi ogni tre secondi. Ciò significa che dopo tre secondi dovrebbe essere possibile eseguire una query sul dispositivo gemello e visualizzare i nuovi valori con patch.

È possibile eseguire una query sul dispositivo gemello tramite il metodo preferito, ad esempio un' [app client personalizzata](tutorial-command-line-app.md), l' [app di esempio Explorer di Azure Digital Twins](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), gli [SDK e le API](how-to-use-apis-sdks.md)o l' [interfaccia](how-to-use-cli.md)della riga di comando. 

Per altre informazioni sull'esecuzione di query sull'istanza di Azure Digital Twins, vedere [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata un'app per la logica che aggiorna regolarmente un gemello nell'istanza di Azure Digital Twins con una patch fornita. È possibile provare a selezionare altre API nel connettore personalizzato per creare app per la logica per un'ampia gamma di azioni nell'istanza.

Per altre informazioni sulle operazioni API disponibili e sui dettagli necessari, vedere [*procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md).