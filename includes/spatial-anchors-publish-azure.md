---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179836"
---
### <a name="open-the-publish-wizard"></a>Aprire la pubblicazione guidata

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **SharingService** e scegliere **Pubblica**.

Viene avviata la pubblicazione guidata. Selezionare **Servizio app** > **Pubblica** per aprire la finestra di dialogo **Crea servizio app**.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Nella finestra di dialogo **Crea servizio app** fare clic su **Aggiungi un account** e accedere alla sottoscrizione di Azure. Se è già stato effettuato l'accesso, scegliere l'account da usare nell'elenco a discesa.

> [!NOTE]
> Se si è già connessi, non selezionare ancora l'opzione **Crea**.
>

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](resource-group.md)]

Accanto a **Gruppo di risorse** selezionare **Nuovo**.

Assegnare al gruppo di risorse il nome **myResourceGroup** e selezionare **OK**.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [app-service-plan](app-service-plan.md)]

Accanto a **Piano di hosting** selezionare **Nuovo**.

Nella finestra di dialogo **Configura piano di hosting** usare le impostazioni seguenti:

| Impostazione | Valore consigliato | DESCRIZIONE |
|-|-|-|
|Piano di servizio app| MySharingServicePlan | Nome del piano di servizio app. |
| Località | Stati Uniti occidentali | Data center in cui è ospitata l'app Web. |
| Dimensione | Gratuito | Il [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) che determina le funzionalità di hosting. |

Selezionare **OK**.

### <a name="create-and-publish-the-web-app"></a>Creare e pubblicare l'app Web

In **Nome app** immettere un nome univoco dell'app, usando i caratteri validi `a-z`, `0-9` e `-`, o accettare il nome univoco generato automaticamente. L'URL dell'app Web è `https://<app_name>.azurewebsites.net`, dove `<app_name>` è il nome dell'app.

Selezionare **Crea** per avviare la creazione delle risorse di Azure.

Al termine della procedura guidata, l'app Web ASP.NET Core viene pubblicata in Azure e aperta nel browser predefinito.

![App Web ASP.NET pubblicata in Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Il nome dell'app usato in questa sezione viene usato come prefisso dell'URL nel formato `https://<app_name>.azurewebsites.net`. Prendere nota di questo URL perché sarà necessario in seguito.
