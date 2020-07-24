---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040241"
---
## <a name="prerequisites"></a>Prerequisiti

* Un account [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Prima di poter usare l'account OneDrive con le app per la logica, è necessario autorizzare le app per la logica a connettersi all'account OneDrive nel portale di Azure.

Seguire questa procedura per autorizzare le app per la logica a connettersi all'account OneDrive:  

1. Accedere al portale di Azure. 

1. In **servizi di Azure**selezionare **app**per la logica. Selezionare quindi il nome dell'app per la logica nell'elenco.

1. Nel menu dell'app per la logica selezionare **progettazione app** per la logica in **strumenti di sviluppo**.

1. Nella finestra di progettazione di app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere *OneDrive* nella casella di ricerca. Selezionare il trigger o l'azione da usare:

   ![Screenshot della finestra di progettazione di app per la logica, che mostra l'elenco delle azioni dell'API OneDrive da aggiungere.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Se in precedenza non sono state create connessioni a OneDrive, seguire la richiesta di accesso con le credenziali di OneDrive:  

   ![Screenshot della finestra di progettazione di app per la logica, che visualizza la richiesta di accesso per l'API OneDrive.](./media/connectors-create-api-onedrive/onedrive-2.png)

3. Fare clic su **Accedi**, quindi immettere il nome utente e la password. Selezionare **Accedi**: 

   ![Screenshot della pagina di accesso account Microsoft per l'autorizzazione dell'API OneDrive.](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Queste credenziali vengono usate per autorizzare l'app per la logica ad accedere ai dati nell'account OneDrive. 

4. Scegliere **Sì** per autorizzare l'app per la logica a usare l'account OneDrive:  

   ![Screenshot dell'autorizzazione account Microsoft per le app per la logica, che mostra le azioni consentite.](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. La connessione è ora elencata nel passaggio. Selezionare Save (Salva) e quindi continuare a creare l'app per la logica. 

   ![Screenshot della finestra di progettazione di app per la logica, che mostra l'editor di azioni con connessione API OneDrive.](./media/connectors-create-api-onedrive/onedrive-5.png)
