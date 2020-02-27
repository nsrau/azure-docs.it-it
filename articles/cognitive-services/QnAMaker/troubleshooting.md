---
title: Risoluzione dei problemi-QnA Maker
description: L'elenco curato delle domande più frequenti relative al servizio QnA Maker consentirà di adottare il servizio più rapidamente e con risultati migliori.
ms.topic: article
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 9032f2cdc134289fef6c0cbc2e874198aefa90ee
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650385"
---
# <a name="troubleshooting-for-qna-maker"></a>Risoluzione dei problemi per QnA Maker

L'elenco curato delle domande più frequenti relative al servizio QnA Maker consentirà di adottare il servizio più rapidamente e con risultati migliori.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

<details>
<summary><b>Come ottenere l'endpoint del servizio QnAMaker</b></summary>

**Risposta:** L'endpoint del servizio QnAMaker è utile a scopo di debug quando si contatta il supporto di QnAMaker o UserVoice. L'endpoint è un URL nel formato seguente: https://your-resource-name.azurewebsites.net.

1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse Azure per QnA Maker nel portale di Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selezionare il servizio app associato alla risorsa QnA Maker. In genere, i nomi sono gli stessi.

     ![Selezionare il servizio app QnA Maker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. L'URL dell'endpoint è disponibile nella sezione Panoramica

    ![Endpoint QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>Gestione della knowledge base

<details>
<summary><b>Ho eliminato accidentalmente una parte del mio QnA Maker, cosa devo fare?</b></summary>

**Risposta:** Non eliminare i servizi di Azure creati insieme alla risorsa QnA Maker come la ricerca o l'app Web. Per il corretto funzionamento di QnA Maker, è necessario eliminarne uno QnA Maker smette di funzionare correttamente.

Tutte le eliminazioni sono permanenti, incluse le coppie di domande e risposte, i file, gli URL, le domande e risposte personalizzate, le knowledge base e le risorse di Azure. Prima di eliminare qualsiasi parte della knowledge base, quindi, assicurarsi di esportare le knowledge base dalla pagina **Impostazioni**.

</details>

<details>
<summary><b>Perché gli URL o i/file non vengono estratti da coppie di domande e risposte?</b></summary>

**Risposta:** È possibile che QnA Maker non possa estrarre automaticamente alcune coppie di domanda/riposta da URL di domande frequenti validi. In questi casi è possibile incollare il contenuto di domande/risposte in un file TXT e verificare se lo strumento è in grado di inglobarlo. In alternativa è possibile aggiungere contenuti alla Knowledge Base in modo editoriale tramite il [portale QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Quali sono le dimensioni di una Knowledge base che è possibile creare?</b></summary>

**Risposta:** Le dimensioni della Knowledge Base dipendono dallo SKU di Ricerca di Azure scelto durante la creazione del servizio QnA Maker. Vedere [qui](./Tutorials/choosing-capacity-qnamaker-deployment.md) per altri dettagli.

</details>

<details>
<summary><b>Perché non è possibile visualizzare nulla nell'elenco a discesa quando si tenta di creare una nuova Knowledge base?</b></summary>

**Risposta:** Non è stato ancora creato alcun servizio QnA Maker in Azure. Per altre informazioni su come eseguire questa operazione, leggere [qui](./How-To/set-up-qnamaker-service-azure.md).

</details>

<details>
<summary><b>Ricerca per categorie condividere una Knowledge base con altri utenti?</b></summary>

**Risposta:** La condivisione funziona a livello di servizio QnA Maker, in altre parole vengono condivise tutte le Knowledge Base all'interno di un servizio. Vedere [qui](./How-To/collaborate-knowledge-base.md) per informazioni su come collaborare a una Knowledge Base.

</details>

<details>
<summary><b>È possibile condividere una Knowledge base con un collaboratore che non si trova nello stesso tenant di AAD per modificare una Knowledge base?</b></summary>

**Risposta:** La condivisione è basata sul controllo degli accessi in base al ruolo di Azure. Se è possibile condividere _qualsiasi_ risorsa in Azure con un altro utente, è anche possibile condividere QnA Maker.

</details>

<details>
<summary><b>se si dispone di un piano di servizio app con 5 Knowledge base QnAMaker. È possibile assegnare diritti di lettura/scrittura a 5 utenti diversi, in modo che ognuno di essi possa accedere solo a 1 QnAMaker Knowledge base?</b></summary>

**Risposta:** È possibile condividere un intero servizio QnAMaker, non i singoli knowledge base.

</details>

<details>
<summary><b>Come è possibile modificare il messaggio predefinito quando non viene trovata una corrispondenza corretta?</b></summary>

**Risposta:** Il messaggio predefinito fa parte delle impostazioni del servizio app.
- Passare alla risorsa del servizio app nel portale di Azure

![Servizio app QnA Maker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Fare clic sull'opzione **Impostazioni**

![Impostazioni del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Modificare il valore dell'impostazione **DefaultAnswer**
- Riavviare il servizio app

![Riavvio del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Perché il collegamento di SharePoint non viene estratto?</b></summary>

**Risposta:** Per altre informazioni, vedere [Posizioni della data factory](./Concepts/knowledge-base.md#data-source-locations).

</details>

<details>
<summary><b>gli aggiornamenti apportati alla Knowledge base non si riflettono sulla pubblicazione. Perché?</b></summary>

**Risposta:** Ogni operazione di modifica, nell'aggiornamento di tabelle, nel test o nell'impostazione, deve essere salvata prima di poter essere pubblicata. Assicurarsi di fare clic sul pulsante **Salva e Train** dopo ogni operazione di modifica.

</details>

<details>
<summary><b>La Knowledge base supporta dati avanzati o multimediali?</b></summary>

**Risposta:**

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Estrazione automatica multimediale per file e URL

* Funzionalità di conversione da HTML a Markdown limitata agli URL.
* File: non supportato

#### <a name="answer-text-in-markdown"></a>Testo della risposta in Markdown
Una volta che i set di QnA sono nella Knowledge base, è possibile modificare il testo Markdown di una risposta per includere i collegamenti ai supporti disponibili da URL pubblici.


</details>

<details>
<summary><b>QnA Maker supportano le lingue diverse dall'inglese?</b></summary>

**Risposta:** Vedere altri dettagli sulle [lingue supportate](./Overview/languages-supported.md).

Se sono presenti contenuti in più lingue, assicurati di creare un servizio separato per ogni lingua.

</details>

## <a name="manage-service"></a>Gestire il servizio

<details>
<summary><b>Quando è necessario riavviare il servizio app?</b></summary>

**Risposta:** Aggiornare il servizio app quando l'icona di attenzione è accanto al valore della versione per la Knowledge base nella tabella chiavi endpoint nella [pagina](https://www.qnamaker.ai/UserSettings) **impostazioni utente**.

</details>

<details>
<summary><b>ho eliminato il servizio di ricerca esistente. Come si può risolvere il problema?</b></summary>

**Risposta:** Se si elimina un indice ricerca cognitiva di Azure, l'operazione è finale e non è possibile recuperare l'indice.

</details>

<details>
<summary><b>ho eliminato il `testkb` indice nel servizio di ricerca. Come si può risolvere il problema?</b></summary>

**Risposta:** Non è possibile recuperare i dati precedenti. Creare una nuova risorsa di QnA Maker e creare di nuovo la Knowledge base.

</details>

<details>
<summary><b>Quando è necessario aggiornare le chiavi dell'endpoint?</b></summary>

**Risposta:** Aggiornare le chiavi endpoint se si sospetta che siano state compromesse.

</details>

<details>
<summary><b>È possibile usare la stessa risorsa ricerca cognitiva di Azure per le Knowledge base usando più lingue?</b></summary>

**Risposta:** Per usare più lingue e knowledge base, l'utente deve creare una risorsa di QnA Maker per ciascuna lingua. Verrà creato un servizio di ricerca di Azure separato per ogni lingua. La combinazione di knowledge base in lingue diverse in un singolo servizio di Ricerca di Azure comporterà una ridotta pertinenza dei risultati.

</details>

<details>
<summary><b>Come è possibile modificare il nome della risorsa ricerca cognitiva di Azure usata da QnA Maker?</b></summary>

**Risposta:** Il nome della risorsa ricerca cognitiva di Azure è il nome della risorsa QnA Maker con alcune lettere casuali aggiunte alla fine. La distinzione tra più risorse di ricerca per QnA Maker risulta quindi più difficile. Creare un servizio di ricerca separato (denominarlo come si desidera) e connetterlo al servizio QnA. I passaggi sono simili ai passaggi necessari per [aggiornare una ricerca di Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Quando QnA Maker restituisce `Runtime core is not initialized,` come risolvere il problema?</b></summary>

**Risposta:** Lo spazio su disco per il servizio app potrebbe essere pieno. Passaggi per la correzione dello spazio su disco:

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

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integrazione con altri servizi, inclusi Bot

<details>
<summary><b>È necessario usare bot Framework per usare QnA Maker?</b></summary>

**Risposta:** No, non è necessario usare [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) con QnA Maker. Tuttavia, QnA Maker è disponibile come uno dei diversi modelli nel [servizio Azure bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Il servizio Bot consente di sviluppare rapidamente bot intelligenti con Microsoft Bot Framework e viene eseguito in un ambiente senza server.

</details>

<details>
<summary><b>Come è possibile creare un nuovo bot con QnA Maker?</b></summary>

**Risposta:** Seguire le istruzioni riportate in [questa](./Tutorials/create-qna-bot.md) documentazione per creare il bot con il servizio Azure Bot.

</details>

<details>
<summary><b>Ricerca per categorie usare una Knowledge base diversa con un servizio Azure bot esistente?</b></summary>

**Risposta:** È necessario disporre delle informazioni seguenti sulla Knowledge Base:

* ID della Knowledge base.
* Nome del sottodominio personalizzato dell'endpoint pubblicato della Knowledge base, noto come `host`, disponibile nella pagina **Impostazioni** dopo la pubblicazione.
* Chiave endpoint pubblicata della Knowledge base, disponibile nella pagina **Impostazioni** dopo la pubblicazione.

Con queste informazioni, andare al servizio app del bot nella portale di Azure. In **Impostazioni-> configurazione > Impostazioni applicazione**modificare tali valori.

La chiave dell'endpoint della Knowledge base è denominata `QnAAuthkey` nel servizio ABS.

</details>

<details>
<summary><b>Due o più applicazioni client possono condividere una Knowledge base?</b></summary>

**Risposta:** Sì, la Knowledge base può essere sottoposta a query da un numero qualsiasi di client. Se la risposta dalla Knowledge base sembra essere lenta o timeout, provare ad aggiornare il livello di servizio per il servizio app associato alla Knowledge base.

</details>

<details>
<summary><b>Ricerca per categorie incorporare il servizio QnA Maker nel sito Web?</b></summary>

**Risposta:** Seguire questa procedura per incorporare il servizio QnA Maker come controllo di chat nel sito Web:

1. Creare il bot di domande frequenti seguendo [queste istruzioni](./Tutorials/create-qna-bot.md).
2. Abilitare la chat Web seguendo [questi](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) passaggi

</details>

## <a name="data-storage"></a>Archiviazione dati

<details>
<summary><b>Quali dati vengono archiviati e dove vengono archiviati?</b></summary>

**Risposta:**

Quando si crea un servizio QnA Maker, si seleziona un'area di Azure. Le knowledge base e i file di log vengono archiviati in questa area.

</details>