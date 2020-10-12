---
title: Configurare un'istanza e l'autenticazione (interfaccia della riga di comando)
titleSuffix: Azure Digital Twins
description: Vedere come configurare un'istanza del servizio dispositivi digitali gemelli di Azure usando l'interfaccia della riga di comando
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bb5ff0bf7347b87fdc3a103a03f9ff58279a367d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320771"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Configurare un'istanza e l'autenticazione di Azure Digital Twins (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Questo articolo illustra i passaggi per **configurare una nuova istanza di Azure Digital Twins**, inclusa la creazione dell'istanza e la configurazione dell'autenticazione. Al termine dell'articolo, si disporrà di un'istanza di gemelli digitali di Azure pronta per iniziare la programmazione.

Questa versione di questo articolo esegue manualmente questa procedura, una alla volta, usando l'interfaccia della riga di comando.
* Per eseguire questa procedura manualmente usando il portale di Azure, vedere la versione del portale di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (portale)*](how-to-set-up-instance-portal.md).
* Per eseguire un'installazione automatica usando uno script di distribuzione di esempio, vedere la versione con script di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (con script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Configurare una sessione di Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Creare l'istanza di Azure Digital Twins

In questa sezione si creerà **una nuova istanza di Azure Digital Twins** usando il comando cloud Shell. È necessario fornire:
* Un gruppo di risorse in cui distribuirlo. Se non si dispone già di un gruppo di risorse esistente, è possibile crearne uno ora con questo comando:
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Area per la distribuzione. Per visualizzare le aree che supportano i dispositivi gemelli digitali di Azure, visitare i [*prodotti Azure disponibili in base all'area*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Nome dell'istanza. Il nome della nuova istanza deve essere univoco all'interno dell'area per la sottoscrizione. Ciò significa che se la sottoscrizione ha un'altra istanza di Azure Digital gemelli nell'area che usa già il nome scelto, verrà chiesto di selezionare un nome diverso.

Usare questi valori nel comando seguente per creare l'istanza:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verificare l'esito positivo e raccogliere i valori importanti

Se l'istanza è stata creata correttamente, il risultato in Cloud Shell sarà simile al seguente, in modo da ottenere informazioni sulla risorsa creata:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="finestra di comando con la creazione corretta del gruppo di risorse e dell'istanza di Azure Digital Twins":::

Prendere nota dei nomi *host*, *nome*e *ResourceGroup* dell'istanza di Azure Digital Twins dall'output. Questi sono tutti i valori importanti che potrebbero essere necessari quando si continua a usare l'istanza di Azure Digital Twins, per configurare l'autenticazione e le risorse di Azure correlate. Se altri utenti eseguiranno la programmazione in base all'istanza, è necessario condividerli con questi valori.

> [!TIP]
> È possibile visualizzare queste proprietà, insieme a tutte le proprietà dell'istanza, in qualsiasi momento eseguendo `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso. Successivamente, si forniranno le autorizzazioni utente di Azure appropriate per gestirlo.

## <a name="set-up-user-access-permissions"></a>Configurare le autorizzazioni di accesso utente

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Usare il comando seguente per assegnare il ruolo (deve essere eseguito da un utente con [autorizzazioni sufficienti](#prerequisites-permission-requirements) nella sottoscrizione di Azure). Per il comando è necessario passare il *nome dell'entità utente* nell'account Azure ad per l'utente a cui deve essere assegnato il ruolo. Nella maggior parte dei casi, corrisponderà alla posta elettronica dell'utente nell'account Azure AD.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Il risultato di questo comando è l'output delle informazioni sull'assegnazione di ruolo creata.

> [!NOTE]
> Se questo comando restituisce un errore che informa che l'interfaccia della riga **di comando non è in grado di trovare l'utente o l'entità servizio nel database Graph**
>
> Assegnare il ruolo usando invece l' *ID oggetto* dell'utente. Questo problema può verificarsi per gli utenti di [account Microsoft personali (MSAS)](https://account.microsoft.com/account). 
>
> Utilizzare la [pagina portale di Azure di Azure Active Directory utenti](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) per selezionare l'account utente e aprirne i dettagli. Copiare il valore *ObjectID*dell'utente:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="finestra di comando con la creazione corretta del gruppo di risorse e dell'istanza di Azure Digital Twins" lightbox="media/includes/user-id.png":::
>
> Quindi, ripetere il comando elenco assegnazione ruolo usando l' *ID oggetto* dell'utente per il `assignee` parametro precedente.

### <a name="verify-success"></a>Verificare l'esito positivo

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso e sono state assegnate le autorizzazioni per gestirla. Successivamente, si imposteranno le autorizzazioni per l'accesso di un'app client.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurare le autorizzazioni di accesso per le applicazioni client

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Per creare una registrazione dell'app, è necessario fornire gli ID risorsa per le API di Azure Digital gemelli e le autorizzazioni di base per l'API.

Nella directory di lavoro creare un nuovo file e immettere il frammento di codice JSON seguente per configurare i dettagli: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Salvare il file come _**manifest.js**_.

> [!NOTE] 
> Ci sono alcune posizioni in cui è possibile usare una stringa leggibile "intuitiva" `https://digitaltwins.azure.net` per l'ID dell'app per le risorse di Azure Digital gemelli anziché il GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Molti esempi in questo set di documentazione, ad esempio, utilizzano l'autenticazione con la libreria MSAL e la stringa descrittiva può essere utilizzata. Tuttavia, durante questo passaggio della creazione della registrazione dell'app, il formato GUID dell'ID è necessario come illustrato in precedenza. 

Successivamente, il file verrà caricato nel Cloud Shell. Nella finestra di Cloud Shell fare clic sull'icona "Carica/Scarica file" e scegliere "carica".

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="finestra di comando con la creazione corretta del gruppo di risorse e dell'istanza di Azure Digital Twins":::
Passare almanifest.jsappena creato e fare clic * su* "Apri".

Eseguire quindi il comando seguente per creare una registrazione dell'app con un URL di risposta *client/nativo (mobile & desktop)* di `http://localhost` . Sostituire i segnaposto in base alle esigenze:

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Di seguito è riportato un estratto dell'output di questo comando, che mostra le informazioni sulla registrazione creata:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="finestra di comando con la creazione corretta del gruppo di risorse e dell'istanza di Azure Digital Twins":::

### <a name="verify-success"></a>Verificare l'esito positivo

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Verificare quindi che le impostazioni del *manifest.js* caricato siano state impostate correttamente per la registrazione. A tale scopo, selezionare *manifesto* dalla barra dei menu per visualizzare il codice manifesto della registrazione dell'app. Scorrere fino alla fine della finestra del codice e cercare i campi dal *manifest.jsin* `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Raccogli valori importanti

Selezionare quindi *Panoramica* dalla barra dei menu per visualizzare i dettagli della registrazione dell'app:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="finestra di comando con la creazione corretta del gruppo di risorse e dell'istanza di Azure Digital Twins":::

Prendere nota dell'ID dell' *applicazione (client)* e della *Directory (tenant)* visualizzati nella **pagina.** Questi valori saranno necessari in un secondo momento per [autenticare un'app client sulle API dei dispositivi gemelli digitali di Azure](how-to-authenticate-client.md). Se non si è la persona che scriverà il codice per tali applicazioni, sarà necessario condividere questi valori con la persona che lo sarà.

### <a name="other-possible-steps-for-your-organization"></a>Altri passaggi possibili per l'organizzazione

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Passaggi successivi

Testare le singole chiamate API REST nell'istanza usando i comandi dell'interfaccia della riga di comando di Azure Digital gemelli: 
* [riferimento AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
* [*Procedura: Usare l'interfaccia della riga di comando di Gemelli digitali di Azure*](how-to-use-cli.md)

In alternativa, vedere come connettere l'applicazione client all'istanza scrivendo il codice di autenticazione dell'app client:
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)