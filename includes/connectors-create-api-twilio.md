---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 845b27b8efd66de87ec08e0b5b81bcc332dffdfb
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202719"
---
### <a name="prerequisites"></a>Prerequisiti
* Un account Twilio
* Un numero di telefono Twilio verificato in grado di ricevere SMS
* Un numero di telefono Twilio verificato in grado di inviare SMS

> [!NOTE]
> Se si usa un account di valutazione di Twilio, è possibile inviare SMS esclusivamente ai numeri di telefono **verificati**.  
> 
> 

Prima di poter usare l'account Twilio in un'app per la logica, è necessario autorizzare l'app per la logica a connettersi all'account Twilio. Fortunatamente, è possibile eseguire questa operazione in modo facile dalla propria app per la logica nel Portale di Azure. 

Ecco i passaggi per autorizzare l'app per la logica a connettersi all'account Twilio:

1. Per creare una connessione a Twilio, nella finestra di progettazione dell'app per la logica selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere *Twilio* nella casella di ricerca. Selezionare il trigger o l'azione che si desidera usare:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Se non sono mai state create connessioni a Twilio prima d'ora, verrà chiesto di fornire le credenziali di Twilio. Queste credenziali verranno usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati dell'account Twilio:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Saranno necessari l'**ID dell'account Twilio** e il **token di accesso Twilio** disponibili nel dashboard di Twilio. Accedere ora all'account Twilio per ottenere questi due tipi di informazioni:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio e le app per la logica usano diversi nomi per identificare questi due tipi di informazioni. Ecco come eseguirne il mapping alla finestra di dialogo relativa alle app per la logica: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Selezionare il pulsante **Crea connessione**:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Si noti che la connessione è stata creata ed è ora possibile procedere con gli altri passaggi nell'app per la logica:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

