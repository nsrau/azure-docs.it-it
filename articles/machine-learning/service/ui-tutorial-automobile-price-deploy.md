---
title: "Esercitazione: Distribuire un modello di Machine Learning con l'interfaccia visiva grafica"
titleSuffix: Azure Machine Learning service
description: Informazioni su come creare una soluzione di analisi predittiva nell'interfaccia visiva grafica del servizio Azure Machine Learning. Eseguire il training, assegnare punteggi e distribuire un modello di Machine Learning usando moduli con trascinamento della selezione. Questa esercitazione è la seconda parte di una serie in due parti su come stimare i prezzi delle automobili con la regressione lineare.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 5f29e3820416686b42167fa278c4b7d0f9a58f1f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190910"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Esercitazione: Distribuire un modello di Machine Learning con l'interfaccia visiva grafica

In questa esercitazione verrà esaminato in modo approfondito lo sviluppo di una soluzione di analisi predittiva nell'interfaccia visiva grafica del servizio Azure Machine Learning. Questa esercitazione è la **seconda di una serie in due parti**. Nella [prima parte dell'esercitazione](ui-tutorial-automobile-price-train-score.md) sono state eseguite le attività di training, assegnazione di punteggi e valutazione di un modello per stimare i prezzi delle automobili. In questa parte dell'esercitazione verranno eseguite queste operazioni:

> [!div class="checklist"]
> * Preparare un modello per la distribuzione
> * Distribuire un servizio Web
> * Testare un servizio Web
> * Gestire un servizio Web
> * Utilizzare il servizio Web

## <a name="prerequisites"></a>Prerequisiti

