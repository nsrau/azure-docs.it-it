---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 6e0175173f17ae0958522517360b94ee80f3b2f9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148977"
---
## <a name="prerequisites"></a>Prerequisiti

### <a name="get-sample-code"></a>Ottenere il codice di esempio

Questa esercitazione presuppone che siano già state completate le procedure dell'[esercitazione precedente](../articles/container-registry/container-registry-tutorial-quick-task.md) e che siano state eseguite la copia tramite fork e la clonazione del repository di esempio. Se non è già stato fatto, prima di procedere eseguire i passaggi descritti nella sezione [Prerequisiti](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) dell'esercitazione precedente.

### <a name="container-registry"></a>Registro contenitori

Per completare questa esercitazione è necessario che la sottoscrizione di Azure includa un registro contenitori di Azure. Se occorre un registro, vedere l'[esercitazione precedente](../articles/container-registry/container-registry-tutorial-quick-task.md), o la [Guida introduttiva: Create a Registro Container using the Azure CLI](../articles/container-registry/container-registry-get-started-azure-cli.md) (Creare un registro contenitori con l'interfaccia della riga di comando di Azure).

## <a name="create-a-github-personal-access-token"></a>Creare un token di accesso personale GitHub

Per attivare una compilazione in caso di commit in un repository Git, Attività del Registro Azure Container richiede un token di accesso personale per accedere al repository. Se non si ha già un token di accesso personale, completare questi passaggi per generarne uno in GitHub:

1. Passare alla pagina per la creazione di token di accesso personali in GitHub all'indirizzo https://github.com/settings/tokens/new
1. Immettere una breve **descrizione** del token, ad esempio "ACR Tasks Demo".
1. In **repo** abilitare **repo:status** e **public_repo**.

   ![Screenshot della pagina per la generazione di token di accesso personali in GitHub][build-task-01-new-token]

1. Selezionare il pulsante **Generate token** (Genera token). Potrebbe essere richiesto di confermare la password.
1. Copiare e salvare il token generato in una **posizione sicura**. Questo token verrà usato per definire un'attività nella sezione seguente.

   ![Screenshot del token di accesso personale generato in GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
