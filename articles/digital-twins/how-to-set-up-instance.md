---
title: Creare un'istanza di Gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Vedere come configurare un'istanza del servizio dispositivi digitali gemelli di Azure.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4cac7a3f663d9ede966b8d6e5753c48629049dcd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057484"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Configurare un'istanza di Gemelli digitali di Azure

Questo articolo illustra i passaggi di base per configurare una nuova istanza di Azure Digital gemelli. Ciò include la creazione dell'istanza e l'assegnazione delle autorizzazioni [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) all'istanza per se stessi.

Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Configurare un'istanza di Gemelli digitali di Azure

Successivamente, verrà creato un nuovo gruppo di risorse di Azure da usare in questa procedura. Quindi, è possibile **creare una nuova istanza di Azure Digital gemelli** all'interno di tale gruppo di risorse. 

Sarà anche necessario specificare un nome per l'istanza e scegliere un'area per la distribuzione. Per visualizzare le aree che supportano i dispositivi gemelli digitali di Azure, visitare i [prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

>[!NOTE]
> Il nome della nuova istanza deve essere univoco all'interno dell'area (vale a dire che se un'altra istanza di Azure Digital gemelli in quell'area USA già il nome scelto, sarà necessario scegliere un nome diverso).

Creare il gruppo di risorse e l'istanza con i comandi seguenti:

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

Il risultato di questi comandi ha un aspetto simile al seguente, in modo da ottenere informazioni sulle risorse create:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="finestra di comando con la creazione corretta del gruppo di risorse e dell'istanza di Azure Digital Twins":::

Prendere nota dei nomi *host*, *nome*e *ResourceGroup* dell'istanza di Azure Digital Twins dall'output. Si tratta di valori di chiave che potrebbero essere necessari quando si continua a usare l'istanza di Azure Digital gemelli per configurare l'autenticazione e le risorse di Azure correlate.

> [!TIP]
> È possibile visualizzare queste proprietà, insieme a tutte le proprietà dell'istanza, in qualsiasi momento eseguendo `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Assegnare autorizzazioni Azure Active Directory

I dispositivi gemelli digitali di Azure usano [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) per il controllo degli accessi in base al ruolo (RBAC). Ciò significa che prima di poter effettuare chiamate del piano dati all'istanza di Azure Digital gemelli, è necessario prima assegnare a se stessi un ruolo con tali autorizzazioni.

Per usare i dispositivi gemelli digitali di Azure con un'applicazione client, è anche necessario assicurarsi che l'app client sia in grado di eseguire l'autenticazione con i dispositivi gemelli digitali di Azure. Questa operazione viene eseguita impostando una registrazione dell'app Azure Active Directory (AAD), che è possibile leggere in [procedura: autenticare un'applicazione client](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Assegnare un ruolo a se stessi

Creare un'assegnazione di ruolo per se stessi nell'istanza di Azure Digital Twins, usando la posta elettronica associata al tenant di AAD nella sottoscrizione di Azure. 

Per poter eseguire questa operazione, è necessario essere classificati come un proprietario nella sottoscrizione di Azure. Per verificarlo, è possibile eseguire il `az role assignment list --assignee <your-Azure-email>` comando e verificare che l'output del valore *RoleDefinitionName* sia *owner*. Se si rileva che il valore è *collaboratore* o è diverso da *proprietario*, contattare l'amministratore della sottoscrizione per concedere le autorizzazioni nella sottoscrizione per elevare il ruolo.

In qualità di proprietario della sottoscrizione, è possibile usare il comando seguente per assegnare l'utente a un ruolo proprietario per l'istanza di dispositivi gemelli digitali di Azure:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Il risultato di questo comando è costituito dalle informazioni sull'assegnazione di ruolo creata.

> [!TIP]
> Se invece si ottiene un errore *400: richiesta non valida* , eseguire il comando seguente per ottenere il valore *ObjectID* per l'utente:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Quindi, ripetere il comando di assegnazione di ruolo usando l' *ID oggetto* dell'utente al posto del messaggio di posta elettronica.

A questo punto si ha un'istanza di Azure Digital Twins pronta per l'uso.

## <a name="next-steps"></a>Passaggi successivi

Vedere come configurare e autenticare un'app client da usare con l'istanza:
* [Procedura: autenticare un'applicazione client](how-to-authenticate-client.md)
