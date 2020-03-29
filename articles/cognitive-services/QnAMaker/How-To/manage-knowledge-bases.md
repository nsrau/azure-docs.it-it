---
title: Gestire le basi di conoscenza - QnA Maker
description: QnA Maker consente di gestire le proprie basi di conoscenza fornendo l'accesso alle impostazioni e ai contenuti della Knowledge Base.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 57839125011016daed5f0b3d441a83e8db488198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071144"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Creare una Knowledge Base e gestire le impostazioni

QnA Maker consente di gestire le basi di conoscenza fornendo l'accesso alle impostazioni della Knowledge Base e alle origini dati.

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
> * Risorsa QnA Maker creata nel portale di Azure.A [QnA Maker resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) created in the Azure portal. Prendere nota dei valori di ID di Azure Active Directory, sottoscrizione e nome della risorsa di QnA selezionati durante la creazione della risorsa.

## <a name="create-a-knowledge-base"></a>Creare una knowledge base

1. Accedere al portale [QnAMaker.ai](https://QnAMaker.ai) con le credenziali di Azure.

1. Nel portale di QnA Maker selezionare **Creare una knowledge base**.

1. Nella pagina **Crea** ignorare il **Passaggio 1** se la risorsa QnA Maker è già presente.

    Se la risorsa non è ancora stata creata, selezionare **Creare un servizio QnA**. Si verrà reindirizzati al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) per configurare un servizio QnA Maker nella sottoscrizione. Prendere nota dei valori di ID di Azure Active Directory, sottoscrizione e nome della risorsa di QnA selezionati durante la creazione della risorsa.

    Al termine della creazione della risorsa nel portale di Azure, tornare al portale di QnA Maker, aggiornare la pagina del browser e continuare con il **Passaggio 2**.

1. Nel **Passaggio 3** selezionare l'istanza di Active Directory, la sottoscrizione, il servizio (risorsa) e la lingua per tutte le knowledge base create nel servizio.

   ![Screenshot della selezione di una knowledge base per il servizio QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Nel **passaggio 3**assegnare alla Knowledge Base `My Sample QnA KB`il nome .

1. Nel **Passaggio 4** configurare le impostazioni in base alla tabella seguente:

    |Impostazione|valore|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files** (Abilita estrazione a più turni da URL e file PDF o DOCX)|Selezionato|
    |**Default answer text** (Testo della risposta predefinita)| `Quickstart - default answer not found.`|
    |**+ Add URL** (+ Aggiungi URL)|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Selezionare **Professional**|

1. Nel **Passaggio 5** selezionare **Create your KB** (Crea la KB).

    Il processo di estrazione impiega alcuni minuti per leggere il documento e identificare le domande e risposte.

    Al termine della creazione della knowledge base, verrà visualizzata la pagina **Knowledge base**. È possibile modificare il contenuto della knowledge base in questa pagina.

## <a name="edit-knowledge-base"></a>Modificare la knowledge base

1.  Selezionare **My Knowledge Bases** (Knowledge Base personali) nella barra di spostamento in alto.

       È possibile visualizzare tutti i servizi creati o condivisi con l'utente disposti in ordine decrescente in base alla data dell'**ultima modifica**.

       ![Knowledge Base personali](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selezionare una Knowledge Base per modificarla.

1.  Selezionare **Impostazioni**. L'elenco seguente contiene campi che è possibile modificare.

       |Obiettivo|Azione|
       |--|--|
       |Aggiungere URL|È possibile aggiungere nuovi URL per aggiungere nuovi contenuti delle domande frequenti alla knowledge base facendo clic sul collegamento **Gestisci Knowledge base -> "+ Aggiungi URL"**.|
       |Eliminare URL|È possibile eliminare gli URL esistenti selezionando l'icona di eliminazione (cestino).|
       |Aggiornare il contenuto|Se si vuole che la knowledge base esegua una ricerca per indicizzazione del contenuto più recente degli URL esistenti, selezionare la casella di controllo **Aggiorna**. In questo modo la Knowledge Base verrà aggiornata con il contenuto dell'URL più recente una sola volta. Non si tratta di impostare una pianificazione regolare degli aggiornamenti.|
       |Aggiungere file|È possibile aggiungere un documento supportato come parte di una knowledge base selezionando **Gestisci Knowledge base** e quindi **+ Aggiungi file**.|
    |Importa|È inoltre possibile importare qualsiasi Knowledge Base esistente selezionando il pulsante **Importa Knowledge Base.** |
    |Aggiornamento|L'aggiornamento della Knowledge base dipende dal **piano tariffario di gestione** usato durante la creazione del servizio QnA Maker associato alla knowledge base. Se necessario, è anche possibile aggiornare il livello di gestione dal portale di Azure.You can also update the management tier from Azure portal if necessary.

  1. Dopo aver apportato le modifiche alla Knowledge Base, selezionare **Salva e esegui il training** nell'angolo superiore destro della pagina per rendere persistenti le modifiche.

       ![Save and train (Salva ed esegui training)](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Se si lascia la pagina prima di selezionare **Save and train** (Salva ed esegui training), tutte le modifiche andranno perse.



## <a name="manage-large-knowledge-bases"></a>Gestire Knowledge Base di grandi dimensioni

* **Gruppi di origini dati:** le Qna vengono raggruppate in base all'origine dati da cui sono state estratte. È possibile espandere o comprimere l'origine dati.

    ![Usare la barra di origine dati di QnA Maker per comprimere ed espandere le risposte e le domande sull'origine dati](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Ricerca nella Knowledge Base**: È possibile cercare nella Knowledge Base digitando nella casella di testo nella parte superiore della tabella della Knowledge Base. Premere INVIO per eseguire la ricerca nelle domande, nelle risposte o nei metadati. Fare clic sull'icona X per rimuovere il filtro di ricerca.

    ![Usare la casella di ricerca di QnA Maker sopra alle domande e risposte, per limitare la visualizzazione solo agli elementi corrispondenti al filtro](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginazione**: Spostarsi rapidamente tra le origini dati per gestire grandi knowledge base

    ![Usare le funzionalità di paginazione QnA Maker sopra alle domande e risposte, per spostarsi tra le pagine di domande e risposte](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Eliminare le knowledge base

L'eliminazione di una knowledge base (KB) è un'operazione definitiva. Non può essere modificata. Prima di eliminare una knowledge base è necessario esportarla dalla pagina **Impostazioni** del portale di QnA Maker.

Se si condivide la Knowledge Base con i collaboratori,](base.md di conoscenza-collaborazione), quindi eliminarla, tutti perdono l'accesso alla KB.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [gestione della lingua](language-knowledge-base.md) di tutte le Knowledge Base in una risorsa.

* Modifica set QnA
* Gestire le risorse di Azure usate da QnA MakerManage Azure resources used by QnA Maker