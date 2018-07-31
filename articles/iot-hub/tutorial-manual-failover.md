---
title: Failover manuale di un hub IoT di Azure | Microsoft Docs
description: Descrive come eseguire un failover manuale per un hub IoT di Azure
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: f0e8bf922f142b795dd1a2ded4b3ec265c43481a
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249990"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>Esercitazione: Eseguire il failover manuale per un hub IoT (anteprima pubblica)

Il failover manuale è una funzionalità del servizio hub IoT che consente ai clienti di eseguire il [failover](https://en.wikipedia.org/wiki/Failover) delle attività dell'hub da un'area primaria all'area geografica associata di Azure corrispondente. Il failover manuale può essere eseguito in caso di emergenza a livello di area o in eseguito a un'interruzione del servizio estesa. È anche possibile eseguire un failover pianificato per testare le funzionalità di ripristino di emergenza, anche se è consigliabile usare un hub IoT di test, invece di uno in esecuzione nell'ambiente di produzione. La funzionalità di failover manuale viene offerta ai clienti senza costi aggiuntivi.

In questa esercitazione si eseguono le seguenti attività:

> [!div class="checklist"]
> * Creare un hub IoT usando il portale di Azure. 
> * Eseguire un failover. 
> * Vedere l'hub in esecuzione nella località secondaria.
> * Eseguire un failback per ritornare alle operazioni dell'hub IoT nella località primaria. 
> * Verificare che l'hub venga eseguito correttamente nella località corretta.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Fare clic su **+ Crea una risorsa** e selezionare **Internet delle cose**, quindi **Hub IoT**.

   ![Screenshot che illustra la creazione di un hub IoT](./media/tutorial-manual-failover/create-hub-01.png)

3. Selezionare la scheda **Informazioni di base**. Compilare i campi seguenti.

    **Sottoscrizione**: selezionare la sottoscrizione di Azure che si vuole usare.

    **Gruppo di risorse**: fare clic su **Crea nuovo** e specificare **ManlFailRG** come nome del gruppo di risorse.

    **Area**: selezionare un'area vicina inclusa nell'anteprima. Questa esercitazione usa `westus2`. Un failover può essere eseguito solo tra aree geografiche associate di Azure. L'area geografica associata a westus2 è WestCentralUS.
    
   > [!NOTE]
   > Il failover manuale è attualmente in anteprima pubblica e *non* è disponibile nelle aree di Azure seguenti: Stati Uniti orientali, Stati Uniti occidentali, Europa settentrionale, Europa occidentale, Brasile meridionale e Stati Uniti centro-meridionali.

   **Nome hub IoT**: specificare un nome per l'hub IoT. Il nome dell'hub deve essere univoco a livello globale. 

   ![Screenshot che mostra il riquadro Informazioni di base per la creazione di un hub IoT](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Fare clic su **Rivedi e crea**. (Usa le impostazioni predefinite per dimensioni e scalabilità.) 

4. Verificare le informazioni e quindi fare clic su **Crea** per creare l'hub IoT. 

   ![Screenshot che mostra l'ultimo passaggio per la creazione di un hub IoT](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Eseguire un failover manuale

Si noti che è previsto un limite di due failover e due failback al giorno per un hub IoT.

1. Fare clic su **Gruppi di risorse** e quindi selezionare il gruppo di risorse **ManlFailRG**. Fare clic sull'hub nell'elenco delle risorse. 

2. In **Resilienza**, nel riquadro dell'hub IoT, fare clic su **Failover manuale (anteprima)**. Si noti che se l'hub non è configurato per un'area valida, l'opzione di failover manuale verrà disabilitata.

   ![Screenshot che mostra il riquadro delle proprietà dell'hub IoT](./media/tutorial-manual-failover/trigger-failover-01.png)

3. Nel riquadro di failover manuale vengono visualizzate la **località primaria dell'hub IoT** e la **località secondaria dell'hub IoT**. La località primaria è inizialmente impostata sulla località specificata durante la creazione dell'hub IoT e indica sempre la località in cui l'hub è attualmente attivo. La località secondaria è l'[area geografica associata di Azure](../best-practices-availability-paired-regions.md) standard abbinata alla località primaria. Non è possibile modificare i valori della località. Per questa esercitazione, la località primaria è `westus2` e la località secondaria è `WestCentralUS`.

   ![Screenshot che mostra il riquadro Failover manuale](./media/tutorial-manual-failover/trigger-failover-02.png)

3. Nella parte superiore del riquadro Failover manuale, fare clic su **Initiate failover** (Avvia failover). Viene visualizzato il riquadro **Confirm manual failover** (Conferma failover manuale). Specificare il nome dell'hub IoT per confermare che è quello di cui si vuole eseguire il failover. Quindi, per avviare il failover, fare clic su **OK**.

   La quantità di tempo impiegato per eseguire il failover manuale è proporzionale al numero di dispositivi registrati per l'hub. Ad esempio, in presenza di 100.000 dispositivi potrebbero essere richiesti 15 minuti, ma per cinque milioni di dispositivi l'operazione potrebbe richiedere un'ora o più.

4. Nel riquadro **Confirm manual failover** (Conferma failover manuale) specificare il nome dell'hub IoT per confermare che è quello di cui si vuole eseguire il failover. Quindi, per avviare il failover, fare clic su OK. 

   ![Screenshot che mostra il riquadro Failover manuale](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Durante l'esecuzione del processo di failover manuale, nel riquadro Failover manuale viene visualizzato un banner che indica che è in corso un failover manuale. 

   ![Screenshot che mostra il failover manuale in corso](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Se si chiude il riquadro Hub IoT e lo si apre di nuovo facendo clic su di esso nel riquadro Gruppo di risorse, viene visualizzato un banner che indica che l'hub non è attivo. 

   ![Screenshot che mostra l'hub IoT inattivo](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Al termine, le aree primaria e secondaria nella pagina Failover manuale vengono invertite e l'hub diventa attivo di nuovo. In questo esempio, la località primaria è ora `WestCentralUS` e la località secondaria è ora `westus2`. 

   ![Screenshot che mostra che il failover è stato completato](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>Eseguire il failback 

Dopo aver eseguito un failover manuale, è possibile ritrasferire le operazioni dell'hub nell'area primaria originale, operazione nota come failback. Se è appena stato eseguito un failover, è necessario attendere circa un'ora prima di poter richiedere un failback. Se si prova a eseguire il failback in un periodo di tempo più breve, viene visualizzato un messaggio di errore.

Il failback viene eseguito esattamente come un failover manuale. Questa è la procedura: 

1. Per eseguire un failback, tornare al riquadro Hub Iot per l'hub Iot.

2. In **Resilienza**, nel riquadro dell'hub IoT, fare clic su **Failover manuale (anteprima)**. 

3. Nella parte superiore del riquadro Failover manuale, fare clic su **Initiate failover** (Avvia failover). Viene visualizzato il riquadro **Confirm manual failover** (Conferma failover manuale). 

4. Nel riquadro **Confirm manual failover** (Conferma failover manuale) specificare il nome dell'hub IoT per confermare che è quello di cui si vuole eseguire il failback. Per avviare quindi il failback, fare clic su OK. 

   ![Screenshot della richiesta di failback manuale](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   Vengono visualizzati i banner, come spiegato nella sezione [Eseguire un failover](#perform-a-failover). Dopo aver completato il failback, `westus2` viene visualizzata di nuovo come località primaria e `WestCentralUS` come località secondaria, come da impostazioni originali.

## <a name="clean-up-resources"></a>Pulire le risorse 

Per rimuovere le risorse create per questa esercitazione, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso, verranno rimossi l'hub IoT e il gruppo di risorse stesso. 

1. Fare clic su **Gruppi di risorse**. 

2. Individuare e selezionare il gruppo di risorse **ManlFailRG**. Fare clic su di essa per aprirla. 

3. Fare clic su **Elimina gruppo di risorse**. Quando richiesto, immettere il nome del gruppo di risorse e fare clic su **Elimina** per confermare. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare ed eseguire un failover manuale e come richiedere un failback eseguendo le attività seguenti:

> [!div class="checklist"]
> * Creare un hub IoT usando il portale di Azure. 
> * Eseguire un failover. 
> * Vedere l'hub in esecuzione nella località secondaria.
> * Eseguire un failback per ritornare alle operazioni dell'hub IoT nella località primaria. 
> * Verificare che l'hub venga eseguito correttamente nella località corretta.

Passare all'esercitazione successiva per imparare a gestire lo stato di un dispositivo hub IoT. 

> [!div class="nextstepaction"]
[Gestire lo stato di un dispositivo IoT](tutorial-device-twins.md)