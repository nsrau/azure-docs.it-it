---
title: Creazione di un modello con l&quot;interfaccia utente di Recommendations | Microsoft Docs
description: Recommendations di Azure Machine Learning - Creazione di un modello con l&quot;interfaccia utente di Recommendations
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: b264fe44-f94e-40ae-9754-60ad7d6cfeb9
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5f98395e2beba671192e50f8e53c6198e1efed29


---
# <a name="building-a-model-with-the-recommendations-ui"></a>Creazione di un modello con l'interfaccia utente di Recommendations
Questo argomento costituisce una guida dettagliata. L'obiettivo è spiegare nel dettaglio i passaggi necessari per eseguire il training di un modello usando l'[interfaccia utente di Recommendations](https://recommendations-portal.azurewebsites.net/).
Lo svolgimento dell'esercizio consente di comprendere il processo di creazione di un modello prima di procedere a livello di programmazione. Permette inoltre di acquisire familiarità con l'interfaccia utente, il che è utile quando si inizia a usare il servizio.

Questo esercizio richiede circa 30 minuti.

<a name="Step1"></a>

## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Passaggio 1 - Accesso all'interfaccia utente di Recommendations
1. Se non è già stato fatto, è necessario [iscriversi](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) per ottenere una nuova sottoscrizione per l'[API Recommendations](https://www.microsoft.com/cognitive-services/en-us/recommendations-api). È possibile iscriversi al servizio in **Azure** all'indirizzo [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) e accedere con il proprio account Azure. Nella sezione *Attività 1* della [Guida introduttiva](cognitive-services-recommendations-quick-start.md) sono disponibili istruzioni dettagliate sul processo di iscrizione. 
2. Dopo aver ottenuto una **chiave** per la sottoscrizione all'API Recommendations, passare all'[interfaccia utente di Recommendations](https://recommendations-portal.azurewebsites.net/). 
3. Accedere al portale immettendo la chiave nel campo **Chiave account** e quindi fare clic sul pulsante **Accesso**.
   
    ![Interfaccia utente di Recommendations: finestra di accesso.][reco_signin]

<a name="Step2"></a>

## <a name="step-2---lets-gather-some-training-data"></a>Passaggio 2 - Raccolta di alcuni dati di training
Prima di creare una build, è necessario fornire al motore due informazioni: un file di catalogo e un set di file di dati di utilizzo. 

Il file di catalogo contiene informazioni sugli elementi che si offrono al cliente. Un file di dati di utilizzo contiene informazioni sulle modalità d'uso degli elementi o sulle transazioni dell'azienda.

Generalmente, per questo tipo di informazioni è necessario generare una query del database dell'archivio. Ai fini di questo esercizio vengono forniti alcuni dati di esempio che consentono di comprendere come usare l'API Recommendations.

È possibile scaricare i dati dal sito Web all'indirizzo [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copiare e decomprimere il file **Books.Zip** in una cartella del computer locale, ad esempio **c:\data**.

Informazioni dettagliate sullo schema dei file di catalogo e di dati di utilizzo sono disponibili nell'articolo [Raccolta dei dati per eseguire il training del modello](cognitive-services-recommendations-collecting-data.md).

In questo esercizio verrà utilizzato un file di piccole dimensioni, in modo che non sia necessario attendere a lungo per il training. Se si vuole provare un file più realistico, nello [stesso percorso](http://aka.ms/RecoSampleData) è disponibile anche il file **MsStoreData.zip**, contenente transazioni di esempio fornite da Microsoft Store.

<a name="Step3"></a>

## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Passaggio 3 - Creazione di un progetto e caricamento dei dati di catalogo e di utilizzo
Dopo l'accesso all'[interfaccia utente di Recommendations](https://recommendations-portal.azurewebsites.net/) viene visualizzata la pagina Projects (Progetti). Se in precedenza erano già stati creati dei progetti, questi sono visualizzati in tale pagina.
Un progetto, noto anche come *modello* nelle informazioni di riferimento sulle API, è un contenitore per i dati di catalogo e di utilizzo. È possibile creare diverse *build* all'interno del progetto. I passaggi successivi illustrano questo processo.

1. Per creare un nuovo progetto, digitarne il nome nella casella di testo (ad esempio "MyFirstModel") e fare clic su **Add Project** (Aggiungi progetto).
   
    ![Interfaccia utente di Recommendations: pagina Projects (Progetti).][reco_projects]
2. Dopo aver creato il progetto fare clic su **Browse for File** (Cerca file) nella sezione **Add a Catalog File** (Aggiungi file di catalogo). 
   Caricare il catalogo ottenuto al passaggio 2. Se il file è stato salvato in *c:\data*, è necessario spostarsi in tale cartella.
   
     ![Interfaccia utente di Recommendations: aggiunta di dati a un progetto.][reco_firstmodel]
3. Dopo aver caricato il catalogo fare clic su **Browse for File** (Cerca file) nella sezione **Add Usage Files** (Aggiungi file di dati di utilizzo). Aggiungere il file usage_large.txt.

> **Che cosa accade se si dispone di un file di dati di utilizzo di grandi dimensioni?**
> 
> È possibile caricare solo file di dati di utilizzo inferiori a 200 MB. Detto questo, il sistema può contenere fino a 2 GB di dati relativi alle transazioni. Di conseguenza, se necessario è possibile caricare più file.
> Non è necessaria una tale quantità di dati per creare un buon modello. Quello che conta, infatti, non sono le dimensioni ma la qualità dei dati. Nei dati di utilizzo è ovvio notare che la maggior parte delle transazioni riguarda pochi elementi comuni, mentre sono presenti scarse occorrenze di altri elementi.
> 
> 

<a name="Step4"></a>

## <a name="step-4---lets-do-some-training"></a>Passaggio 4 - Training
Dopo aver caricato sia i dati di catalogo che quelli di utilizzo, è possibile eseguire il training del motore in modo che possa apprendere modelli dai dati forniti.

1. Fare clic sul pulsante **New Build** (Nuova build).
2. Selezionare **Recommendations** (Raccomandazioni) come tipo di build. Si noti che sono supportate anche build di classificazione e build FBT (Frequently Bought Together).
   
   ![Interfaccia utente di Recommendations: finestra di dialogo Build.][reco_build_dialog.png]

    Una build FBT consente di identificare i modelli per prodotti che sono in genere acquistati/utilizzati nella stessa transazione.
    Una build di classificazione viene usata per identificare funzionalità di particolare interesse. 
    In questo workshop le build FBT o di classificazione non vengono trattate in modo approfondito. Per altre informazioni su queste build, vedere [Tipi di compilazione e qualità dei modelli](cognitive-services-recommendations-buildtypes.md).

1. Fare clic sul pulsante **Build**. Se si usano i dati di documentazione, il processo di creazione della build richiede circa cinque minuti. Per i set di dati di maggiori dimensioni, il processo richiede più tempo.

<a name="Step5"></a>

## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Passaggio 5 - Modelli appresi dal computer
Dopo aver completato la build, nell'elenco delle build ne viene inserita una nuova. È possibile eseguire query su questa build riguardo alle raccomandazioni relative agli elementi e agli utenti.

1. Dopo aver completato la build fare clic su **Score** (Punteggio). Questo consente di fare esperimenti con il modello e di vedere quali elementi sono consigliati.
   
    ![Interfaccia utente di Recommendations: pulsante Score (Punteggio)][reco_score_button]
2. Selezionare un elemento per visualizzare quali elementi vengono restituiti come raccomandazioni per l'elemento stesso. Si noti che, se non sono presenti transazioni sufficienti per creare una raccomandazione per un particolare elemento, il sistema non restituirà alcuna raccomandazione per tale elemento.  Se per qualche motivo è presente un elemento che non restituisce alcuna raccomandazione, provare ad assegnare un punteggio agli altri elementi.

<a name="Step6"></a>

## <a name="step-6---next-steps"></a>Passaggio 6 - Fasi successive
Congratulazioni. È stato eseguito il training di un modello e sono state ottenute raccomandazioni da tale modello.  L'interfaccia utente di Recommendations è uno strumento utile che consente di visualizzare lo stato dei progetti e delle build. 

Una volta creato il modello, può essere opportuno apprendere come eseguire a livello di programmazione tutti i passaggi descritti in precedenza. Per informazioni su come chiamare l'API a livello di programmazione, vedere [Recommendations API Reference](http://go.microsoft.com/fwlink/?LinkId=759348) (Riferimento per l'API Recommendations) e scaricare l'[esempio di API Recommendations](http://go.microsoft.com/fwlink/?LinkID=759344).

[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png



<!--HONumber=Dec16_HO2-->


