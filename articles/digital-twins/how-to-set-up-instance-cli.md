---
title: Configurare un'istanza e l'autenticazione (interfaccia della riga di comando)
titleSuffix: Azure Digital Twins
description: Vedere come configurare un'istanza del servizio dispositivi digitali gemelli di Azure usando l'interfaccia della riga di comando
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 26302fa67394e6c3122b159866c3814fb5677ba6
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494991"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Configurare un'istanza e l'autenticazione di Azure Digital Twins (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Questo articolo illustra i passaggi per **configurare una nuova istanza di Azure Digital Twins**, inclusa la creazione dell'istanza e la configurazione dell'autenticazione. Al termine dell'articolo, si disporrà di un'istanza di gemelli digitali di Azure pronta per iniziare la programmazione.

Questa versione di questo articolo esegue manualmente questa procedura, una alla volta, usando l'interfaccia della riga di comando.
* Per eseguire questa procedura manualmente usando il portale di Azure, vedere la versione del portale di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (portale)*](how-to-set-up-instance-portal.md).
* Per eseguire un'installazione automatica usando uno script di distribuzione di esempio, vedere la versione con script di questo articolo: [*procedura: configurare un'istanza e l'autenticazione (con script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Configurare una sessione di Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Creare l'istanza di Azure Digital Twins

In questa sezione si creerà **una nuova istanza di Azure Digital Twins** usando il comando cloud Shell. È necessario fornire:
* Un gruppo di risorse in cui distribuirlo. Se non si dispone già di un gruppo di risorse esistente, è possibile crearne uno ora con questo comando:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Area per la distribuzione. Per visualizzare le aree che supportano i dispositivi gemelli digitali di Azure, visitare i [*prodotti Azure disponibili in base all'area*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Nome dell'istanza. Il nome della nuova istanza deve essere univoco all'interno dell'area per la sottoscrizione. Ciò significa che se la sottoscrizione ha un'altra istanza di Azure Digital gemelli nell'area che usa già il nome scelto, verrà chiesto di selezionare un nome diverso.

Usare questi valori nel comando seguente per creare l'istanza:

```azurecli-interactive
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

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
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

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso e sono state assegnate le autorizzazioni per gestirla.

## <a name="next-steps"></a>Passaggi successivi

Testare le singole chiamate API REST nell'istanza usando i comandi dell'interfaccia della riga di comando di Azure Digital gemelli: 
* [riferimento AZ DT](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Procedura: Usare l'interfaccia della riga di comando di Gemelli digitali di Azure*](how-to-use-cli.md)

In alternativa, vedere come connettere un'applicazione client all'istanza con il codice di autenticazione:
* [*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)