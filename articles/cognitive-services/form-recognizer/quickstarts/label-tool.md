---
title: 'Guida introduttiva: Etichettare moduli, eseguire il training di un modello e analizzare un modulo usando lo strumento di etichettatura campioni - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si userà lo strumento di etichettatura campioni di Riconoscimento modulo per etichettare manualmente documenti modulo. Con i documenti etichettati, verrà quindi eseguito il training di un modello personalizzato, che verrà usato per estrarre coppie chiave-valore.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 301b68d0dfaeef6d5cfdd4d7a5a504794ac877f4
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205824"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Eseguire il training di un modello di Riconoscimento modulo con le etichette usando lo strumento di etichettatura campioni

In questo argomento di avvio rapido si userà l'API REST Riconoscimento modulo con lo strumento di etichettatura campioni per eseguire il training di un modello personalizzato con dati etichettati manualmente. Per altre informazioni su questa funzionalità, vedere la sezione [Eseguire il training con le etichette](../overview.md#train-with-labels) della panoramica.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:

- Un set di almeno sei moduli dello stesso tipo. Questi dati verranno usati per eseguire il training del modello e testare un modulo. Per questa guida di avvio rapido, è possibile usare un [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451). Caricare i file di training nella radice di un contenitore di archiviazione BLOB in un account di archiviazione di Azure.

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>Configurare lo strumento di etichettatura campioni

Per eseguire lo strumento di etichettatura campioni, verrà usato il motore Docker. Per configurare il contenitore Docker, seguire questa procedura. Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).
1. Installare prima di tutto Docker in un computer host. Questa guida illustra come usare un computer locale come host. Se si vuole usare un servizio di hosting Docker in Azure, vedere la guida pratica [Distribuire lo strumento di etichettatura di esempio](../deploy-label-tool.md). 

   Il computer host deve soddisfare i requisiti hardware seguenti:

    | Contenitore | Minima | Consigliato|
    |:--|:--|:--|
    |Strumento di etichettatura campioni|2 core, 4 GB di memoria|4 core, 8 GB di memoria|

   Installare Docker nel computer seguendo le istruzioni appropriate per il sistema operativo in uso: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/).

