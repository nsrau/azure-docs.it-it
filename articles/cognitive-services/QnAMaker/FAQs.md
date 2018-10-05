---
title: Domande frequenti - QnA Maker
titleSuffix: Azure Cognitive Services
description: Elenco delle domande frequenti relative al servizio QnA Maker
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: efefd595c43d7f46ff1ead91577d070cf8fb90e4
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47164617"
---
# <a name="frequently-asked-questions"></a>Domande frequenti

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Perché i miei URL/file non estraggono coppie di domanda/risposta?

È possibile che QnA Maker non possa estrarre automaticamente alcune coppie di domanda/riposta da URL di domande frequenti validi. In questi casi è possibile incollare il contenuto di domande/risposte in un file TXT e verificare se lo strumento è in grado di inglobarlo. In alternativa è possibile aggiungere contenuti alla Knowledge Base in modo editoriale.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Quali sono le dimensioni massime consentite per la creazione di Knowledge Base?

Le dimensioni della Knowledge Base dipendono dallo SKU di Ricerca di Azure scelto durante la creazione del servizio QnA Maker. Vedere [qui](./Tutorials/choosing-capacity-qnamaker-deployment.md) per altri dettagli.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Perché non vedo nulla nell'elenco a discesa quando cerco di creare una nuova Knowledge Base?

Non è stato ancora creato alcun servizio QnA Maker in Azure. Per informazioni su come creare il servizio, vedere [qui](./How-To/set-up-qnamaker-service-azure.md).

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Come si condivide una Knowledge Base?

La condivisione funziona a livello di servizio QnA Maker, in altre parole vengono condivise tutte le Knowledge Base all'interno di un servizio. Vedere [qui](./How-To/collaborate-knowledge-base.md) per informazioni su come collaborare a una Knowledge Base.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Come si modifica il messaggio predefinito visualizzato se non viene trovata alcuna corrispondenza?

Il messaggio predefinito fa parte delle impostazioni del servizio app.
- Passare alla risorsa del servizio app nel portale di Azure

![Servizio app QnA Maker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Fare clic sull'opzione **Impostazioni**

![Impostazioni del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Modificare il valore dell'impostazione **DefaultAnswer**
- Riavviare il servizio app

![Riavvio del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Perché il collegamento SharePoint non viene estratto?

Lo strumento analizza solo gli URL pubblici e non supporta attualmente le origini dati autenticate. In alternativa è possibile scaricare il file e usare l'opzione di caricamento file per estrarre domande e risposte.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Gli aggiornamenti apportati alla Knowledge Base non sono riflesse nella pubblicazione. Perché?

Ogni operazione di modifica, nell'aggiornamento di tabelle, nel test o nelle impostazioni, deve essere salvata prima di poter essere pubblicata. Fare clic sul pulsante Save and train (Salva ed esegui training) dopo ogni operazione di modifica.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Quando è necessario aggiornare le chiavi endpoint?

È necessario aggiornare le chiavi endpoint se si sospetta che siano state compromesse.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>La Knowledge Base supporta i dati avanzati o i contenuti multimediali?

La Knowledge Base supporta Markdown. L'estrazione automatica dagli URL ha tuttavia una capacità limitata di conversione da HTML a Markdown. Per usare le funzionalità complete di Markdown, è possibile modificare i contenuti direttamente nella tabella o caricare una Knowledge Base con contenuti avanzati.

Al momento non è supportato alcun tipo di contenuto multimediale, ad esempio immagini e video.

## <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker supporta lingue diverse dall'inglese?

Vedere altri dettagli sulle [lingue supportate](./Overview/languages-supported.md).

Se sono presenti contenuti in più lingue, assicurarsi di creare un servizio separato per ciascuna lingua.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>È necessario usare Bot Framework per usare QnA Maker?

No, non è necessario usare Bot Framework con QnA Maker. QnA Maker viene tuttavia offerto come uno dei diversi modelli del servizio Azure Bot. Il servizio Bot consente di sviluppare rapidamente bot intelligenti con Microsoft Bot Framework e viene eseguito in un ambiente senza server.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Come si crea un bot con QnA Maker?

Seguire le istruzioni riportate in [questa](./Tutorials/create-qna-bot.md) documentazione per creare il bot con Azure Bot.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Come si incorpora il servizio QnA Maker nel proprio sito Web?

Seguire questa procedura per incorporare il servizio QnA Maker come controllo di chat nel sito Web:

1. Creare il bot di domande frequenti seguendo [queste istruzioni](./Tutorials/create-qna-bot.md).
2. Abilitare la chat Web seguendo [questi](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) passaggi


