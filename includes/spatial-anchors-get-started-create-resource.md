---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752855"
---
## <a name="create-a-spatial-anchors-resource"></a>Creare una risorsa di Ancoraggi nello spazio

1. Passare al <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.

2. Nel menu sinistro del portale di Azure selezionare **Crea una risorsa**.

3. Digitare "Ancoraggi nello spazio" nella barra di ricerca.

   ![Ricerca di Ancoraggi nello spazio](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Selezionare **Ancoraggi nello spazio (anteprima)** per aprire una finestra di dialogo e fare clic su **Crea**.

5. Nel modulo **Account ancoraggi nello spazio**:

   1. Specificare un nome di risorsa univoco.
   2. Selezionare la sottoscrizione a cui collegare la risorsa.
   3. Selezionare **Crea nuovo** per creare un gruppo di risorse a cui assegnare il nome **myResourceGroup**, quindi selezionare **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Selezionare una località (area) in cui inserire la risorsa.
   5. Selezionare **Nuovo** per iniziare a creare la risorsa.

   ![Creare una risorsa](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Dopo aver completato correttamente la creazione della risorsa, è possibile visualizzare le relative proprietà. Prendere nota del valore di **ID account** della risorsa perché sarà necessario più avanti.

   ![Visualizzare le proprietà della risorsa](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. In **Impostazioni** selezionare **Chiave** e prendere nota del valore di **Chiave primaria**. Questo valore è `Account Key` e verrà usato più avanti.

   ![Visualizzare la chiave dell'account](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
