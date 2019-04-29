---
title: Usare un servizio Web in Excel
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio semplifica la chiamata dei servizi Web direttamente da Excel senza dover di scrivere codice.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: ef1d8f1a72c5936ff661636c4c51acf439a0a5ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773800"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>Utilizzo di un servizio Web di Azure Machine Learning Studio da Excel

 Azure Machine Learning Studio semplifica la chiamata dei servizi Web direttamente da Excel senza dover di scrivere codice.

Se si usa Excel 2013 (o versione successiva) o Excel Online, è consigliabile usare il [componente aggiuntivo di Excel](excel-add-in-for-web-services.md).



## <a name="steps"></a>Passaggi
Pubblicazione di un servizio Web. [Esercitazione 3: Distribuire il modello di rischio di credito](tutorial-part3-credit-risk-deploy.md) spiega come eseguire questa operazione. Attualmente la funzionalità della cartella di lavoro di Excel è supportata solo per i servizi di richiesta/risposta con un unico output cioè, una singola etichetta di valutazione. 

Dopo aver creato un servizio Web, fare clic sulla sezione **WEB SERVICES** sulla sinistra di Studio e quindi selezionare il servizio Web da utilizzare tramite Excel.

**Servizio Web classico**

1. Nella scheda **DASHBOARD** per il servizio Web è presente una riga per il servizio **REQUEST/RESPONSE**. Se il servizio ha un singolo output, è necessario vedere il collegamento **Download Excel Workbook** in quella riga.

    ![Scaricare una cartella di lavoro di Excel usando il portale dei servizi Web di Studio](./media/consuming-from-excel/excellink.png)
2. Fare clic su **Download Excel Workbook**(Scarica cartella di lavoro Excel).

**Nuovo servizio Web**

1. Nel portale del servizio Web di Azure Machine Learning, selezionare **Consume**(Uso).
2. Nella pagina Consume (Uso), nella sezione **Web service consumption options** (Opzioni d'uso del servizio Web) fare clic sull'icona di Excel.

**Uso della cartella di lavoro**

1. Aprire la cartella di lavoro.
2. Viene visualizzato un avviso di sicurezza. Fare clic sul pulsante **Abilita modifica**.

    ![Abilitare la modifica rimuovere l'avviso di sicurezza Visualizzazione protetta](./media/consuming-from-excel/enableeditting.png)
3. Viene visualizzato un avviso di sicurezza. Fare clic sul pulsante **Abilita contenuto** per eseguire le macro nel foglio di calcolo.

    ![Abilitare il contenuto ignorare l'avviso di sicurezza la disabilitazione di macro](./media/consuming-from-excel/enablecontent.png)
4. Una volta attivate le macro, viene generata una tabella. Le colonne in blu sono necessarie come input nel servizio Web di richiesta-risposta (RRS) o **PARAMETRI**. Si noti che l'output del servizio RRS, **PREDICTED VALUES** è verde. Quando vengono riempite tutte le colonne per una determinata riga, la cartella di lavoro può automaticamente chiamare l'API di valutazione e visualizzare i risultati corrispondenti.

    ![Tabella di input di parametri e risultante stimati valori](./media/consuming-from-excel/sampletable.png)
5. Per valutare più di una riga, compilare la seconda riga con i dati, così verranno elaborati i valori stimati. È anche possibile incollare più righe contemporaneamente.

È possibile usare tutte le funzionalità di Excel (grafici, Power Map, formattazione condizionale e così via) con i valori stimati per visualizzare i dati.

## <a name="sharing-your-workbook"></a>Condivisione della cartella di lavoro
Affinché le macro funzionino, la chiave API deve far parte del foglio di calcolo. Ciò significa che è necessario condividere la cartella di lavoro solo con le entità o i singoli utenti considerati attendibili.

## <a name="automatic-updates"></a>Aggiornamenti automatici
Una chiamata RRS viene effettuata nei due casi seguenti:

1. La prima volta che una riga include contenuto in tutti i propri **PARAMETRI**
2. Ogni volta che uno dei **PARAMETRI** viene modificato in una riga che aveva tutti i **PARAMETRI** immessi.