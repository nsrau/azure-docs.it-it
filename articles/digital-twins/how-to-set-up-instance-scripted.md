---
title: Configurare un'istanza e l'autenticazione (con script)
titleSuffix: Azure Digital Twins
description: Vedere come configurare un'istanza del servizio dispositivi digitali gemelli di Azure eseguendo uno script di distribuzione automatica
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5806ea094abd3431cd7e22064c6acd8ad150726a
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495011"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Configurare un'istanza di Azure Digital Twins e l'autenticazione (con script)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Questo articolo illustra i passaggi per **configurare una nuova istanza di Azure Digital Twins**, inclusa la creazione dell'istanza e la configurazione dell'autenticazione. Al termine dell'articolo, si disporrà di un'istanza di gemelli digitali di Azure pronta per iniziare la programmazione.

Questa versione di questo articolo completa questa procedura eseguendo un esempio di [ **script di distribuzione automatizzato** ](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) che semplifica il processo. 
* Per visualizzare i passaggi dell'interfaccia della riga di comando manuali eseguiti dallo script dietro le quinte, vedere la versione dell'interfaccia della riga di comando di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (CLI)*](how-to-set-up-instance-cli.md).
* Per visualizzare i passaggi manuali in base alla portale di Azure, vedere la versione del portale di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (portale)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Prerequisiti: scaricare lo script

Lo script di esempio è scritto in PowerShell. Fa parte degli [**esempi end-to-end di Azure Digital gemelli**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), che è possibile scaricare nel computer passando al collegamento di esempio e selezionando il pulsante *Scarica zip* sotto il titolo.

Il progetto di esempio verrà scaricato nel computer come _**Azure_Digital_Twins_end_to_end_samples.zip**_. Passare alla cartella del computer e decomprimerla per estrarre i file.

Nella cartella decompressa lo script di distribuzione si trova in _Azure_Digital_Twins_end_to_end_samples script > > **deploy.ps1** _.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Eseguire lo script di distribuzione

Questo articolo usa un esempio di codice per i dispositivi gemelli digitali di Azure per distribuire un'istanza di Azure Digital Twins e l'autenticazione richiesta parzialmente automaticamente. Può anche essere usato come punto di partenza per la scrittura di interazioni con script.

