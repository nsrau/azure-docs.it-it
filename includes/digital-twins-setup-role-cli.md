---
author: baanders
description: file di inclusione per il requisito del ruolo nell'installazione dei dispositivi gemelli di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407481"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Prerequisiti: requisiti di autorizzazione

Per poter completare tutti i passaggi descritti in questo articolo, è necessario essere classificati come un proprietario nella sottoscrizione di Azure. 

È possibile verificare il livello di autorizzazione eseguendo questo comando in Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Se si è un proprietario, il `roleDefinitionName` valore nell'output è *owner*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Cloud Shell finestra che mostra l'output del comando AZ Role Assignment List":::

Se si rileva che il valore è *collaboratore* o non è *proprietario*, è possibile procedere in uno dei modi seguenti:
* Contattare il proprietario della sottoscrizione e richiedere al proprietario di completare i passaggi descritti in questo articolo per conto dell'utente
* Contattare il proprietario della sottoscrizione o un utente con il ruolo amministratore accesso utenti per la sottoscrizione e richiedere che venga innalzato al proprietario della sottoscrizione, in modo da avere le autorizzazioni necessarie per procedere manualmente. Il fatto che sia appropriato dipende dall'organizzazione e dal ruolo al suo interno.