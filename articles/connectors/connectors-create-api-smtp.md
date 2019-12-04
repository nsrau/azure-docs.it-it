---
title: Connettersi a SMTP da app per la logica di Azure
description: Automatizzare le attività e i flussi di lavoro per l'invio di messaggi di posta elettronica tramite l'account SMTP (Simple Mail Transfer Protocol) usando App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: fb501a158c839e6d4d71fc2af5ae50e48c248466
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789155"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Inviare messaggi di posta elettronica dall'account SMTP con App per la logica di Azure

Con App per la logica di Azure e il connettore SMTP (Simple Mail Transfer Protocol), è possibile creare attività e flussi di lavoro automatizzati per inviare messaggi di posta elettronica dall'account SMTP. È anche possibile fare in modo che altre azioni usino l'output delle azioni di SMTP. Ad esempio, dopo che SMTP ha inviato un messaggio di posta elettronica, è possibile informare il team in Slack con il connettore Slack. Se non si ha familiarità con App per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* L'account SMTP e le credenziali utente.

  Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account SMTP.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account SMTP. Per usare un'azione di SMTP, avviare l'app per la logica con un trigger, ad esempio un trigger Salesforce, se si ha un account Salesforce.

  È ad esempio possibile avviare l'app per la logica con il trigger Salesforce **Quando un record viene creato**. 
  Questo trigger viene attivato ogni volta che in Salesforce viene creato un nuovo record, ad esempio un lead. 
  È quindi possibile seguire questo trigger con l'azione **Invia messaggio di posta elettronica** di SMTP. In questo modo, quando viene creato il nuovo record, l'app per la logica invia un messaggio di posta elettronica sul nuovo record dall'account SMTP.

## <a name="connect-to-smtp"></a>Connettersi a un server SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Nell'ultimo passaggio in cui si vuole aggiungere un'azione di SMTP, scegliere **Nuovo passaggio**. 

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno più ( **+** ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca immettere "smtp" come filtro. Nell'elenco delle azioni scegliere l'azione desiderata.

1. Quando richiesto, specificare queste informazioni di connessione:

   | Proprietà | Obbligatoria | Description |
   |----------|----------|-------------|
   | **Connection Name** (Nome connessione) | SÌ | Nome per la connessione al server SMTP | 
   | **Indirizzo del server SMTP** | SÌ | Indirizzo relativo al server SMTP | 
   | **Nome utente** | SÌ | Nome utente per l'account SMTP | 
   | **Password** | SÌ | Password per l'account SMTP | 
   | **Porta del server SMTP** | No | Porta specifica sul server SMTP che si vuole usare | 
   | **Abilitare SSL?** | No | Attivare o disattivare la crittografia SSL. | 
   |||| 

1. Specificare i dettagli necessari per l'azione selezionata. 

1. Salvare l'app per la logica e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/smtpconnector/) del connettore.

## <a name="get-support"></a>Ottenere supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)