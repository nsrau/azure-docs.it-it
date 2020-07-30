---
title: Configurare un'istanza e l'autenticazione (con script)
titleSuffix: Azure Digital Twins
description: Vedere come configurare un'istanza del servizio dispositivi digitali gemelli di Azure eseguendo uno script di distribuzione automatica
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 076bde9e2760a862822d80d63197e2c15a678d35
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407488"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Configurare un'istanza di Azure Digital Twins e l'autenticazione (con script)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Questo articolo illustra i passaggi per **configurare una nuova istanza di Azure Digital Twins**, inclusa la creazione dell'istanza e la configurazione dell'autenticazione. Al termine dell'articolo, si disporrà di un'istanza di gemelli digitali di Azure pronta per iniziare la programmazione.

Questa versione di questo articolo completa questa procedura eseguendo un esempio di [ **script di distribuzione automatizzato** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) che semplifica il processo. 
* Per visualizzare i passaggi dell'interfaccia della riga di comando manuali eseguiti dallo script dietro le quinte, vedere la versione dell'interfaccia della riga di comando di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (CLI)*](how-to-set-up-instance-cli.md).
* Per visualizzare i passaggi manuali in base alla portale di Azure, vedere la versione del portale di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (portale)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-role-cli.md](../../includes/digital-twins-setup-role-cli.md)]

## <a name="run-the-deployment-script"></a>Eseguire lo script di distribuzione

Questo articolo usa un esempio di codice per i dispositivi gemelli digitali di Azure per distribuire un'istanza di Azure Digital Twins e l'autenticazione richiesta parzialmente automaticamente. Può anche essere usato come punto di partenza per la scrittura di interazioni con script.

Lo script di esempio è scritto in PowerShell. Fa parte degli esempi di [Azure Digital Twins](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/), che è possibile scaricare nel computer passando al collegamento di esempio e selezionando il pulsante *Scarica zip* sotto il titolo.

Nella cartella di esempio scaricata lo script di distribuzione si trova in _Azure_Digital_Twins_samples.zip script > > **deploy.ps1** _.