1. Ottenere il contenitore dello strumento di etichettatura campioni con il comando `docker pull`.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Ora è possibile eseguire il contenitore con `docker run`.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Questo comando rende disponibile lo strumento di etichettatura campioni tramite un Web browser. Passare a [http://localhost:3000](http://localhost:3000).

> [!NOTE]
> È anche possibile etichettare i documenti ed eseguire il training dei modelli usando l'API REST Riconoscimento modulo. Per eseguire il training e l'analisi con l'API REST, vedere [Eseguire il training con le etichette usando l'API REST e Python](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Configurare i dati di input

Prima di tutto, assicurarsi che tutti i documenti di training abbiano lo stesso formato. Se si usano moduli in più formati, organizzarli in sottocartelle basate sul formato comune. Quando si esegue il training, è necessario indirizzare l'API a una sottocartella.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Configurare la condivisione di risorse tra domini (CORS)

Abilitare CORS nell'account di archiviazione. Selezionare l'account di archiviazione nel portale di Azure e fare clic sulla scheda **CORS** nel riquadro sinistro. Nella riga inferiore inserire i valori seguenti. Quindi fare clic su **Salva** nella parte superiore.

* Origini consentite = * 
* Metodi consentiti = \[seleziona tutto\]
* Intestazioni consentite = *
* Intestazioni esposte = * 
* Età massima = 200

> [!div class="mx-imgBorder"]
> ![Configurazione di CORS nel portale di Azure](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Connettere lo strumento di etichettatura campioni

Lo strumento di etichettatura campioni si connette a un'origine (in cui si trovano i moduli originali) e a una destinazione (in cui esporta le etichette create e i dati di output).

Le connessioni possono essere configurate e condivise tra progetti. Si basano su un modello di provider estendibile, quindi è possibile aggiungere facilmente nuovi provider di origine/destinazione.

Per creare una nuova connessione, fare clic sull'icona **Nuove connessioni** (spina) sulla barra di spostamento sinistra.

Compilare i campi con i valori seguenti:

* **Nome visualizzato**: il nome visualizzato della connessione.
* **Descrizione**: la descrizione del progetto.
* **URL di firma di accesso condiviso**: l'URL di firma di accesso condiviso del contenitore di archiviazione BLOB di Azure. Per recuperare l'URL SAS, aprire Microsoft Azure Storage Explorer, fare clic con il pulsante destro del mouse sul contenitore e scegliere **Ottieni firma di accesso condiviso**. Impostare la scadenza su un'ora successiva a quella in cui verrà terminato l'uso del servizio. Assicurarsi che le autorizzazioni **Lettura**, **Scrittura**, **Eliminazione** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

![Impostazioni di connessione dello strumento di etichettatura campioni](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Creare un nuovo progetto

Nello strumento di etichettatura campioni vengono archiviate le configurazioni e le impostazioni dei progetti. Creare un nuovo progetto e compilare i campi con i valori seguenti:

* **Nome visualizzato**: il nome visualizzato del progetto
* **Token di sicurezza**: alcuni progetti possono includere valori sensibili, ad esempio chiavi di API o altri segreti condivisi. Ogni progetto genererà un token di sicurezza che è possibile usare per crittografare/decrittografare le impostazioni di progetti sensibili. Per trovare i token di sicurezza in Impostazioni applicazione, fare clic sull'icona dell'ingranaggio nell'angolo in basso della barra di spostamento sinistra.
* **Connessione protetta**: la connessione ad archiviazione BLOB di Azure creata nel passaggio precedente che si vuole usare per questo progetto.
* **Percorso cartella** (facoltativo): se i moduli di origine si trovano in una cartella del contenitore BLOB, specificare qui il relativo nome
* **URI del servizio Riconoscimento modulo**: l'URL dell'endpoint di Riconoscimento modulo.
* **Chiave API**: la chiave di sottoscrizione di Riconoscimento modulo.
* **Descrizione** (facoltativo): la descrizione del progetto

![Pagina di nuovo progetto nello strumento di etichettatura campioni](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Etichettare i moduli

Quando si crea o si apre un progetto, viene visualizzata la finestra principale dell'editor di tag. L'editor di tag è costituito da tre parti:

* Un riquadro di anteprima ridimensionabile che contiene un elenco scorrevole di moduli provenienti dalla connessione di origine.
* Il riquadro principale dell'editor che consente di applicare tag.
* Il riquadro dell'editor di tag che consente di modificare, bloccare, riordinare ed eliminare tag. 

### <a name="identify-text-elements"></a>Identificare gli elementi di testo

Fare clic su **Run OCR on all files** (Esegui OCR su tutti i file) nel riquadro sinistro per ottenere informazioni sul layout del testo per ogni documento. Lo strumento di etichettatura traccerà rettangoli di selezione intorno a ogni elemento di testo.

### <a name="apply-labels-to-text"></a>Applicare le etichette al testo

Creare quindi i tag (etichette) e applicarli agli elementi di testo che dovranno essere riconosciuti dal modello.

1. Usare prima di tutto il riquadro dell'editor di tag per creare i tag da identificare.
  1. Fare clic su **+** per creare un nuovo tag.
  1. Immettere il nome del tag.
  1. Premere INVIO per salvare il tag.
1. Nell'editor principale fare clic e trascinare una o più parole dagli elementi di testo evidenziati per selezionarle.
1. Fare clic sul tag da applicare oppure premere il tasto corrispondente della tastiera. I tasti numerici vengono assegnati come tasti di scelta rapida per i primi 10 tag. È possibile riordinare i tag usando le icone delle frecce su e giù nel riquadro dell'editor di tag.
    > [!Tip]
    > Per l'etichettatura dei moduli, tenere presenti i suggerimenti seguenti.
    > * È possibile applicare un unico tag a ogni elemento di testo selezionato.
    > * Ogni tag può essere applicato una sola volta per pagina. Se un valore viene visualizzato più volte nella stessa pagina, creare tag diversi per ogni istanza. Ad esempio: "invoice# 1", "invoice# 2" e così via.
    > * I tag non possono estendersi in più pagine.
    > * Etichettare i valori così come appaiono nel modulo. Non provare a dividere un valore in due parti con due tag diversi. Ad esempio, un campo di indirizzo deve essere etichettato con un singolo tag anche se si estende su più righe.
    > * Non includere le chiavi nei campi etichettati, ma solo i valori.
    > * I dati delle tabelle dovrebbero essere rilevati automaticamente e saranno disponibili nel file JSON finale di output. Tuttavia, se il modello non riesce a rilevare tutti i dati di una tabella, è anche possibile etichettare manualmente questi campi. Assegnare un'etichetta diversa a ogni cella della tabella. Se i moduli includono tabelle con un numero variabile di righe, assicurarsi di etichettare almeno un modulo con la tabella più grande possibile.


Seguire la procedura descritta sopra per etichettare cinque moduli, quindi procedere con il passaggio successivo.

![Finestra dell'editor principale dello strumento di etichettatura campioni](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>Eseguire il training di un modello personalizzato

Fare clic sull'icona del training (il vagone di treno) nel riquadro sinistro per aprire la pagina corrispondente. Quindi fare clic sul pulsante **Train** per iniziare il training del modello. Al termine del processo di training, verranno visualizzate le informazioni seguenti:

* **ID modello**: l'ID del modello creato e sottoposto a training. Ogni chiamata al training crea un nuovo modello con un proprio ID. Copiare questa stringa in un posto sicuro, perché sarà necessaria per eseguire le chiamate di previsione tramite l'API REST.
* **Average Accuracy** (Accuratezza media): l'accuratezza media del modello. È possibile migliorare l'accuratezza del modello etichettando altri moduli ed eseguendo di nuovo il training per creare un nuovo modello. Per iniziare, è consigliabile etichettare cinque moduli ed aggiungerne altri se necessario.
* L'elenco dei tag e l'accuratezza stimata per ognuno.

![visualizzazione del training](../media/label-tool/train-screen.png)

Al termine del training, esaminare il valore di **Average Accuracy** (Accuratezza media). Se è basso, è necessario aggiungere altri documenti di input e ripetere i passaggi precedenti. I documenti già etichettati rimarranno nell'indice del progetto.

> [!TIP]
> È anche possibile eseguire il processo di training con una chiamata API REST. Per informazioni su come eseguire questa operazione, vedere [Eseguire il training con le etichette usando Python](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Analizzare un modulo

Fare clic sull'icona Previsioni (rettangoli) a sinistra per testare il modello. Caricare un documento modulo che non è stato usato nel processo di training. Fare quindi clic sul pulsante **Previsioni** a destra per ottenere le previsioni relative alle coppie chiave-valore per il modulo. Lo strumento applicherà i tag nei riquadri di selezione e segnalerà l'attendibilità di ognuno.

> [!TIP]
> È anche possibile eseguire l'API di analisi con una chiamata REST. Per informazioni su come eseguire questa operazione, vedere [Eseguire il training con le etichette usando Python](./python-labeled-data.md).

## <a name="improve-results"></a>Migliorare i risultati

A seconda dell'accuratezza indicata, è possibile scegliere di eseguire ulteriori processi di training per migliorare il modello. Dopo aver eseguito una previsione, esaminare i valori di attendibilità per ognuno dei tag applicati. Se il valore dell'accuratezza media del training è alto, ma i punteggi di attendibilità sono bassi (o i risultati non sono accurati), è consigliabile aggiungere il file usato per le previsioni nel set di training, etichettarlo e ripetere il training.

L'accuratezza media indicata, i punteggi di attendibilità e l'accuratezza effettiva possono essere incoerenti se i documenti analizzati sono diversi da quelli usati per il training. Tenere presente che alcuni documenti hanno un aspetto simile quando vengono visualizzati dalle persone ma sembrano diversi per il modello di intelligenza artificiale. Ad esempio, è possibile eseguire il training con un tipo di modulo con due varianti, in cui il set di training è costituito per il 20% dalla variante A e per l'80% dalla variante B. Durante la previsione, è probabile che i punteggi di attendibilità per i documenti della variante A siano inferiori.

## <a name="save-a-project-and-resume-later"></a>Salvare un progetto e riprenderlo in seguito

Per riprendere il progetto in un secondo momento o in un altro browser, è necessario salvare il relativo token di sicurezza e reimmetterlo in seguito. 

### <a name="get-project-credentials"></a>Ottenere le credenziali del progetto
Passare alla pagina delle impostazioni del progetto (icona del dispositivo di scorrimento) e prendere nota del nome del token di sicurezza. Passare quindi alle impostazioni dell'applicazione (icona dell'ingranaggio), che visualizzano tutti i token di sicurezza nell'istanza del browser corrente. Trovare il token di sicurezza del progetto e copiarne il nome e il valore della chiave in un posto sicuro.

### <a name="restore-project-credentials"></a>Ripristinare le credenziali del progetto
Quando si vuole riprendere il progetto, è prima di tutto necessario creare una connessione allo stesso contenitore di archiviazione BLOB. A questo scopo, ripetere la procedura descritta sopra. Quindi, passare alla pagina di impostazioni dell'applicazione (icona dell'ingranaggio) e verificare se il token di sicurezza del progetto è presente. Se non lo è, aggiungere un nuovo token di sicurezza e sovrascrivere il nome e la chiave del token del passaggio precedente. Quindi fare clic su Save settings (Salva impostazioni). 

### <a name="resume-a-project"></a>Riprendere un progetto
Infine, passare alla pagina principale (icona della casa) e fare clic su Open Cloud Project (Apri progetto cloud). Selezionare quindi la connessione all'archiviazione BLOB e il file con estensione *vott* del progetto. L'applicazione caricherà tutte le impostazioni del progetto perché contiene il token di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare lo strumento di etichettatura campioni di Riconoscimento modulo per eseguire il training di un modello con dati etichettati manualmente. Se si vuole integrare lo strumento di etichettatura nella propria applicazione, usare le API REST che gestiscono il training dei dati etichettati.

> [!div class="nextstepaction"]
> [Eseguire il training con le etichette usando Python](./python-labeled-data.md)
