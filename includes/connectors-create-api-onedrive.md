---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 7cfce34cb2d6002dba5ec570bf859ec47e894c65
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134461"
---
#### <a name="prerequisites"></a>Prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un account [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Prima di poter usare l'account OneDrive in un'app per la logica, autorizzare l'app per la logica a connettersi all'account OneDrive.  È possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure. 

Attenersi alla seguente procedura per autorizzare l'app per la logica a connettersi all'account OneDrive:

1. Creare un'app per la logica. Nella finestra di progettazione dell'app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere "onedrive" nella casella di ricerca. Selezionare un trigger o un'azione:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se non sono mai state create connessioni a OneDrive prima d'ora, verrà chiesto di effettuare l'accesso usando le credenziali di OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Fare clic su **Accedi**, quindi immettere il nome utente e la password. Fare clic su **Accedi**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Queste credenziali vengono usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati presenti nell'account OneDrive. 
4. Scegliere **Sì** per autorizzare l'app per la logica a usare l'account OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. La connessione è stata creata. A questo punto, procedere con gli altri passaggi nell'app per la logica:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