Ecco i passaggi per eseguire lo script di distribuzione in Cloud Shell.
1. Passare a una finestra [Azure cloud Shell](https://shell.azure.com/) nel browser. Accedere con questo comando:
    ```azurecli-interactive
    az login
    ```
    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure. In caso contrario, aprire una pagina del browser all'indirizzo *https://aka.ms/devicelogin* e immettere il codice di autorizzazione visualizzato nel terminale.
 
2. Dopo l'accesso, cercare la barra delle icone della finestra Cloud Shell. Selezionare l'icona "Carica/Scarica file" e scegliere "carica".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell finestra che mostra la selezione dell'opzione di caricamento":::

    Passare al file di _**deploy.ps1**_ nel computer e fare clic su "Apri". Il file verrà caricato nel Cloud Shell in modo che sia possibile eseguirlo nella finestra di Cloud Shell.

3. Eseguire lo script inviando il `./deploy.ps1` comando nella finestra cloud Shell. Quando lo script viene eseguito tramite la procedura di installazione automatica, verrà richiesto di passare i valori seguenti:
    * Per l'istanza: *ID sottoscrizione* della sottoscrizione di Azure da usare
    * Per l'istanza: un *percorso* in cui si vuole distribuire l'istanza. Per visualizzare le aree che supportano i dispositivi gemelli digitali di Azure, visitare i [*prodotti Azure disponibili in base all'area*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Per l'istanza: nome del *gruppo di risorse* . È possibile usare un gruppo di risorse esistente o immetterne uno nuovo da creare.
    * Per l'istanza: un *nome* per l'istanza di Azure Digital gemelli. Il nome della nuova istanza deve essere univoco all'interno dell'area per la sottoscrizione. Ciò significa che se la sottoscrizione ha un'altra istanza di Azure Digital gemelli nell'area che usa già il nome scelto, verrà chiesto di selezionare un nome diverso.
    * Per la registrazione dell'app: un *Azure ad nome visualizzato dell'applicazione* da associare alla registrazione. Questa registrazione dell'app è la posizione in cui vengono configurate le autorizzazioni di accesso alle [API di Azure Digital gemelli](how-to-use-apis-sdks.md) Successivamente, l'app client eseguirà l'autenticazione con la registrazione dell'app e, di conseguenza, le autorizzazioni di accesso configurate alle API.
    * Per la registrazione dell'app: *URL di risposta dell'applicazione Azure ad* per l'applicazione Azure ad. È possibile utilizzare `http://localhost` .

Lo script creerà un'istanza di Azure Digital Twins, assegna all'utente di Azure il ruolo *proprietario (anteprima) di Azure Digital gemelli (anteprima)* nell'istanza e configura una registrazione dell'app Azure ad per l'uso da parte dell'app client.

Di seguito è riportato un estratto del log di output dello script:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell finestra che mostra il log di input e output tramite l'esecuzione dello script di distribuzione" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Se lo script viene completato correttamente, la stampa finale dirà `Deployment completed successfully` . In caso contrario, risolvere il messaggio di errore ed eseguire nuovamente lo script. Ignorerà i passaggi già completati e inizierà a richiedere nuovamente l'input nel momento in cui è stato interrotto.

Al termine dello script, ora si ha un'istanza di Azure Digital Twins pronta per l'uso con le autorizzazioni per gestirla e sono state configurate le autorizzazioni per l'accesso di un'app client.

> [!NOTE]
> Lo script assegna attualmente il ruolo di gestione necessario nei dispositivi gemelli digitali di Azure (*proprietario di Azure Digital gemelli (anteprima)*) allo stesso utente che esegue lo script da cloud Shell. Se è necessario assegnare questo ruolo a un altro utente che gestirà l'istanza, è possibile farlo ora tramite il portale di Azure ([istruzioni](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) o l'interfaccia della riga di comando ([istruzioni](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

## <a name="collect-important-values"></a>Raccogli valori importanti

Sono disponibili diversi valori importanti dalle risorse configurate dallo script che potrebbero essere necessarie quando si continua a usare l'istanza di Azure Digital gemelli. In questa sezione si userà il [portale di Azure](https://portal.azure.com) per raccogliere questi valori. È consigliabile salvarli in un luogo sicuro oppure tornare a questa sezione per trovarli in un secondo momento, se necessario.

Se altri utenti eseguiranno la programmazione in base all'istanza, è necessario condividere anche questi valori.

### <a name="collect-instance-values"></a>Raccogli valori istanza

Nella [portale di Azure](https://portal.azure.com)trovare l'istanza di Azure Digital gemelli cercando il nome dell'istanza nella barra di ricerca del portale.

Se la si seleziona, verrà visualizzata la pagina *Panoramica* dell'istanza. Annotare il *nome*, il *gruppo di risorse*e il *nome host*. Potrebbero essere necessari in seguito per identificare e connettersi all'istanza di.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Evidenziazione dei valori importanti dalla pagina Panoramica dell'istanza":::

### <a name="collect-app-registration-values"></a>Raccogliere i valori di registrazione delle app 

Esistono due valori importanti della registrazione dell'app che saranno necessari in seguito per [scrivere il codice di autenticazione dell'app client per le API dei dispositivi gemelli digitali di Azure](how-to-authenticate-client.md). 

Per trovarli, seguire [questo collegamento](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) per passare alla pagina di panoramica della registrazione dell'app Azure AD nell'portale di Azure. Questa pagina Mostra tutte le registrazioni dell'app che sono state create nella sottoscrizione.

Verrà visualizzata la registrazione dell'app appena creata in questo elenco. Selezionarlo per aprirne i dettagli:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Visualizzazione del portale dei valori importanti per la registrazione dell'app":::

Prendere nota dell'ID dell' *applicazione (client)* e della *Directory (tenant)* visualizzati nella **pagina.** Se non si è la persona che scriverà il codice per le applicazioni client, sarà necessario condividere questi valori con la persona che sarà.

## <a name="verify-success"></a>Verificare l'esito positivo

Se si desidera verificare la creazione delle risorse e delle autorizzazioni impostate dallo script, è possibile esaminarle nella [portale di Azure](https://portal.azure.com).

### <a name="verify-instance"></a>Verifica istanza

Per verificare che l'istanza sia stata creata, passare alla [pagina dei dispositivi gemelli di Azure Digital](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) nella portale di Azure. Questa pagina elenca tutte le istanze di dispositivi gemelli digitali di Azure. Cercare il nome dell'istanza appena creata nell'elenco.

### <a name="verify-user-role-assignment"></a>Verificare l'assegnazione del ruolo utente

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Tenere presente che lo script assegna attualmente questo ruolo necessario allo stesso utente che esegue lo script da Cloud Shell. Se è necessario assegnare questo ruolo a un altro utente che gestirà l'istanza, è possibile farlo ora tramite il portale di Azure ([istruzioni](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) o l'interfaccia della riga di comando ([istruzioni](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

### <a name="verify-app-registration"></a>Verificare la registrazione dell'app

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Prima di tutto, verificare che le impostazioni delle autorizzazioni di Azure Digital Twins siano state impostate correttamente nella registrazione. A tale scopo, selezionare *manifesto* dalla barra dei menu per visualizzare il codice manifesto della registrazione dell'app. Scorrere fino alla fine della finestra del codice e cercare questi campi sotto `requiredResourceAccess` . I valori devono corrispondere a quelli dello screenshot seguente:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>Altri passaggi possibili per l'organizzazione

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere come connettere l'applicazione client all'istanza scrivendo il codice di autenticazione dell'app client:
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)
