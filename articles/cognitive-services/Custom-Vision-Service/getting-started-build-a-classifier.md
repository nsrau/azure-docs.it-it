---
title: Creare un classificatore con il servizio Visione personalizzata - Servizi cognitivi di Azure | Microsoft Docs
description: Imparare a usare il servizio Visione personalizzata per creare un classificatore per distinguere oggetti nelle fotografie.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 6dc271c13f53a445c7d1101f5264d890208bd03c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374772"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Come creare un classificatore con Visione personalizzata

Per usare il servizio Visione personalizzata, è prima di tutto necessario creare un classificatore. Questo documento illustra come creare un classificatore tramite il web browser.

## <a name="prerequisites"></a>prerequisiti

Per creare un classificatore, è prima di tutto necessario:

- Un [account Microsoft](https://account.microsoft.com/account) valido o un OrgID di Azure Active Directory ("account aziendale o dell'istituto di istruzione"), in modo da poter accedere a customvision.ai e iniziare.

    > [!IMPORTANT] 
    > L'account di accesso OrgID per gli utenti di Azure Active Directory (Azure AD) dai [cloud nazionali](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) non è attualmente supportato.

- Una serie di immagini per il training del classificatore (con un minimo di 30 immagini per ogni tag).

- Alcune immagini per testare il classificatore dopo averne eseguito il training.

- Facoltativo: una sottoscrizione di Azure associata all'account Microsoft o all'OrgID. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

    > [!IMPORTANT]
    > Senza una sottoscrizione di Azure, sarà solo possibile creare progetti di __valutazione limitata__. Se è disponibile una sottoscrizione di Azure, verrà richiesto di creare risorse di training e previsione del servizio Visione personalizzata nel [portale di Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision) durante la creazione del progetto.   

## <a name="create-a-new-project"></a>Creare un nuovo progetto

Per creare un nuovo progetto, usare questa procedura:

1. Nel Web browser passare alla [pagina web Visione personalizzata](https://customvision.ai). Selezionare __Sign in__ (Accedi) per iniziare a usare il servizio.

    ![Immagine della pagina di accesso](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > Dopo l'accesso al servizio Visione personalizzata viene visualizzato un elenco di progetti. Con l'eccezione dei due progetti per "valutazione limitata", ai progetti è associata una risorsa di Azure. Gli utenti Azure potranno visualizzare tutti i progetti associati alle [risorse di Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources) a cui hanno accesso. 

2. Per creare il primo progetto, selezionare **New Project** (Nuovo progetto). Per il primo progetto viene richiesto di accettare le condizioni del servizio. Selezionare la casella di controllo e quindi selezionare il pulsante **I agree** (Accetto). Verrà visualizzata la finestra di dialogo **New project** (Nuovo progetto).

    ![La finestra di dialogo New Project (Nuovo progetto) include campi per nome, descrizione e domini.](./media/getting-started-build-a-classifier/new-project.png)

3. Immettere un nome e una descrizione per il progetto. Selezionare quindi uno dei domini disponibili. Ogni dominio ottimizza il classificatore per tipi specifici di immagini, come descritto nella tabella seguente:

    |Domain|Scopo|
    |---|---|
    |__Domande generiche__| Ottimizzato per un'ampia gamma di attività di classificazione di immagini. Se nessuno degli altri domini risulta appropriato o si è in dubbio sul dominio da scegliere, selezionare il dominio generico. |
    |__Food__ (Cibo)|Ottimizzato per fotografie di piatti come nel menù di un ristorante. Se si vogliono classificare fotografie di singoli frutti o verdure, usare il dominio Food (Cibo).|
    |__Landmarks__ (Luoghi di interesse)|Ottimizzato per i luoghi di interesse riconoscibili, sia naturali che artificiali. Il dominio offre i migliori risultati quando il luogo di interesse è chiaramente visibile nella fotografia. Il dominio è efficace anche se il luogo è leggermente nascosto da utenti posti davanti.|
    |__Retail__ (Vendita)|Ottimizzato per le immagini che si trovano in un catalogo di vendita o in un sito Web di vendita. Se si vogliono classificare con alta precisione vestiti, pantaloni e magliette o camicie, usare questo dominio.|
    |__Adult__ (Adulti)|Ottimizzato per distinguere più in dettaglio contenuto per adulti e contenuto per non adulti. Ad esempio, se si vogliono bloccare le immagini che ritraggono persone in costume da bagno, questo dominio consente di creare appositamente un classificatore personalizzato.|
    |__Domini compatti__| Ottimizzati per i vincoli di classificazione in tempo reale su dispositivi mobili. I modelli generati da domini compatti possono essere esportati per l'esecuzione in locale.|

    Se si vuole, è possibile modificare il dominio in un secondo momento.

4. Selezionare un gruppo di risorse. L'elenco a discesa Resource Group (Gruppo di risorse) mostra tutti i gruppi di risorse di Azure che includono una risorsa del servizio Visione personalizzata. È anche possibile selezionare __limited trial__ (valutazione limitata). La voce valutazione limitata è l'unico gruppo di risorse che potrà scegliere un utente senza sottoscrizione di Azure.

    Per creare il progetto, selezionare __Create project__ (Crea progetto).

## <a name="upload-and-tag-images"></a>Caricare e contrassegnare le immagini

1. Per aggiungere immagini al classificatore, usare il pulsante __Add images__ (Aggiungi immagini) e quindi selezionare __Browse local files__ (Sfoglia file locali). Selezionare __Open__ (Apri) per passare all'assegnazione di tag.

    > [!TIP]
    > Dopo aver selezionato le immagini, è necessario contrassegnarle. Il tag viene applicato al gruppo di immagini che si è scelto di caricare, pertanto potrebbe essere più facile caricare le immagini in base ai tag che si prevede di usare. È anche possibile modificare il tag per le immagini selezionate dopo averle contrassegnate e caricate.

    > [!TIP]
    > Caricare immagini con diverse angolazioni, illuminazione, sfondi, tipi, stili, gruppi, dimensioni e così via. Usare un'ampia gamma di tipi di foto per evitare distorsioni del classificatore e ottenere una buona generalizzazione.

    Il servizio Visione personalizzata accetta immagini di training in formato JPG, PNG e BMP, con dimensioni fino a 6 MB per ogni immagine. (Le immagini per la previsione possono avere dimensioni fino a 4 MB per ogni immagine.) È consigliabile che le immagini abbiano dimensioni di 256 pixel sul bordo più corto. Tutte le immagini con dimensioni minori di 256 pixel sul bordo più corto vengono ridimensionate dal servizio Visione personalizzata.

    ![Il controllo per l'aggiunta di immagini è visualizzato in alto a sinistra e come pulsante in basso al centro.](./media/getting-started-build-a-classifier/add-images01.png)

    ![Il pulsante per la selezione di file locali è visualizzato in basso al centro.](./media/getting-started-build-a-classifier/add-images02.png)

    >[!NOTE] 
    > È possibile usare l'API REST per caricare immagini di training da URL.

2. Per impostare il tag, immettere il testo nel campo __My Tags__ (Tag personali) e quindi usare il pulsante __+__. Per caricare le immagini e contrassegnarle, usare il pulsante __Upload [number] files__ (Carica [numero] file). È possibile aggiungere più di un tag alle immagini. 

    > [!NOTE]
    > Il tempo di caricamento varia in base al numero e alle dimensioni delle immagini selezionate.

    ![Immagine della pagina per l'aggiunta dei tag e il caricamento](./media/getting-started-build-a-classifier/add-images03.png)

3. Selezionare __Done__ (Fine) al termine del caricamento delle immagini.

    ![L'indicatore di stato mostra tutte le attività completate.](./media/getting-started-build-a-classifier/add-images04.png)

4. Per caricare un altro set di immagini, tornare al passaggio 1. Ad esempio, se si vuole distinguere tra cani e pony, caricare e contrassegnare immagini di pony.

## <a name="train-and-evaluate-the-classifier"></a>Eseguire il training e valutare il classificatore

Per eseguire il training del classificatore, selezionare il pulsante **Train** (Esegui training).

![Il pulsante per eseguire il training è disponibile in alto a destra nella finestra del browser.](./media/getting-started-build-a-classifier/train01.png)

Per il training del classificatore servono pochi minuti. Durante questo periodo, vengono visualizzate informazioni sul processo di training.

![Il pulsante per eseguire il training è disponibile in alto a destra nella finestra del browser.](./media/getting-started-build-a-classifier/train02.png)

Dopo il training, viene visualizzata la scheda __Performance__ (Prestazioni). Gli indicatori di precisione e richiamo indicano l'efficacia del classificatore, in base a test automatici. Il servizio Visione personalizzata usa le immagini inviate per il training per calcolare questi numeri, tramite un processo noto come [convalida incrociata k-fold](https://en.wikipedia.org/wiki/Cross-validation_(statistics)).

![I risultati di training mostrano la precisione e il richiamo complessivi, oltre alla precisione e al richiamo per ogni tag nella funzione di classificazione.](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> Ogni volta che si seleziona il pulsante **Train** (Esegui il training), si crea una nuova iterazione del classificatore. È possibile visualizzare tutte le iterazioni precedenti nella scheda **Performance** (Prestazioni) ed è possibile eliminare eventuali iterazioni obsolete. Quando si elimina un'iterazione, vengono eliminate anche tutte le immagini associate in modo univoco a tale iterazione.

Il classificatore usa tutte le immagini per creare un modello che identifica ogni tag. Per testare la qualità del modello, il classificatore prova ogni immagine nel modello per vedere ciò che trova il modello.

Vengono visualizzate le qualità dei risultati del classificatore.

|Termine|Definizione|
|---|---|
|__Precisione__|Quando si classifica un'immagine, quante probabilità ha il classificatore di classificare l'immagine correttamente? Di tutte le immagini usate per il training del classificatore (cani e pony), in che percentuale il modello ha ottenuto il risultato corretto? 99 tag corretti su 100 immagini rappresenta una precisione del 99%.|
|__Richiamo__|Di tutte le immagini da classificare, quante sono state identificate correttamente dal classificatore? Un richiamo del 100% significa che se sono presenti 38 immagini di cani nelle immagini usate per il training del classificatore, il classificatore ha trovato 38 cani.|

## <a name="next-steps"></a>Passaggi successivi

[Testare il modello e ripeterne il training](test-your-model.md)

