---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: d249a205c64f4e067f2d81c7e1068c8ad9756958
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202760"
---
### <a name="prerequisites"></a>Prerequisiti
* Un account [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  

Prima di poter usare l'account SFTP in un'app per la logica, è necessario autorizzare l'app per la logica a connettersi a tale account. Fortunatamente, è possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure.  

Ecco i passaggi per autorizzare l'app per la logica a connettersi all'account SFTP:  

1. Per creare una connessione a SFTP, nella finestra di progettazione dell'app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere *SFTP* nella casella di ricerca. Selezionare il trigger **SFTP - Quando viene aggiunto o modificato un file**:  
   ![Immagine di connessione online a SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Se non sono mai state create connessioni a SFTP prima d'ora, verrà chiesto di fornire le relative credenziali. Queste credenziali verranno usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati dell'account SFTP:  
   ![Immagine di connessione online a SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Si noti che la connessione è stata creata ed è ora possibile procedere con gli altri passaggi nell'app per la logica:   
   ![Immagine di connessione online a SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 