Ecco i passaggi per eseguire lo script di distribuzione in Cloud Shell.
1. Passare a una finestra [Azure cloud Shell](https://shell.azure.com/) nel browser. Accedere con questo comando:
    ```azurecli-interactive
    az login
    ```
    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure. In caso contrario, aprire una pagina del browser all'indirizzo *https://aka.ms/devicelogin* e immettere il codice di autorizzazione visualizzato nel terminale.
 
2. Nella barra delle icone Cloud Shell verificare che il Cloud Shell sia impostato in modo da eseguire la versione di PowerShell.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Cloud Shell finestra che mostra la selezione della versione di PowerShell&quot;:::

1. Selezionare l'icona &quot;Carica/Scarica file" e scegliere "carica".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Cloud Shell finestra che mostra la selezione della versione di PowerShell&quot;:::

1. Selezionare l'icona &quot;Carica/Scarica file":::

    Passare al file di _**deploy.ps1**_ nel computer (in _Azure_Digital_Twins_end_to_end_samples > script > **deploy.ps1** _) e premere "Apri". Il file verrà caricato nel Cloud Shell in modo che sia possibile eseguirlo nella finestra di Cloud Shell.

4. Eseguire lo script inviando il `./deploy.ps1` comando nella finestra cloud Shell. È possibile copiare il comando seguente (ricordare che per incollare in Cloud Shell, è possibile usare **Ctrl + Maiusc + v** in Windows e Linux o **cmd + Maiusc + v** in MacOS. È inoltre possibile utilizzare il menu di scelta rapida.

    ```azurecli-interactive
    ./deploy.ps1
    ```

    Lo script creerà un'istanza di Azure Digital Twins e assegnerà all'utente di Azure il ruolo di *proprietario dei dati di Azure Digital gemelli* nell'istanza.

    Quando lo script viene eseguito tramite la procedura di installazione automatica, verrà richiesto di passare i valori seguenti:
    * Per l'istanza: *ID sottoscrizione* della sottoscrizione di Azure da usare
    * Per l'istanza: un *percorso* in cui si vuole distribuire l'istanza. Per visualizzare le aree che supportano i dispositivi gemelli digitali di Azure, visitare i [*prodotti Azure disponibili in base all'area*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Per l'istanza: nome del *gruppo di risorse* . È possibile usare un gruppo di risorse esistente o immetterne uno nuovo da creare.
    * Per l'istanza: un *nome* per l'istanza di Azure Digital gemelli. Il nome della nuova istanza deve essere univoco all'interno dell'area per la sottoscrizione. Ciò significa che se la sottoscrizione ha un'altra istanza di Azure Digital gemelli nell'area che usa già il nome scelto, verrà chiesto di selezionare un nome diverso.

Di seguito è riportato un estratto del log di output dello script:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Cloud Shell finestra che mostra la selezione della versione di PowerShell&quot;:::

1. Selezionare l'icona &quot;Carica/Scarica file" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Se lo script viene completato correttamente, la stampa finale dirà `Deployment completed successfully` . In caso contrario, risolvere il messaggio di errore ed eseguire nuovamente lo script. Ignorerà i passaggi già completati e inizierà a richiedere nuovamente l'input nel momento in cui è stato interrotto.

> [!NOTE]
> Lo script assegna attualmente il ruolo di gestione necessario nei dispositivi gemelli digitali di Azure (*proprietario dei dati di Azure Digital gemelli*) allo stesso utente che esegue lo script da cloud Shell. Se è necessario assegnare questo ruolo a un altro utente che gestirà l'istanza, è possibile farlo ora tramite il portale di Azure ([istruzioni](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) o l'interfaccia della riga di comando ([istruzioni](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

>[!NOTE]
>Attualmente esiste un **problema noto** con la configurazione con script, in cui alcuni utenti (in particolare gli utenti degli [account Microsoft personali (MSAS)](https://account.microsoft.com/account)) potrebbero trovare l' **assegnazione di ruolo al _proprietario dei dati di Azure Digital gemelli_ non è stato creato**.
>
>È possibile verificare l'assegnazione di ruolo con la sezione [*verificare l'assegnazione del ruolo utente*](#verify-user-role-assignment) più avanti in questo articolo e, se necessario, configurare manualmente l'assegnazione di ruolo usando il [portale di Azure](how-to-set-up-instance-portal.md#set-up-user-access-permissions) o l' [interfaccia](how-to-set-up-instance-cli.md#set-up-user-access-permissions)della riga di comando.
>
>Per informazioni dettagliate su questo problema, vedere [*risoluzione dei problemi: problemi noti nei dispositivi gemelli digitali di Azure*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

## <a name="verify-success-and-collect-important-values"></a>Verificare l'esito positivo e raccogliere i valori importanti

Per verificare la creazione delle risorse e delle autorizzazioni impostate dallo script, è possibile esaminarle nel [portale di Azure](https://portal.azure.com) con le istruzioni riportate di seguito. Se non si riesce a verificare l'esito positivo di un passaggio, ripetere il passaggio. È possibile eseguire i passaggi singolarmente usando le istruzioni [portale di Azure](how-to-set-up-instance-portal.md) o [CLI](how-to-set-up-instance-cli.md) .

Questa sezione illustra anche come trovare i valori importanti dalle risorse configurate dallo script che può essere necessario quando si continua a usare l'istanza di Azure Digital gemelli. È necessario salvare questi valori in un luogo sicuro oppure tornare a questa sezione per trovarli in un secondo momento, se necessario. Se altri utenti eseguiranno la programmazione in base all'istanza, è necessario condividere anche questi valori.

### <a name="verify-instance"></a>Verifica istanza

Per verificare che l'istanza sia stata creata, passare alla [pagina dei dispositivi gemelli di Azure Digital](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) nella portale di Azure. Per ottenere questa pagina, è possibile cercare i dispositivi *gemelli digitali di Azure* nella barra di ricerca del portale.

Questa pagina elenca tutte le istanze di dispositivi gemelli digitali di Azure. Cercare il nome dell'istanza appena creata nell'elenco.

Se la verifica ha esito negativo, è possibile riprovare a creare un'istanza usando il [portale](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) o l' [interfaccia](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)della riga di comando.

### <a name="collect-instance-values"></a>Raccogli valori istanza

Selezionare il nome dell'istanza dalla pagina di [Azure Digital Twins](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) per aprire la pagina *Panoramica* dell'istanza. Annotare il *nome*, il *gruppo di risorse*e il *nome host*. Potrebbero essere necessari in seguito per identificare e connettersi all'istanza di.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Cloud Shell finestra che mostra la selezione della versione di PowerShell&quot;:::

1. Selezionare l'icona &quot;Carica/Scarica file":::

### <a name="verify-user-role-assignment"></a>Verificare l'assegnazione del ruolo utente

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Tenere presente che lo script assegna attualmente questo ruolo necessario allo stesso utente che esegue lo script da Cloud Shell. Se è necessario assegnare questo ruolo a un altro utente che gestirà l'istanza, è possibile farlo ora tramite il portale di Azure ([istruzioni](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) o l'interfaccia della riga di comando ([istruzioni](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

Se la verifica ha avuto esito negativo, è anche possibile ripetere la propria assegnazione di ruolo usando il [portale](how-to-set-up-instance-portal.md#set-up-user-access-permissions) o l' [interfaccia](how-to-set-up-instance-cli.md#set-up-user-access-permissions)della riga di comando.

## <a name="next-steps"></a>Passaggi successivi

Testare le singole chiamate API REST nell'istanza usando i comandi dell'interfaccia della riga di comando di Azure Digital gemelli: 
* [riferimento AZ DT](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Procedura: Usare l'interfaccia della riga di comando di Gemelli digitali di Azure*](how-to-use-cli.md)

In alternativa, vedere come connettere un'applicazione client all'istanza con il codice di autenticazione:
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)