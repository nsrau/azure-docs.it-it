---
title: Configurare un'istanza e l'autenticazione (metodo manuale)
titleSuffix: Azure Digital Twins
description: Vedere come configurare un'istanza del servizio dispositivi digitali gemelli di Azure, inclusa l'autenticazione corretta. Versione manuale.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d2d5ce0bc988badc6f25726206a953d87de7eaa2
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371457"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-manual"></a>Configurare un'istanza di dispositivi gemelli digitali di Azure e l'autenticazione (manuale)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Questo articolo illustra i passaggi per **configurare una nuova istanza di Azure Digital Twins**, inclusa la creazione dell'istanza e la configurazione dell'autenticazione. Al termine dell'articolo, si disporrà di un'istanza di gemelli digitali di Azure pronta per iniziare la programmazione.

Questa versione di questo articolo descrive manualmente questa procedura, una alla volta. Per eseguire un'installazione automatica usando uno script di distribuzione di esempio, vedere la versione con script di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (con script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

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

### <a name="verify-success"></a>Verificare l'esito positivo

Se l'istanza è stata creata correttamente, il risultato in Cloud Shell sarà simile al seguente, in modo da ottenere informazioni sulla risorsa creata:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="finestra di comando con la creazione corretta del gruppo di risorse e dell'istanza di Azure Digital Twins":::

Prendere nota dei nomi *host*, *nome*e *ResourceGroup* dell'istanza di Azure Digital Twins dall'output. Questi sono tutti i valori importanti che potrebbero essere necessari quando si continua a usare l'istanza di Azure Digital Twins, per configurare l'autenticazione e le risorse di Azure correlate.

> [!TIP]
> È possibile visualizzare queste proprietà, insieme a tutte le proprietà dell'istanza, in qualsiasi momento eseguendo `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso. Successivamente, si forniranno le autorizzazioni utente di Azure appropriate per gestirlo.

## <a name="set-up-your-users-access-permissions"></a>Configurare le autorizzazioni di accesso dell'utente

I dispositivi gemelli digitali di Azure usano [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) per il controllo degli accessi in base al ruolo (RBAC). Ciò significa che prima che un utente possa effettuare chiamate del piano dati all'istanza di Azure Digital Twins, a tale utente deve essere assegnato un ruolo con le autorizzazioni necessarie.

Per i dispositivi gemelli digitali di Azure, questo ruolo è proprietario di dispositivi _**gemelli digitali di Azure (anteprima)**_. Per altre informazioni sui ruoli e sulla sicurezza, vedere Concetti relativi alla [*sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](concepts-security.md).

Questa sezione illustra come creare un'assegnazione di ruolo per un utente nell'istanza di Azure Digital Twins, tramite la posta elettronica associata al tenant di Azure AD nella sottoscrizione di Azure. A seconda del ruolo e delle autorizzazioni per la sottoscrizione di Azure, questa impostazione viene configurata autonomamente o configurata per conto di un altro utente che gestirà l'istanza di dispositivi gemelli digitali di Azure.

### <a name="assign-the-role"></a>Assegnare il ruolo

Per concedere a un utente le autorizzazioni per la gestione di un'istanza di dispositivi gemelli digitali di Azure, è necessario assegnare loro il ruolo _**proprietario (anteprima) di Azure Digital gemelli**_ nell'istanza. 

> [!NOTE]
> Si noti che questo ruolo è diverso dal ruolo *proprietario* Azure ad, che può essere assegnato anche nell'ambito dell'istanza di Azure Digital gemelli. Si tratta di due ruoli di gestione distinti e Azure AD *proprietario* non concede l'accesso alle funzionalità del piano dati concesse con il *proprietario di Azure Digital gemelli (anteprima)*.

Usare il comando seguente per assegnare il ruolo (deve essere eseguito da un proprietario della sottoscrizione di Azure):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Il risultato di questo comando è l'output delle informazioni sull'assegnazione di ruolo creata.

> [!TIP]
> Se invece si ottiene un errore *400: richiesta non valida* , eseguire il comando seguente per ottenere il valore *ObjectID* per l'utente:
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> Quindi, ripetere il comando di assegnazione di ruolo usando l' *ID oggetto* dell'utente al posto del messaggio di posta elettronica.

