---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632915"
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

In **Impostazioni** selezionare **Chiave**. Copiare il valore di **Chiave primaria** in un editor di testo. Questo valore è `Account Key`. Sarà necessario più avanti.

   ![Chiave account](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
