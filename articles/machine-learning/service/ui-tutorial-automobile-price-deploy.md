---
title: "Esercitazione: Distribuire un modello di Machine Learning con l'interfaccia visiva grafica"
titleSuffix: Azure Machine Learning
description: Informazioni su come creare una soluzione di analisi predittiva nell'interfaccia visiva grafica di Azure Machine Learning. Eseguire il training, assegnare punteggi e distribuire un modello di Machine Learning usando moduli con trascinamento della selezione. Questa esercitazione è la seconda parte di una serie in due parti su come stimare i prezzi delle automobili con la regressione lineare.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 22d5c41e8b815fd99450962cb63d11e9560c787f
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997018"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Esercitazione: Distribuire un modello di Machine Learning con l'interfaccia visiva grafica

Per offrire ad altri utenti la possibilità di usare il modello predittivo sviluppato nella [prima parte dell'esercitazione](ui-tutorial-automobile-price-train-score.md), è possibile distribuirlo come servizio Web di Azure. Finora è stato sperimentato il training del modello. Ora è il momento di generare nuove stime in base all'input dell'utente. In questa parte dell'esercitazione verranno eseguite queste operazioni:

> [!div class="checklist"]
> * Preparare un modello per la distribuzione
> * Distribuire un servizio Web
> * Testare un servizio Web
> * Gestire un servizio Web
> * Utilizzare il servizio Web

## <a name="prerequisites"></a>Prerequisiti

Completare [la prima parte dell'esercitazione](ui-tutorial-automobile-price-train-score.md) per apprendere come eseguire il training e assegnare un punteggio a un modello di Machine Learning nell'interfaccia visiva grafica.

## <a name="prepare-for-deployment"></a>Preparare la distribuzione

Prima di distribuire l'esperimento come servizio Web, è necessario convertire l'*esperimento di training* in un *esperimento predittivo*.

1. Selezionare **Create Predictive Experiment*** (Crea esperimento predittivo) nella parte inferiore dell'area di disegno dell'esperimento.

    ![Gif animata che mostra la conversione automatica di un esperimento di training in un esperimento predittivo](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Quando si seleziona **Create Predictive Experiment**, si verificano diverse situazioni:
    
    * Il modello sottoposto a training viene archiviato come **Trained Model** (Modello con training) nel riquadro dei moduli. nella sezione **Trained Models** (Modelli con training).
    * Vengono rimossi i moduli usati per il training, in particolare:
      * Train Model (Training del modello)
      * Split Data (Divisione dei dati)
      * Valutare il modello
    * Il modello con training salvato viene aggiunto nuovamente all'esperimento.
    * Vengono aggiunti i moduli **Web service input** (Input servizio Web) e **Web service output** (Output servizio Web). Questi moduli identificano il punto in cui i dati dell'utente verranno immessi nel modello e il punto in cui verranno restituiti.

    L'**esperimento di training** è ancora memorizzato nelle nuove schede nella parte superiore dell'area di disegno dell'esperimento.

1. **Eseguire** l'esperimento.

1. Selezionare l'output del modulo **Score Model** (Punteggio del modello) e quindi **View Results** (Visualizza risultati) per verificare se il modello continua a funzionare. Come si può notare, vengono visualizzati i dati originali insieme al prezzo stimato ("Scored Labels", Etichette con punteggio).

L'esperimento dovrebbe risultare simile al seguente:  

![Screenshot della configurazione prevista dell'esperimento dopo la preparazione per la distribuzione](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Distribuire il servizio web

1. Selezionare **Deploy Web Service** (Distribuisci servizio Web) sotto l'area di disegno.

1. Selezionare la **destinazione di calcolo** per eseguire il servizio Web.

    Attualmente l'interfaccia visiva grafica supporta solo la distribuzione nelle destinazioni di calcolo del servizio Azure Kubernetes. È possibile scegliere una delle destinazioni di calcolo disponibili del servizio Azure Kubernetes nell'area di lavoro del servizio Machine Learning oppure configurare un nuovo ambiente del servizio Azure Kubernetes seguendo i passaggi indicati nella finestra di dialogo visualizzata.

    ![Screenshot di una possibile configurazione per una nuova destinazione di calcolo](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Selezionare **Deploy Web Service** (Distribuisci servizio Web). Al termine della distribuzione verrà visualizzata la notifica seguente. La distribuzione può richiedere alcuni minuti.

    ![Screenshot del messaggio di conferma per una distribuzione riuscita.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testare il servizio Web

È possibile testare e gestire i servizi Web dell'interfaccia visiva grafica passando alla scheda **Servizi Web**.

1. Passare alla sezione del servizio Web. Verrà visualizzato il servizio Web distribuito con il nome **Tutorial - Predict Automobile Price[Predictive Exp]** .

     ![Screenshot della scheda del servizio Web con il servizio Web creato di recente evidenziato](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selezionare il nome del servizio Web per visualizzare altri dettagli.

1. Selezionare **Test**.

    [![Screenshot della pagina di test del servizio Web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. Immettere i dati di test oppure usare i dati di esempio compilati automaticamente e selezionare **Test**.

    La richiesta di test viene inviata al servizio Web e i risultati vengono visualizzati nella pagina. Anche se generato, il valore di prezzo per i dati di input non viene usato per generare il valore della stima.

## <a name="consume-the-web-service"></a>Utilizzare il servizio Web

Gli utenti possono ora inviare richieste API al servizio Web di Azure e ricevere i risultati per prevedere il prezzo delle nuove automobili.

**Richiesta/Risposta**: l'utente invia una o più righe di dati sulle automobili al servizio tramite un protocollo HTTP. Il servizio risponde con uno o più set di risultati.

L'esempio di chiamate REST è disponibile nella scheda **Consume** (Utilizzo) della pagina di dettagli del servizio Web.

   ![Screenshot di un esempio di chiamata REST che gli utenti trovano nella scheda Consume](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Passare alla scheda **API Doc** (Documentazione dell'API) per altre informazioni sull'API.

## <a name="manage-models-and-deployments"></a>Gestire modelli e distribuzioni

I modelli e le distribuzioni di servizi Web creati nell'interfaccia visiva grafica possono anche essere gestiti nell'area di lavoro di Azure Machine Learning.

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  

1. Nell'area di lavoro selezionare **Modelli**. Quindi selezionare l'esperimento creato.

    ![Screenshot che mostra come passare agli esperimenti nel portale di Azure](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    In questa pagina sono visualizzati altri dettagli sul modello.

1. Selezionare **Distribuzioni** per visualizzare un elenco dei servizi Web che usano il modello. Selezionare il nome del servizio Web per passare alla relativa pagina di dettagli. In questa pagina è possibile visualizzare informazioni più dettagliate sul servizio Web.

    [![Screenshot del report dettagliato sulle esecuzioni](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

È anche possibile trovare questi modelli e distribuzioni nelle sezioni **Modelli** ed **Endpoint** della [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com).

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati i principali passaggi per creare, distribuire e utilizzare un modello di Machine Learning nell'interfaccia visiva grafica. Per altre informazioni su come usare l'interfaccia visiva grafica per risolvere altri tipi di problemi, vedere gli esperimenti di esempio.

> [!div class="nextstepaction"]
> [Esempio di classificazione del rischio di credito](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