Completare la [prima parte dell'esercitazione](ui-tutorial-automobile-price-train-score.md).

## <a name="prepare-for-deployment"></a>Preparare la distribuzione

Per offrire ad altri utenti la possibilità di usare il modello predittivo sviluppato in questa esercitazione, è possibile distribuirlo come servizio Web di Azure.

Finora è stato sperimentato il training del modello. Ora è il momento di generare nuove stime in base all'input dell'utente.

La preparazione per la distribuzione è un processo in due passaggi:  

1. Convertire l'*esperimento di training* creato in un *esperimento predittivo*
1. Distribuire l'esperimento predittivo come servizio Web

È consigliabile creare prima una copia dell'esperimento selezionando **Salva con nome** nella parte inferiore dell'area di disegno dell'esperimento.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertire l'esperimento di training in un esperimento predittivo

Per preparare questo modello per la distribuzione, convertire l'esperimento di training in un esperimento predittivo. Questa operazione implica tre passaggi:

1. Salvare il modello di cui è stato eseguito il training e sostituire i moduli di training
1. Ridurre l'esperimento per rimuovere i moduli che sono serviti solo per il training
1. Definire il punto in cui il servizio Web accetterà i dati di input e in cui genererà l'output

È possibile eseguire questi passaggi manualmente oppure selezionare **Set Up Web Service** (Configura servizio Web) nella parte inferiore dell'area di disegno dell'esperimento per completarli automaticamente.

![Gif animata che mostra la conversione automatica di un esperimento di training in un esperimento predittivo](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

Quando si seleziona **Set Up Web Service**(Configura servizio Web), vengono eseguite diverse operazioni:

* Il modello di cui è stato eseguito il training viene convertito in un singolo modulo **Trained Model** (Modello con training). Viene archiviato nella tavolozza del modulo a sinistra dell'area di disegno dell'esperimento, nella sezione **Trained Models** (Modelli con training).
* Vengono rimossi i moduli usati per il training, in particolare:
  * Train Model (Training del modello)
  * Split Data (Divisione dei dati)
  * Evaluate Model (Valutazione del modello)
* Il modello con training salvato viene aggiunto nuovamente all'esperimento
* Vengono aggiunti i moduli **Web service input** (Input servizio Web) e **Web service output** (Output servizio Web). Questi moduli identificano il punto in cui i dati dell'utente verranno immessi nel modello e il punto in cui verranno restituiti.

Si può notare che l'esperimento viene salvato in due parti nelle nuove schede nella parte superiore dell'area di disegno dell'esperimento. L'esperimento di training originale si trova sotto la scheda **Esperimento di training** e l'esperimento predittivo appena creato si trova sotto **Esperimento predittivo**. L'esperimento predittivo è quello che verrà distribuito come servizio Web.

L'esperimento dovrebbe risultare simile al seguente:  

![Screenshot della configurazione prevista dell'esperimento dopo la preparazione per la distribuzione](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

Eseguire l'esperimento un'ultima volta, selezionando **Run** (Esegui). Scegliere la destinazione di calcolo in cui eseguire l'esperimento nella finestra di dialogo popup. Per verificare se il modello continua a funzionare, selezionare l'output del modulo Score Model (Punteggio del modello) e quindi **View Results** (Visualizza risultati). Come si può notare, vengono visualizzati i dati originali insieme al prezzo stimato ("Scored Labels", Etichette con punteggio).

## <a name="deploy-the-web-service"></a>Distribuire il servizio web

Per distribuire un nuovo servizio Web derivato dall'esperimento:

1. Selezionare **Deploy Web Service** (Distribuisci servizio Web) sotto l'area di disegno.
1. Selezionare la **destinazione di calcolo** per eseguire il servizio Web.

    Attualmente l'interfaccia visiva grafica supporta solo la distribuzione nelle destinazioni di calcolo del servizio Azure Kubernetes. È possibile scegliere una delle destinazioni di calcolo disponibili del servizio Azure Kubernetes nell'area di lavoro del servizio Machine Learning oppure configurare un nuovo ambiente del servizio Azure Kubernetes seguendo i passaggi indicati nella finestra di dialogo visualizzata.

    ![Screenshot di una possibile configurazione per una nuova destinazione di calcolo](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Selezionare **Deploy Web Service** (Distribuisci servizio Web). Al termine della distribuzione verrà visualizzata la notifica seguente. La distribuzione può richiedere alcuni minuti.

    ![Screenshot del messaggio di conferma per una distribuzione riuscita.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testare il servizio Web

I dati dell'input utente vengono immessi nel modello distribuito tramite il modulo **Web service input** (Input servizio Web). All'input viene quindi assegnato un punteggio nel modulo **Score Model** (Punteggio del modello). In base al modo in cui è configurato l'esperimento predittivo, il modello presuppone che i dati abbiano lo stesso formato del set di dati originale di prezzi delle automobili. Infine, i risultati vengono restituiti all'utente tramite il modulo **Web service output** (Output del servizio Web).

È possibile testare un servizio Web nell'apposita scheda dell'interfaccia visiva grafica.

1. Passare alla sezione del servizio Web. Verrà visualizzato il servizio Web distribuito con il nome **Tutorial - Predict Automobile Price[Predictive Exp]**.

     ![Screenshot della scheda del servizio Web con il servizio Web creato di recente evidenziato](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selezionare il nome del servizio Web per visualizzare altri dettagli.

     ![Screenshot degli altri dettagli disponibili nella visualizzazione del servizio Web](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Selezionare **Test**.

    ![Screenshot della pagina di test del servizio Web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Immettere i dati di test oppure usare i dati di esempio compilati automaticamente e selezionare **Test** nella parte inferiore. La richiesta di test viene inviata al servizio Web e i risultati vengono visualizzati nella pagina.

## <a name="manage-the-web-service"></a>Gestire il servizio Web

Dopo aver distribuito il nuovo servizio Web, è possibile gestirlo nella scheda **Web Services** (Servizi Web) dell'interfaccia visiva grafica.

È possibile eliminare un servizio Web selezionando **Delete** (Elimina) nella relativa pagina di dettagli.

   ![Screenshot che mostra la posizione del pulsante per eliminare il servizio Web nella parte inferiore della finestra](./media/ui-tutorial-automobile-price-deploy/web-service-delete.png)

## <a name="consume-the-web-service"></a>Utilizzare il servizio Web

Nei passaggi precedenti dell'esercitazione è stato distribuito un modello di stima dei prezzi delle automobili come servizio Web di Azure. Ora gli utenti possono inviare dati al servizio e ricevere i risultati tramite API REST.

**Richiesta/Risposta**: l'utente invia una o più righe di dati sulle automobili al servizio tramite un protocollo HTTP. Il servizio risponde con uno o più set di risultati.

L'esempio di chiamate REST è disponibile nella scheda **Consume** (Utilizzo) della pagina di dettagli del servizio Web.

   ![Screenshot di un esempio di chiamata REST che gli utenti trovano nella scheda Consume](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Passare alla scheda **API Doc** (Documentazione dell'API) per altre informazioni sull'API.

  ![Screenshot che mostra altri dettagli sull'API disponibili nella scheda API Doc](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments-in-azure-machine-learning-service-workspace"></a>Gestire e distribuire modelli nell'area di lavoro del servizio Azure Machine Learning

I modelli e le distribuzioni di servizi Web creati nell'interfaccia visiva grafica possono essere gestiti nell'area di lavoro del servizio Azure Machine Learning.

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com/).  

1. Nell'area di lavoro selezionare **Modelli**. Quindi selezionare l'esperimento creato.

    ![Screenshot che mostra come passare agli esperimenti nel portale di Azure](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    In questa pagina sono visualizzati altri dettagli sul modello.

    ![Screenshot che mostra una panoramica delle statistiche dell'esperimento nel portale di Azure](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Selezionare **Distribuzioni** per visualizzare un elenco dei servizi Web che usano il modello. Selezionare il nome del servizio Web per passare alla relativa pagina di dettagli. In questa pagina è possibile visualizzare informazioni più dettagliate sul servizio Web.

    ![Screenshot del report dettagliato sulle esecuzioni](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati i principali passaggi per creare, distribuire e utilizzare un modello di Machine Learning nell'interfaccia visiva grafica. Per altre informazioni su come usare l'interfaccia visiva grafica per risolvere altri tipi di problemi, vedere gli esperimenti di esempio.

> [!div class="nextstepaction"]
> [Esempio di classificazione del rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
