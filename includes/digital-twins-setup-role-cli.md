---
author: baanders
description: file di inclusione per il requisito del ruolo nell'installazione dei dispositivi gemelli di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832348"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Prerequisiti: requisiti di autorizzazione

Per poter completare tutti i passaggi descritti in questo articolo, è necessario essere classificati come un proprietario nella sottoscrizione di Azure. 

È possibile verificare il livello di autorizzazione eseguendo questo comando in Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> Se questo comando restituisce un errore che informa che l'interfaccia della riga **di comando non è in grado di trovare l'utente o l'entità servizio nel database Graph**
>
> Usare l' *ID oggetto* dell'utente anziché l'indirizzo di posta elettronica per la parte restante di questo articolo. Questo problema può verificarsi per gli utenti di [account Microsoft personali (MSAS)](https://account.microsoft.com/account). 
>
> Utilizzare la [pagina portale di Azure di Azure Active Directory utenti](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) per selezionare l'account utente e aprirne i dettagli. Copiare il valore *ObjectID*dell'utente:
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="Visualizzazione della pagina utente in portale di Azure evidenziando il GUID nel campo ' ID oggetto '" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> Quindi, ripetere il comando elenco assegnazione ruolo usando l' *ID oggetto* dell'utente al posto del messaggio di posta elettronica.

Dopo aver eseguito il comando elenco assegnazione ruolo, se si è proprietari, il `roleDefinitionName` valore nell'output è *proprietario*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Cloud Shell finestra che mostra l'output del comando AZ Role Assignment List":::

Se si rileva che il valore è *collaboratore* o non è *proprietario*, è possibile procedere in uno dei modi seguenti:
* Contattare il proprietario della sottoscrizione e richiedere al proprietario di completare i passaggi descritti in questo articolo per conto dell'utente
* Contattare il proprietario della sottoscrizione o un utente con il ruolo amministratore accesso utenti per la sottoscrizione e richiedere che venga innalzato al proprietario della sottoscrizione, in modo da avere le autorizzazioni necessarie per procedere manualmente. Il fatto che sia appropriato dipende dall'organizzazione e dal ruolo al suo interno.