### <a name="verify-success"></a>Verificare l'esito positivo

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso e sono state assegnate le autorizzazioni per gestirla. Successivamente, si imposteranno le autorizzazioni per l'accesso di un'app client.

## <a name="set-up-access-permissions-for-client-applications"></a>Configurare le autorizzazioni di accesso per le applicazioni client

Quando si configura un'istanza di Azure Digital Twins, è comune interagire con tale istanza tramite un'applicazione client creata dall'utente. A tale scopo, è necessario assicurarsi che l'app client sia in grado di eseguire l'autenticazione con i dispositivi gemelli digitali di Azure. Questa operazione viene eseguita impostando una **registrazione dell'app** [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) per l'app client da usare.

Questa registrazione dell'app è la posizione in cui vengono configurate le autorizzazioni di accesso alle [API di Azure Digital gemelli](how-to-use-apis-sdks.md) Successivamente, l'app client eseguirà l'autenticazione con la registrazione dell'app e, di conseguenza, le autorizzazioni di accesso configurate alle API.

>[!TIP]
> In qualità di proprietario della sottoscrizione, è preferibile impostare una nuova registrazione dell'app per ogni nuova istanza di Azure Digital gemelli *oppure* eseguire questa operazione una sola volta e stabilire una singola registrazione dell'app che verrà condivisa tra tutte le istanze dei dispositivi gemelli digitali di Azure nella sottoscrizione.

### <a name="create-the-registration"></a>Creare la registrazione

Per creare una registrazione dell'app, è necessario fornire gli ID risorsa per le API di Azure Digital gemelli e le autorizzazioni di base per l'API. Nella directory di lavoro aprire un nuovo file e immettere il frammento di codice JSON seguente per configurare i dettagli: 

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

Salvare il file come *manifest.js*.

> [!NOTE] 
> Ci sono alcune posizioni in cui è possibile usare una stringa leggibile "intuitiva" `https://digitaltwins.azure.net` per l'ID dell'app per le risorse di Azure Digital gemelli anziché il GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Molti esempi in questo set di documentazione, ad esempio, utilizzano l'autenticazione con la libreria MSAL e la stringa descrittiva può essere utilizzata. Tuttavia, durante questo passaggio della creazione della registrazione dell'app, il formato GUID dell'ID è necessario come illustrato in precedenza. 

Nella finestra di Cloud Shell fare clic sull'icona "Carica/Scarica file" e scegliere "carica".

:::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="Cloud Shell finestra che mostra la selezione dell'opzione di caricamento":::
Passare almanifest.jsappena creato e fare clic *su* "Apri".

Eseguire quindi il comando seguente per creare una registrazione dell'app (sostituendo i segnaposto in base alle esigenze):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Di seguito è riportato un estratto dell'output di questo comando, che mostra le informazioni sulla registrazione creata:

:::image type="content" source="media/how-to-set-up-instance/new-app-registration.png" alt-text="Cloud Shell l'output della nuova registrazione dell'app Azure AD":::

### <a name="verify-success"></a>Verificare l'esito positivo

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Per prima cosa, verificare che le impostazioni del *manifest.js* caricato siano state impostate correttamente nella registrazione. A tale scopo, selezionare *manifesto* dalla barra dei menu per visualizzare il codice manifesto della registrazione dell'app. Scorrere fino alla fine della finestra del codice e cercare i campi dal *manifest.jsin* `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Raccogli valori importanti

Selezionare quindi *Panoramica* dalla barra dei menu per visualizzare i dettagli della registrazione dell'app:

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="Visualizzazione del portale dei valori importanti per la registrazione dell'app":::

Prendere nota dell'ID dell' *applicazione (client)* e della *Directory (tenant)* visualizzati nella **pagina.** Questi valori saranno necessari in un secondo momento per [autenticare un'app client sulle API dei dispositivi gemelli digitali di Azure](how-to-authenticate-client.md). Se non si è la persona che scriverà il codice per tali applicazioni, sarà necessario condividere questi valori con la persona che lo sarà.

### <a name="other-possible-steps-for-your-organization"></a>Altri passaggi possibili per l'organizzazione

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Passaggi successivi

Vedere come connettere l'applicazione client all'istanza scrivendo il codice di autenticazione dell'app client:
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)
