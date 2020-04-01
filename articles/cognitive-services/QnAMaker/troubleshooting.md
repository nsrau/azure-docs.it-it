---
title: Risoluzione dei problemi - QnA Maker
description: L'elenco curato delle domande più frequenti riguardanti il servizio QnA Maker ti aiuterà ad adottare il servizio più velocemente e con risultati migliori.
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: 310cfe0cc8c1f647c09b8e0efb435a1ab326ff62
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474953"
---
# <a name="troubleshooting-for-qna-maker"></a>Risoluzione dei problemi per QnA Maker

L'elenco curato delle domande più frequenti riguardanti il servizio QnA Maker ti aiuterà ad adottare il servizio più velocemente e con risultati migliori.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Gestire le stime

<details>
<summary><b>Come è possibile migliorare le prestazioni di velocità effettiva per le stime delle query?</b></summary>

Risposta: I problemi di prestazioni della velocità effettiva indicano che è necessario aumentare le prestazioni sia per il servizio app che per la ricerca **cognitiva.** Valutare la possibilità di aggiungere una replica alla ricerca cognitiva per migliorare le prestazioni.

Ulteriori informazioni sui [piani tariffari](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Come ottenere l'endpoint del servizio QnAMaker</b></summary>

**Risposta:** L'endpoint del servizio QnAMaker è utile a scopo di debug quando si contatta il supporto QnAMaker o UserVoice. L'endpoint è un URL `https://your-resource-name.azurewebsites.net`nel formato seguente: .

1. Passare al servizio QnA Maker (gruppo di risorse) nel [portale di Azure](https://portal.azure.com)

    ![Gruppo di risorse Azure per QnA Maker nel portale di Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selezionare il servizio app associato alla risorsa QnA Maker. In genere, i nomi sono gli stessi.

     ![Selezionare il servizio app QnA Maker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. L'URL dell'endpoint è disponibile nella sezione Panoramica

    ![Endpoint QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>Gestione della knowledge base

<details>
<summary><b>Ho accidentalmente eliminato una parte di QnA Maker, cosa devo fare?</b></summary>

**Risposta:** non eliminare i servizi di Azure creati insieme alla risorsa QnA Maker, ad esempio Ricerca o App Web. Questi sono necessari per QnA Maker per funzionare, se si elimina uno, QnA Maker smetterà di funzionare correttamente.

Tutte le eliminazioni sono permanenti, incluse le coppie di domande e risposte, i file, gli URL, le domande e risposte personalizzate, le knowledge base e le risorse di Azure. Prima di eliminare qualsiasi parte della knowledge base, quindi, assicurarsi di esportare le knowledge base dalla pagina **Impostazioni**.

</details>

<details>
<summary><b>Perché i miei URL/file non estraggono coppie di domanda/risposta?</b></summary>

**Risposta**: È possibile che QnA Maker non sia in grado di estrarre automaticamente alcuni contenuti QnA (Question-and-Answer) da URL FAQ validi. In questi casi è possibile incollare il contenuto di domande/risposte in un file TXT e verificare se lo strumento è in grado di inglobarlo. In alternativa è possibile aggiungere contenuti alla Knowledge Base in modo editoriale tramite il [portale QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Quali sono le dimensioni massime consentite per la creazione di Knowledge Base?</b></summary>

**Risposta**: La dimensione della Knowledge Base dipende dallo SKU della ricerca di Azure scelto durante la creazione del servizio QnA Maker. Vedere [qui](./Tutorials/choosing-capacity-qnamaker-deployment.md) per altri dettagli.

</details>

<details>
<summary><b>Perché non vedo nulla nell'elenco a discesa quando cerco di creare una nuova Knowledge Base?</b></summary>

**Risposta:** non sono ancora stati creati servizi QnA Maker in Azure.Answer : You haven't created any QnA Maker services in Azure haven't created any QnA Maker services in Azure' Per altre informazioni su come eseguire questa operazione, leggere [qui](./How-To/set-up-qnamaker-service-azure.md).

</details>

<details>
<summary><b>Come si condivide una Knowledge Base?</b></summary>

**Risposta**: La condivisione funziona a livello di un servizio QnA Maker, ovvero tutte le knowledge base nel servizio verranno condivise. Vedere [qui](./How-To/collaborate-knowledge-base.md) per informazioni su come collaborare a una Knowledge Base.

</details>

<details>
<summary><b>È possibile condividere una knowledge base con un collaboratore che non è nello stesso tenant di AAD, per modificare una knowledge base?</b></summary>

**Risposta:** La condivisione è basata sul controllo degli accessi in base al ruolo di Azure.Answer : Sharing is based on Azure role-based access control (RBAC). Se è possibile condividere _qualsiasi_ risorsa in Azure con un altro utente, è anche possibile condividere QnA Maker.

</details>

<details>
<summary><b>Se si dispone di un piano di servizio app con 5 Knowledge Base QnAMaker. È possibile assegnare diritti di lettura/scrittura a 5 utenti diversi in modo che ognuno di essi possa accedere solo a 1 Knowledge Base QnAMaker?</b></summary>

**Risposta**: È possibile condividere un intero servizio QnAMaker, non singole basi di conoscenza.

</details>

<details>
<summary><b>Come si modifica il messaggio predefinito visualizzato se non viene trovata alcuna corrispondenza?</b></summary>

**Risposta:** il messaggio predefinito fa parte delle impostazioni nel servizio app.
- Passare alla risorsa del servizio app nel portale di Azure

![Servizio app QnA Maker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Fare clic sull'opzione **Impostazioni**

![Impostazioni del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Modificare il valore dell'impostazione **DefaultAnswer**
- Riavviare il servizio app

![Riavvio del servizio app QnA Maker](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Perché il collegamento SharePoint non viene estratto?</b></summary>

**Risposta**: Per ulteriori informazioni, vedere [Percorsi delle origini dati.](./Concepts/knowledge-base.md#data-source-locations)

</details>

<details>
<summary><b>Gli aggiornamenti apportati alla Knowledge Base non si riflettono sulla pubblicazione. Perché no?</b></summary>

**Risposta**: Ogni operazione di modifica, sia in un aggiornamento, un test o un'impostazione di tabella, deve essere salvata prima di poter essere pubblicata. Assicurarsi di fare clic sul pulsante **Salva e addestra** dopo ogni operazione di modifica.

</details>

<details>
<summary><b>La Knowledge Base supporta i dati avanzati o i contenuti multimediali?</b></summary>

**Risposta**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Estrazione automatica multimediale per file e URL

* URL: funzionalità di conversione da HTML a Markdown limitata.
* File - non supportati

#### <a name="answer-text-in-markdown"></a>Testo della risposta in markdown
Una volta che i set QnA si trovano nella Knowledge Base, è possibile modificare il testo markdown di una risposta per includere collegamenti a i file multimediali disponibili dagli URL pubblici.


</details>

<details>
<summary><b>QnA Maker supporta lingue diverse dall'inglese?</b></summary>

**Risposta**: Vedere ulteriori dettagli sulle [lingue supportate](./Overview/languages-supported.md).

Se sono presenti contenuti in più lingue, assicurarsi di creare un servizio separato per ciascuna lingua.

</details>

## <a name="manage-service"></a>Gestire il servizio

<details>
<summary><b>Quando è consigliabile riavviare un servizio app?</b></summary>

**Risposta:** aggiornare il servizio app quando l'icona di cautela è accanto al valore della versione per la Knowledge Base nella tabella **Chiavi endpoint** della [pagina](https://www.qnamaker.ai/UserSettings) **Impostazioni utente** .

</details>

<details>
<summary><b>Ho eliminato il mio servizio di ricerca esistente. Come posso risolvere questo problema?</b></summary>

**Risposta:** Se si elimina un indice di Ricerca cognitiva di Azure, l'operazione è finale e l'indice non può essere recuperato.

</details>

<details>
<summary><b>Ho eliminato `testkb` il mio indice nel mio servizio di ricerca. Come posso risolvere questo problema?</b></summary>

**Risposta**: I tuoi vecchi dati non possono essere recuperati. Creare una nuova risorsa QnA Maker e creare nuovamente la Knowledge Base.

</details>

<details>
<summary><b>Quando è necessario aggiornare le chiavi endpoint?</b></summary>

**Risposta**: Aggiornare le chiavi dell'endpoint se si sospetta che siano state compromesse.

</details>

<details>
<summary><b>È possibile usare la stessa risorsa Ricerca cognitiva di Azure per le Knowledge Base che usano più lingue?</b></summary>

**Risposta**: Per usare più lingue e più Knowledge Base, l'utente deve creare una risorsa QnA Maker per ogni lingua. Verrà creato un servizio di ricerca di Azure separato per ogni lingua. La combinazione di knowledge base in lingue diverse in un singolo servizio di Ricerca di Azure comporterà una ridotta pertinenza dei risultati.

</details>

<details>
<summary><b>Come è possibile modificare il nome della risorsa Ricerca cognitiva di Azure usata da QnA Maker?</b></summary>

**Risposta:** Il nome della risorsa Ricerca cognitiva di Azure è il nome della risorsa QnA Maker con alcune lettere casuali aggiunte alla fine. La distinzione tra più risorse di ricerca per QnA Maker risulta quindi più difficile. Creare un servizio di ricerca separato (denominandolo nel modo desiderato) e collegarlo al servizio QnA. I passaggi sono simili a quelli da eseguire per aggiornare una ricerca di [Azure.](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)

</details>

<details>
<summary><b>Quando QnA `Runtime core is not initialized,` Maker restituisce come posso risolvere il problema?</b></summary>

**Risposta:** lo spazio su disco per il servizio app potrebbe essere pieno. Passaggi per correggere lo spazio su disco:

1. Nel [portale di Azure](https://portal.azure.com)selezionare il servizio App di QnA Maker, quindi arrestare il servizio.
1. Mentre si è ancora nel servizio app, selezionare Strumenti di **sviluppo**, quindi **Strumenti avanzati**, quindi **Vai**. Si apre una nuova finestra del browser.
1. Selezionare **Console di debug**, quindi **CMD** per aprire uno strumento della riga di comando.
1. Passare alla directory _site/wwwroot/Data/QnAMaker/._
1. Rimuovere tutte le cartelle `rd`il cui nome inizia con .

    **Non eliminare** quanto segue:

    * KbIdToRankerMappings.txt file
    * File EndpointSettings.json
    * Cartella EndpointKeys

1. Avviare il servizio app.
1. Accedi alla tua knowledge base per verificare che funzioni ora.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integrazione con altri servizi, inclusi Bot

<details>
<summary><b>È necessario usare Bot Framework per usare QnA Maker?</b></summary>

**Risposta**: No, non è necessario utilizzare [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) con QnA Maker. Tuttavia, QnA Maker è offerto come uno dei diversi modelli nel servizio bot di Azure.However, QnA Maker is offered as one of several templates in [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Il servizio Bot consente di sviluppare rapidamente bot intelligenti con Microsoft Bot Framework e viene eseguito in un ambiente senza server.

</details>

<details>
<summary><b>Come posso creare un nuovo bot con QnA Maker?</b></summary>

**Risposta**: seguire le istruzioni in questa documentazione per creare il bot con il servizio Bot di Azure.Answer : Follow the instructions in [this](./Quickstarts/create-publish-knowledge-base.md) documentation to create your Bot with Azure Bot Service.

</details>

<details>
<summary><b>Come si usa una Knowledge Base diversa con un servizio bot di Azure esistente?</b></summary>

**Risposta**: È necessario disporre delle seguenti informazioni sulla Knowledge Base:

* ID della Knowledge Base.
* Nome sottodominio personalizzato dell'endpoint pubblicato `host`della Knowledge Base, noto come , disponibile nella pagina **Impostazioni** dopo la pubblicazione.
* Chiave endpoint pubblicata della Knowledge Base, disponibile nella pagina **Impostazioni** dopo la pubblicazione.

Con queste informazioni, passare al servizio app del bot nel portale di Azure.With this information, go to your bot's app service in the Azure portal. In **Impostazioni -> Configurazione -> Impostazioni applicazione**modificare tali valori.

La chiave dell'endpoint della `QnAAuthkey` Knowledge Base è etichettata nel servizio ABS.

</details>

<details>
<summary><b>Due o più applicazioni client possono condividere una Knowledge Base?</b></summary>

**Risposta**: Sì, è possibile eseguire query sulla Knowledge Base da un numero qualsiasi di client. Se la risposta dalla Knowledge Base sembra essere lenta o timeout, prendere in considerazione l'aggiornamento del livello di servizio per il servizio app associato alla Knowledge Base.

</details>

<details>
<summary><b>Come si incorpora il servizio QnA Maker nel proprio sito Web?</b></summary>

**Risposta**: Seguire questi passaggi per incorporare il servizio QnA Maker come controllo di chat Web nel sito Web:

1. Creare il bot di domande frequenti seguendo [queste istruzioni](./Quickstarts/create-publish-knowledge-base.md).
2. Abilitare la chat Web seguendo [questi](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) passaggi

</details>

## <a name="data-storage"></a>Archiviazione dei dati

<details>
<summary><b>Quali dati vengono archiviati e dove vengono archiviati?</b></summary>

**Risposta**:

Quando si crea un servizio QnA Maker, si seleziona un'area di Azure. Le knowledge base e i file di log vengono archiviati in questa area.

</details>