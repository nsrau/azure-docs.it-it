---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524209"
---
## <a name="prerequisites"></a>Prerequisiti

* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un account [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Prima di poter usare l'account OneDrive in un'app per la logica, autorizzare l'app per la logica a connettersi all'account OneDrive.  È possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure. 

Attenersi alla seguente procedura per autorizzare l'app per la logica a connettersi all'account OneDrive:

1. Creare un'app per la logica. Nella finestra di progettazione dell'app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere "onedrive" nella casella di ricerca. Selezionare un trigger o un'azione:  
   ![Una finestra di dialogo intitolata "Mostra API gestite da Microsoft" include una casella di ricerca che contiene "OneDrive". Di seguito è riportato un elenco di quattro trigger. Il primo nell'elenco è "OneDrive-quando viene creato un file", che è selezionato.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Se non sono mai state create connessioni a OneDrive prima d'ora, verrà chiesto di effettuare l'accesso usando le credenziali di OneDrive:  
   ![Una finestra di dialogo intitolata "OneDrive-quando viene creato un file" ha un pulsante con etichetta "Accedi".](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Fare clic su **Accedi**, quindi immettere il nome utente e la password. Selezionare **Accedi**:  
   ![Una finestra di dialogo intitolata "Accedi" indica di usare la account Microsoft. Dispone di due caselle di testo denominate "posta elettronica o telefono" e "password". contiene anche una casella di controllo denominata "Mantieni l'accesso" e un pulsante con etichetta "Accedi".](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Queste credenziali vengono usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati presenti nell'account OneDrive. 
4. Scegliere **Sì** per autorizzare l'app per la logica a usare l'account OneDrive:  
   ![Una finestra di dialogo intitolata "consentire all'app di accedere alle informazioni?" richiede l'autorizzazione a eseguire le quattro operazioni seguenti: 1) "Accedi automaticamente", 2) "Accedi agli indirizzi di posta elettronica", 3) "accedi alle tue informazioni in qualsiasi momento" e 4) "accedi ai file OneDrive". È disponibile un pulsante "Sì" per concedere l'autorizzazione e un pulsante "No" per negarlo. È disponibile un collegamento per modificare queste autorizzazioni dell'applicazione.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. La connessione è stata creata. A questo punto, procedere con gli altri passaggi nell'app per la logica:  
   ![Una finestra di dialogo intitolata "quando viene creato un file" ha una casella di testo denominata "FOLDER" con un pulsante Sfoglia associato.](./media/connectors-create-api-onedrive/onedrive-5.png)

