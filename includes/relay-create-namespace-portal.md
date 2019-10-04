---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 3e3617f68d58e51f24affaae7d69812195b72fd0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210254"
---
1. Accedere al [portale di Azure][Azure portal].
1. Selezionare **Crea una risorsa**. Quindi, selezionare **Integrazione** > **Inoltro**. Se **Inoltro** non è visibile nell'elenco, selezionare **Visualizza tutto** nell'angolo superiore destro.
1. Selezionare **Crea**e immettere un nome per lo spazio dei nomi nel campo **nome** . Portale di Azure verifica se il nome è disponibile.
1. Scegliere una sottoscrizione di Azure in cui creare lo spazio dei nomi.
1. Per [gruppo di risorse](../articles/azure-resource-manager/manage-resource-groups-portal.md)scegliere un gruppo di risorse esistente in cui inserire lo spazio dei nomi oppure crearne uno nuovo.  
1. Selezionare il paese o la regione in cui deve essere ospitato lo spazio dei nomi.

    ![Crea spazio dei nomi][create-namespace]

1. Selezionare **Create**. Il portale di Azure crea lo spazio dei nomi e lo Abilita. Dopo alcuni minuti, il sistema effettua il provisioning delle risorse per l'account.

### <a name="get-management-credentials"></a>Ottenere le credenziali di gestione

1. Selezionare **tutte le risorse**, quindi scegliere il nome dello spazio dei nomi appena creato.
1. Selezionare **Criteri di accesso condiviso**.  
1. In **Criteri di accesso condiviso** selezionare **RootManageSharedAccessKey**.
1. In **criteri SAS: RootManageSharedAccessKey**, selezionare il pulsante **copia** accanto a **stringa di connessione primaria**. Questa azione consente di copiare la stringa di connessione negli Appunti per un uso successivo. Incollare questo valore nel Blocco note o in un'altra posizione temporanea.
1. Ripetere il passaggio precedente per copiare e incollare il valore di **Chiave primaria** in un percorso temporaneo per usarlo in seguito.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
