---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0e6f68daf22659b880827ad027268d9757c60063
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971336"
---
### <a name="open-the-publish-wizard"></a>Aprire la pubblicazione guidata

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **SharingService** e quindi scegliere **Pubblica**.

Viene avviata la pubblicazione guidata. 

Selezionare **Servizio app** > **Pubblica** per aprire il riquadro **Crea servizio app**.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure.

Nel riquadro **Crea servizio App** selezionare **Aggiungi un account** e quindi accedere alla sottoscrizione di Azure. Se è già stato effettuato l'accesso, scegliere l'account da usare nell'elenco a discesa.

   > [!NOTE]
   > Se si è già connessi, non selezionare ancora l'opzione **Crea**.
   >

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](resource-group.md)]

Accanto a **Gruppo di risorse** selezionare **Nuovo**.

Assegnare al gruppo di risorse il nome **myResourceGroup** e quindi selezionare **OK**.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [app-service-plan](app-service-plan.md)]

Accanto a **Piano di hosting** selezionare **Nuovo**.

Nel riquadro **Configura piano di hosting** usare queste impostazioni:

| Impostazione | Valore consigliato | Descrizione |
|-|-|-|
|Piano di servizio app| MySharingServicePlan | Nome del piano di servizio app |
| Location | Stati Uniti occidentali | Il data center in cui viene ospitata l'app Web |
| Dimensione | Gratuito | Il [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) che determina le funzionalità di hosting |

Selezionare **OK**.

### <a name="create-and-publish-the-web-app"></a>Creare e pubblicare l'app Web

In **Nome dell'app** immettere un nome di app univoco. I caratteri validi sono a-z, 0-9 e i trattini (-). In alternativa, accettare il nome univoco generato automaticamente. L'URL dell'app Web è `https://<app_name>.azurewebsites.net`, dove `<app_name>` è il nome dell'app.

Selezionare **Crea** per avviare la creazione delle risorse di Azure.

   Al termine della procedura guidata, l'app Web ASP.NET Core viene pubblicata in Azure e aperta nel browser predefinito.

  ![Screenshot di un'app Web ASP.NET pubblicata in Azure.](./media/spatial-anchors-azure/web-app-running-live.png)

Il nome dell'app usato in questa sezione viene usato come prefisso dell'URL nel formato `https://<app_name>.azurewebsites.net`. Copiare questo URL in un editor di testo per un uso successivo.
