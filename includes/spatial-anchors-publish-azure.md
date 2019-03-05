---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963311"
---
### <a name="launch-the-publish-wizard"></a>Avviare la procedura guidata di pubblicazione

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **SharingService** e scegliere **Pubblica**.

La procedura guidata di pubblicazione viene avviata automaticamente. Selezionare **Servizio app** > **Pubblica** per aprire la finestra di dialogo **Crea servizio app**.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Nella finestra di dialogo **Crea servizio App** fare clic su **Aggiungi un account** e accedere alla sottoscrizione di Azure. Se è già stato effettuato l'accesso, selezionare l'account da usare dall'elenco a discesa.

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

Nella finestra di dialogo **Configura piano di hosting** usare le impostazioni della tabella.

| Impostazione | Valore consigliato | DESCRIZIONE |
|-|-|-|
|Piano di servizio app| MySharingServicePlan | Nome del piano di servizio app. |
| Località | Stati Uniti occidentali | Data center in cui è ospitata l'app Web. |
| Dimensione | Gratuito | [Piano tariffario](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) che determina le funzionalità di hosting. |

Selezionare **OK**.

### <a name="create-and-publish-the-web-app"></a>Creare e pubblicare l'app Web

In **Nome app** immettere un nome univoco dell'app, usando i caratteri validi `a-z`, `0-9` e `-`, o accettare il nome univoco generato automaticamente. L'URL dell'app Web è `https://<app_name>.azurewebsites.net`, dove `<app_name>` è il nome dell'app.

Selezionare **Crea** per avviare la creazione delle risorse di Azure.

Al termine della procedura guidata, l'app Web ASP.NET Core viene pubblicata in Azure e avviata nel browser predefinito.

![App Web ASP.NET pubblicata in Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Il nome dell'app specificato nel passaggio relativo alla [creazione e pubblicazione](#create-and-publish-the-web-app) viene usato come prefisso dell'URL nel formato `https://<app_name>.azurewebsites.net`. Prendere nota di questo URL perché sarà necessario usarlo in seguito.
