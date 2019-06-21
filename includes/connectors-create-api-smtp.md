---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: d90ef1737f0107e5e323c1e90e3f9021efd4e4f7
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205793"
---
### <a name="prerequisites"></a>Prerequisiti
* Un account [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)  

Prima di poter usare l'account SMTP in un'app per la logica, è necessario autorizzare l'app per la logica a connettersi all'account SMTP. Fortunatamente, è possibile eseguire questa operazione in modo facile dalla propria app per la logica nel Portale di Azure.  

Ecco i passaggi per autorizzare l'app per la logica a connettersi all'account SMTP:  

1. Per creare una connessione a SMTP, nella finestra di progettazione dell'app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere *SMTP* nella casella di ricerca. Selezionare il trigger o l'azione che si desidera usare:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Se non sono mai state create connessioni a SMTP prima d'ora, verrà chiesto di fornire le relative credenziali. Queste credenziali verranno usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati dell'account SMTP:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Si noti che la connessione è stata creata ed è ora possibile procedere con gli altri passaggi nell'app per la logica:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

