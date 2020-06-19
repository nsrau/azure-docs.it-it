---
title: Connettersi a Twitter da App per la logica di Azure
description: Utilizzando App per la logica di Azure, è possibile automatizzare attività e flussi di lavoro che monitorano e gestiscono tweet, oltre che ottenere dati su follower, utenti seguiti, altri utenti, sequenze temporali e altro ancora dal proprio account Twitter
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: f2db6d614c3c12cb1be87724e79d79a16769d6b8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829597"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitorare e gestire Twitter con App per la logica di Azure

Con App per la logica di Azure e il connettore Twitter, è possibile creare attività automatizzate e flussi di lavoro che monitorano e gestiscono i dati più interessanti di Twitter, come tweet, follower, utenti e utenti seguiti, sequenze temporali e altro ancora, insieme ad altre azioni, ad esempio:

* Monitorare, postare e cercare tweet.
* Ottenere dati, ad esempio follower, utenti seguiti, sequenze temporali e altro ancora.

È possibile usare i trigger per ottenere risposte dall'account Twitter e rendere l'output disponibile per altre azioni. È possibile usare azioni che eseguono attività con l'account Twitter. È anche possibile fare in modo che altre azioni usino l'output delle azioni di Twitter. Ad esempio, quando viene postato un nuovo tweet con uno specifico hashtag, è possibile inviare messaggi con il connettore Slack. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Account Twitter e credenziali utente

   Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account Twitter.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account Twitter. Per iniziare con un trigger di Twitter, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione di Twitter, avviare l'app per la logica con un altro trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-twitter"></a>Connettersi a Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

   * Per le app per la logica vuote, nella casella di ricerca immettere "twitter" come filtro. 
   Nell'elenco dei trigger selezionare il trigger desiderato. 

     -oppure-

   * Per le app per la logica esistenti: 
   
     * Nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 

       -oppure-

     * Spostare il puntatore del mouse sulla freccia visualizzata tra i passaggi in cui si vuole aggiungere un'azione. 
     Scegliere il segno più ( **+** ) visualizzato e quindi selezionare **Aggiungi un'azione**.
     
       Nella casella di ricerca immettere "twitter" come filtro. 
       Nell'elenco delle azioni selezionare l'azione desiderata.

1. Se viene chiesto di accedere a Twitter, accedere immediatamente per autorizzare l'accesso all'app per la logica.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="examples"></a>Esempi

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Trigger Twitter: Quando viene visualizzato un nuovo tweet

Questo trigger avvia un flusso di lavoro di un'app per la logica quando il trigger rileva un nuovo tweet, ad esempio con l'hashtag #Seattle. Così, ad esempio, quando questi tweet vengono rilevati, è possibile aggiungere un file con il contenuto dei tweet alla risorsa di archiviazione, ad esempio un account Dropbox utilizzando il connettore Dropbox. 

Facoltativamente, è possibile includere una condizione secondo cui i tweet idonei devono provenire da utenti con almeno un numero specificato di follower.

**Esempio riguardante un'organizzazione**: è possibile usare questo trigger per monitorare i tweet della propria azienda e caricare il contenuto dei tweet in un database SQL.

### <a name="twitter-action-post-a-tweet"></a>Azione Twitter: Pubblica un tweet

Questa azione pubblica un tweet, ma è possibile impostare l'azione in modo che il tweet contenga il contenuto dei tweet trovati dal trigger precedentemente descritto. 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/twitterconnector/) del connettore.

## <a name="get-support"></a>Supporto

* Per eventuali domande, visitare la [pagina Microsoft delle domande e risposte per App per la logica di Azure](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
