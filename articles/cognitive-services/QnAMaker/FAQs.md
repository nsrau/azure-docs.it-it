---
title: Domande frequenti - QnA Maker
titleSuffix: Azure Cognitive Services
description: L'elenco delle domande più frequenti relative al servizio QnA Maker aiuta ad accelerare l'adozione del servizio e ottenere risultati migliori.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 11/27/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 6399fe3e53a572c8aea6b804c0e82ffed41cab77
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873955"
---
# <a name="frequently-asked-questions-for-qna-maker"></a>Domande frequenti per QnA Maker

L'elenco delle domande più frequenti relative al servizio QnA Maker aiuta ad accelerare l'adozione del servizio e ottenere risultati migliori.

## <a name="manage-the-knowledge-base"></a>Gestione della knowledge base

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Ho accidentalmente eliminato una parte di QnA Maker, cosa devo fare? 

Tutte le eliminazioni sono permanenti, incluse le coppie di domande e risposte, i file, gli URL, le domande e risposte personalizzate, le knowledge base e le risorse di Azure. Prima di eliminare qualsiasi parte della knowledge base, quindi, assicurarsi di esportare le knowledge base dalla pagina **Impostazioni**. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Perché i miei URL/file non estraggono coppie di domanda/risposta?

È possibile che QnA Maker non possa estrarre automaticamente alcune coppie di domanda/riposta da URL di domande frequenti validi. In questi casi è possibile incollare il contenuto di domande/risposte in un file TXT e verificare se lo strumento è in grado di inglobarlo. In alternativa è possibile aggiungere contenuti alla Knowledge Base in modo editoriale tramite il [portale QnA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Quali sono le dimensioni massime consentite per la creazione di Knowledge Base?

Le dimensioni della Knowledge Base dipendono dallo SKU di Ricerca di Azure scelto durante la creazione del servizio QnA Maker. Vedere [qui](./Tutorials/choosing-capacity-qnamaker-deployment.md) per altri dettagli.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Perché non vedo nulla nell'elenco a discesa quando cerco di creare una nuova Knowledge Base?

Non è stato ancora creato alcun servizio QnA Maker in Azure. Per altre informazioni su come eseguire questa operazione, leggere [qui](./How-To/set-up-qnamaker-service-azure.md).

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Come si condivide una Knowledge Base?

La condivisione funziona a livello di servizio QnA Maker, in altre parole vengono condivise tutte le Knowledge Base all'interno di un servizio. Vedere [qui](./How-To/collaborate-knowledge-base.md) per informazioni su come collaborare a una Knowledge Base.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>È possibile condividere una knowledge base con un collaboratore che non è nello stesso tenant di AAD, per modificare una knowledge base? 

La condivisione è basata sul controllo degli accessi in base al ruolo di Azure. Se è possibile condividere _qualsiasi_ risorsa in Azure con un altro utente, è anche possibile condividere QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Se si dispone di un piano di servizio app con 5 knowledge base QnAMaker. È possibile assegnare diritti di lettura/scrittura per 5 utenti diversi in modo che ognuno di essi possa accedere solo a 1 knowledge base QnAMaker?

È possibile condividere un intero servizio QnAMaker, non i singoli knowledge base.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Come si modifica il messaggio predefinito visualizzato se non viene trovata alcuna corrispondenza?

Il messaggio predefinito fa parte delle impostazioni del servizio app.
- Passare alla risorsa del servizio app nel portale di Azure

![Servizio app QnA Maker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Fare clic sull'opzione **Impostazioni**

![Impostazioni del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Modificare il valore dell'impostazione **DefaultAnswer**
- Riavviare il servizio app

![Riavvio del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Perché il collegamento SharePoint non viene estratto?

Per altre informazioni, vedere [Posizioni della data factory](./Concepts/data-sources-supported.md#data-source-locations).

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Gli aggiornamenti apportati alla Knowledge Base non sono riflesse nella pubblicazione. Perché?

Ogni operazione di modifica, nell'aggiornamento di tabelle, nel test o nell'impostazione, deve essere salvata prima di poter essere pubblicata. Fare clic sul pulsante  **Save and train**  (Salva ed esegui training) dopo ogni operazione di modifica.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>La Knowledge Base supporta i dati avanzati o i contenuti multimediali?

La Knowledge Base supporta Markdown. L'estrazione automatica dagli URL ha tuttavia una capacità limitata di conversione da HTML a Markdown. Per usare le funzionalità complete di Markdown, è possibile modificare i contenuti direttamente nella tabella o caricare una Knowledge Base con contenuti avanzati.

Al momento non è supportato alcun tipo di contenuto multimediale, ad esempio immagini e video.

### <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker supporta lingue diverse dall'inglese?

Vedere altri dettagli sulle [lingue supportate](./Overview/languages-supported.md).

Se sono presenti contenuti in più lingue, assicurarsi di creare un servizio separato per ciascuna lingua.

## <a name="manage-service"></a>Gestire il servizio

### <a name="when-should-i-restart-my-app-service"></a>Quando è consigliabile riavviare un servizio app? 

Aggiornare il servizio app quando viene visualizzata l'icona di avviso accanto al valore di versione della knowledge base nella tabella **Chiavi endpoint** della **pagina** [Impostazioni utente](https://www.qnamaker.ai/UserSettings).

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Quando è necessario aggiornare le chiavi endpoint?

Aggiornare le chiavi endpoint se si sospetta che siano state compromesse.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>È possibile usare la stessa risorsa di Ricerca di Azure per le knowledge base con più lingue?

Per usare più lingue e knowledge base, l'utente deve creare una risorsa di QnA Maker per ciascuna lingua. Verrà creato un servizio di ricerca di Azure separato per ogni lingua. La combinazione di knowledge base in lingue diverse in un singolo servizio di Ricerca di Azure comporterà una ridotta pertinenza dei risultati.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Come è possibile modificare il nome della risorsa di Ricerca di Azure usato da QnA Maker?

Il nome della risorsa di Ricerca di Azure è il nome della risorsa QnA Maker a cui vengono aggiunte alcune lettere casuali alla fine del nome. La distinzione tra più risorse di ricerca per QnA Maker risulta quindi più difficile. Creare un servizio separato di Ricerca di Azure (assegnandogli un nome nel modo desiderato) e collegarlo al servizio QnA. I passaggi sono simili ai passaggi per [eseguire l'aggiornamento di Ricerca di Azure](How-To/upgrade-qnamaker-service.md#upgrade-azure-search-service).

## <a name="integrate-with-other-services-including-bots"></a>Integrazione con altri servizi, inclusi Bot

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>È necessario usare Bot Framework per usare QnA Maker?

No, non è necessario usare Bot Framework con QnA Maker. QnA Maker viene tuttavia offerto come uno dei diversi modelli del servizio Azure Bot. Il servizio Bot consente di sviluppare rapidamente bot intelligenti con Microsoft Bot Framework e viene eseguito in un ambiente senza server.

### <a name="how-can-i-create-a-bot-with-qna-maker"></a>Come si crea un bot con QnA Maker?

Seguire le istruzioni riportate in [questa](./Tutorials/create-qna-bot.md) documentazione per creare il bot con il servizio Azure Bot.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Come si incorpora il servizio QnA Maker nel proprio sito Web?

Seguire questa procedura per incorporare il servizio QnA Maker come controllo di chat nel sito Web:

1. Creare il bot di domande frequenti seguendo [queste istruzioni](./Tutorials/create-qna-bot.md).
2. Abilitare la chat Web seguendo [questi](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) passaggi

## <a name="data-storage"></a>Archiviazione dei dati

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Quali dati vengono archiviati e dove vengono archiviati? 

Quando si crea un servizio QnA Maker, si seleziona un'area di Azure. Le knowledge base e i file di log vengono archiviati in questa area. 
