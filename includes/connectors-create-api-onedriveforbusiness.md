---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102760"
---
## <a name="prerequisites"></a>Prerequisiti

* Un account [OneDrive for business](https://OneDrive.com) 

Prima di poter usare l'account OneDrive for business con le app per la logica, è necessario autorizzare le app per la logica a connettersi all'account OneDrive for business nell'portale di Azure.

Seguire questa procedura per autorizzare le app per la logica a connettersi all'account OneDrive for business:  

1. Accedere al portale di Azure. 

1. In **servizi di Azure**selezionare **app**per la logica. Selezionare quindi il nome dell'app per la logica nell'elenco.

1. Nel menu dell'app per la logica selezionare **progettazione app** per la logica in **strumenti di sviluppo**.

1. Nella finestra di progettazione di app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere *OneDrive for business* nella casella di ricerca. Selezionare il trigger o l'azione da usare:  

   ![Screenshot della finestra di progettazione di app per la logica, che mostra il trigger di ricorrenza con le azioni API OneDrive for business](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. Se non sono mai state create connessioni a OneDrive for business prima di, seguire le istruzioni per fornire le credenziali di OneDrive for business. Queste credenziali vengono usate per autorizzare l'app per la logica ad accedere ai dati dell'account OneDrive for business:  

   ![Screenshot della finestra di progettazione di app per la logica, che visualizza la richiesta di accesso per OneDrive for business.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. Fornire il nome utente e la password di OneDrive for business per autorizzare l'app per la logica:  

   ![Screenshot della pagina di accesso di OneDrive for business, che visualizza la richiesta di accesso.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. La connessione è ora elencata nel passaggio. Selezionare Save (Salva) e quindi continuare a creare l'app per la logica. 

   ![Screenshot della finestra di progettazione di app per la logica, che mostra trigger con connessione OneDrive for business elencata.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
