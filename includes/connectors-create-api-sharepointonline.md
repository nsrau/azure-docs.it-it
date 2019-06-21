---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 0cabc58d856c09accd9b1924fe63d6518b1cb9ef
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205785"
---
Per connettersi a **SharePoint Online** è necessario specificare la propria identità, completa di nome utente, password, credenziali smart card e così via, in SharePoint Online. Dopo l'autenticazione, è possibile usare il connettore SharePoint Online nella propria app per la logica. 

Durante la progettazione dell'app per la logica, seguire questa procedura per accedere a SharePoint e creare la **connessione** da usare nell'app per la logica:

1. Nella casella di ricerca digitare SharePoint e attendere che la ricerca restituisca tutti i trigger e le azioni correlati a SharePoint Online:   
   ![Configurare SharePoint][1]  
2. Selezionare il trigger **SharePoint Online - Quando viene creato un file**.  
3. Selezionare **Sign in to SharePoint Online** (Accedi a SharePoint Online):   
   ![Configurare SharePoint][2]    
4. Fornire le credenziali di SharePoint per accedere ed eseguire l'autenticazione con SharePoint   
   ![Configurare SharePoint][3]     
5. Al termine dell'autenticazione si verrà reindirizzati all'app per la logica. A questo punto, la connessione è stata creata. Il messaggio che appare nella parte inferiore indica che si è connessi a SharePoint.  
   ![Configurare SharePoint][4]  
6. È quindi possibile aggiungere altri trigger e azioni necessari per completare l'app per la logica.   

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png
