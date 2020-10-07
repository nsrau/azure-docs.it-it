---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b2b3ca886359a0b4c906b89ed76f57486fc2c368
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "83638975"
---
## <a name="create-a-spatial-anchors-resource"></a>Creare una risorsa di Ancoraggi nello spazio

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.

Nel riquadro di spostamento sinistro del portale di Azure fare clic su **Crea una risorsa**.

Digitare **Ancoraggi nello spazio** nella casella di ricerca.

   ![Ricerca di Ancoraggi nello spazio](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selezionare **Ancoraggi nello spazio**. Nella finestra di dialogo selezionare **Crea**.

Nella finestra di dialogo **Account ancoraggi nello spazio**:

- Immettere un nome risorsa univoco, usando caratteri alfanumerici regolari.
- Selezionare la sottoscrizione a cui collegare la risorsa.
- Creare un gruppo di risorse selezionando **Crea nuovo**. Assegnare al gruppo il nome **myResourceGroup** e selezionare **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Selezionare un'area in cui inserire la risorsa.
- Selezionare **Nuovo** per iniziare a creare la risorsa.

   ![Creare una risorsa](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Dopo aver creato la risorsa, il portale di Azure indicherà che la distribuzione è stata completata. Fare clic su **Vai alla risorsa**.

![Distribuzione completata](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Sarà possibile quindi visualizzare le proprietà della risorsa. Copiare il valore dell'**ID account** della risorsa in un editor di testo perché sarà necessario in seguito.

   ![Proprietà risorsa](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Copiare il valore di **Dominio account** della risorsa in un editor di testo perché sarà necessario in seguito.

   ![Dominio account](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

In **Impostazioni** selezionare **Chiave**. Copiare il valore di **Chiave primaria** in un editor di testo. Questo valore è `Account Key`. Sarà necessario più avanti.

   ![Chiave account](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
