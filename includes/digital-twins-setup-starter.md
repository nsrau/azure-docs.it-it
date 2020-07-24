---
author: baanders
description: file di inclusione per informazioni di avvio nell'installazione di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 3f19674c0e25ebf5cddc2dfb45580a15d04fad46
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098097"
---
>[!NOTE]
>Queste operazioni devono essere completate da un utente con un ruolo *proprietario* nella sottoscrizione di Azure. Anche se alcune parti possono essere completate senza questa autorizzazione elevata, sarà necessaria una collaborazione del proprietario per configurare completamente un'istanza utilizzabile. Per altre informazioni, vedere la sezione [*prerequisiti: autorizzazioni necessarie*](#prerequisites-permission-requirements) di seguito.

La configurazione completa per una nuova istanza di Azure Digital Twins è costituita da tre parti:
1. **Creazione dell'istanza**
2. **Impostazione delle autorizzazioni di accesso dell'utente**: l'utente di Azure deve avere il ruolo *proprietario (anteprima) di Azure Digital gemelli* nell'istanza per eseguire le attività di gestione. In questo passaggio si assegnerà manualmente questo ruolo (se si è un proprietario nella sottoscrizione di Azure) o si ottiene un proprietario della sottoscrizione per assegnarlo all'utente.
3. **Impostazione delle autorizzazioni di accesso per le applicazioni client**: è normale scrivere un'applicazione client da usare per interagire con l'istanza. Per consentire all'app client di accedere ai dispositivi gemelli digitali di Azure, è necessario configurare una *registrazione dell'app* in [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) che l'applicazione client utilizzerà per l'autenticazione all'istanza.

Per continuare, sarà necessaria una sottoscrizione di Azure. È possibile configurarne uno gratuitamente [qui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Prerequisiti: requisiti di autorizzazione

Per poter completare tutti i passaggi descritti in questo articolo, è necessario essere classificati come un proprietario nella sottoscrizione di Azure. 

È possibile verificare il livello di autorizzazione eseguendo questo comando in Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Se si è un proprietario, il `roleDefinitionName` valore nell'output è *owner*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="Cloud Shell finestra che mostra l'output del comando AZ Role Assignment List":::

Se si rileva che il valore è *collaboratore* o è diverso da *proprietario*, è possibile contattare il proprietario della sottoscrizione e procedere in uno dei modi seguenti:
* Richiedere al proprietario di completare i passaggi descritti in questo articolo per conto dell'utente
* Richiedere al proprietario di elevare l'utente al proprietario nella sottoscrizione, in modo da avere le autorizzazioni necessarie per continuare. Il fatto che sia appropriato dipende dall'organizzazione e dal ruolo al suo interno.
