---
title: Risoluzione dei problemi-QnA Maker
titleSuffix: Azure Cognitive Services
description: L'elenco curato delle domande più frequenti relative al servizio QnA Maker consentirà di adottare il servizio più rapidamente e con risultati migliori.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e98fd089ce8ec1285232840a40bb42ac5b81446
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795435"
---
# <a name="troubleshooting-for-qna-maker"></a>Risoluzione dei problemi per QnA Maker

L'elenco curato delle domande più frequenti relative al servizio QnA Maker consentirà di adottare il servizio più rapidamente e con risultati migliori.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>Come ottenere l'endpoint del servizio QnAMaker

L'endpoint del servizio QnAMaker è utile a scopo di debug quando si contatta il supporto di QnAMaker o UserVoice. L'endpoint è un URL nel formato seguente: https://your-resource-name.azurewebsites.net.
    
1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse Azure per QnA Maker nel portale di Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selezionare il servizio app associato alla risorsa QnA Maker. In genere, i nomi sono gli stessi.

     ![Selezionare il servizio app QnA Maker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. L'URL dell'endpoint è disponibile nella sezione Panoramica

    ![Endpoint QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Usare il bot della guida nel portale di QnA Maker

QnA Maker fornisce un bot della **Guida** all'interno del portale QnA Maker per aiutarti. Il bot della guida è disponibile in ogni pagina Web. Il bot USA QnA Maker per fornire risposte e fornisce il [ C# progetto di codice di bot Framework](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) al bot, in modo che sia possibile iniziare subito a usare il proprio bot di risposta. 

![! [QnA Maker fornisce un bot * * Help * * all'interno del portale di QnA Maker.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Gestione della knowledge base

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Ho accidentalmente eliminato una parte di QnA Maker, cosa devo fare? 

Non eliminare i servizi di Azure creati insieme alla risorsa QnA Maker come la ricerca o l'app Web. Per il corretto funzionamento di QnA Maker, è necessario eliminarne uno QnA Maker smette di funzionare correttamente.

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

Ogni operazione di modifica, nell'aggiornamento di tabelle, nel test o nell'impostazione, deve essere salvata prima di poter essere pubblicata. Assicurarsi di fare clic sul pulsante **Salva e Train** dopo ogni operazione di modifica.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>La Knowledge Base supporta i dati avanzati o i contenuti multimediali?

La Knowledge Base supporta Markdown. L'estrazione automatica dagli URL ha tuttavia una capacità limitata di conversione da HTML a Markdown. Per usare le funzionalità complete di Markdown, è possibile modificare i contenuti direttamente nella tabella o caricare una Knowledge Base con contenuti avanzati.

Al momento non è supportato alcun tipo di contenuto multimediale, ad esempio immagini e video.

### <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker supporta lingue diverse dall'inglese?

Vedere altri dettagli sulle [lingue supportate](./Overview/languages-supported.md).

Se sono presenti contenuti in più lingue, assicurarsi di creare un servizio separato per ciascuna lingua.

## <a name="manage-service"></a>Gestire il servizio

### <a name="when-should-i-restart-my-app-service"></a>Quando è consigliabile riavviare un servizio app? 

Aggiornare il servizio app quando viene visualizzata l'icona di avviso accanto al valore di versione della knowledge base nella tabella **Chiavi endpoint** della **pagina** [Impostazioni utente](https://www.qnamaker.ai/UserSettings).

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Il servizio di ricerca esistente è stato eliminato. Come si risolve questo problema?

Se si elimina un indice ricerca cognitiva di Azure, l'operazione è finale e non è possibile recuperare l'indice. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Ho eliminato l'indice `testkb` nel servizio di ricerca. Come si risolve questo problema? 

Non è possibile recuperare i dati precedenti. Creare una nuova risorsa di QnA Maker e creare di nuovo la Knowledge base.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Quando è necessario aggiornare le chiavi endpoint?

Aggiornare le chiavi endpoint se si sospetta che siano state compromesse.

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>È possibile usare la stessa risorsa ricerca cognitiva di Azure per le Knowledge base usando più lingue?

Per usare più lingue e knowledge base, l'utente deve creare una risorsa di QnA Maker per ciascuna lingua. Verrà creato un servizio di ricerca di Azure separato per ogni lingua. La combinazione di knowledge base in lingue diverse in un singolo servizio di Ricerca di Azure comporterà una ridotta pertinenza dei risultati.

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>Come è possibile modificare il nome della risorsa ricerca cognitiva di Azure usata da QnA Maker?

Il nome della risorsa ricerca cognitiva di Azure è il nome della risorsa QnA Maker con alcune lettere casuali aggiunte alla fine. La distinzione tra più risorse di ricerca per QnA Maker risulta quindi più difficile. Creare un servizio di ricerca separato (denominarlo come si desidera) e connetterlo al servizio QnA. I passaggi sono simili ai passaggi necessari per [aggiornare una ricerca di Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Quando QnA Maker restituisce `Runtime core is not initialized,` come risolverlo?

Lo spazio su disco per il servizio app potrebbe essere pieno. Passaggi per la correzione dello spazio su disco:

1. Nella [portale di Azure](https://portal.azure.com)selezionare il servizio App del QnA Maker, quindi arrestare il servizio.
1. Sempre nel servizio app, selezionare strumenti di **sviluppo**, **strumenti avanzati**, quindi **fare**clic su. Verrà visualizzata una nuova finestra del browser.
1. Selezionare **console di debug**, quindi **cmd** per aprire uno strumento da riga di comando. 
1. Passare alla directory _site/wwwroot/data/QnAMaker/_ .
1. Rimuovere tutte le cartelle il cui nome inizia con `rd`. 

    Non **eliminare** gli elementi seguenti:

    * File KbIdToRankerMappings. txt
    * File EndpointSettings. JSON
    * Cartella EndpointKeys 

1. Avviare il servizio app.
1. Accedere alla Knowledge base per verificarne il funzionamento. 


## <a name="integrate-with-other-services-including-bots"></a>Integrazione con altri servizi, inclusi Bot

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>È necessario usare Bot Framework per usare QnA Maker?

No, non è necessario usare [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) con QnA Maker. Tuttavia, QnA Maker è disponibile come uno dei diversi modelli nel [servizio Azure bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Il servizio Bot consente di sviluppare rapidamente bot intelligenti con Microsoft Bot Framework e viene eseguito in un ambiente senza server.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Come è possibile creare un nuovo bot con QnA Maker?

Seguire le istruzioni riportate in [questa](./Tutorials/create-qna-bot.md) documentazione per creare il bot con il servizio Azure Bot.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Ricerca per categorie usare una Knowledge base diversa con un servizio Azure bot esistente?

È necessario disporre delle informazioni seguenti sulla Knowledge Base:

* ID della Knowledge base.
* Nome del sottodominio personalizzato dell'endpoint pubblicato della Knowledge base, noto come `host`, disponibile nella pagina **Impostazioni** dopo la pubblicazione.
* Chiave endpoint pubblicata della Knowledge base, disponibile nella pagina **Impostazioni** dopo la pubblicazione. 

Con queste informazioni, andare al servizio app del bot nella portale di Azure. In **Impostazioni-> configurazione > Impostazioni applicazione**modificare tali valori.  

La chiave dell'endpoint della Knowledge base è denominata `QnAAuthkey` nel servizio ABS. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Due o più applicazioni client possono condividere una Knowledge base? 

Sì, la Knowledge base può essere sottoposta a query da un numero qualsiasi di client. Se la risposta dalla Knowledge base sembra essere lenta o timeout, provare ad aggiornare il livello di servizio per il servizio app associato alla Knowledge base.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Come si incorpora il servizio QnA Maker nel proprio sito Web?

Seguire questa procedura per incorporare il servizio QnA Maker come controllo di chat nel sito Web:

1. Creare il bot di domande frequenti seguendo [queste istruzioni](./Tutorials/create-qna-bot.md).
2. Abilitare la chat Web seguendo [questi](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) passaggi

## <a name="data-storage"></a>Archiviazione dei dati

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Quali dati vengono archiviati e dove vengono archiviati? 

Quando si crea un servizio QnA Maker, si seleziona un'area di Azure. Le knowledge base e i file di log vengono archiviati in questa area. 
