---
title: Componente aggiuntivo di Excel per i servizi Web di Machine Learning | Documentazione Microsoft
description: Come usare i servizi Web di Azure Machine Learning direttamente in Excel senza scrivere codice.
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 2/1/2018
ms.author: tedway;garye
ms.openlocfilehash: cec26a5ccd4620dd0dabc92f4655f5fdfaf62e20
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Componente aggiuntivo Excel per i servizi Web di Azure Machine Learning
Excel consente di chiamare servizi Web direttamente senza dover scrivere alcun codice.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Procedura per usare un servizio Web esistente nella cartella di lavoro

1. Aprire il [file di Excel di esempio](http://aka.ms/amlexcel-sample-2)che contiene il componente aggiuntivo Excel e i dati relativi ai passeggeri sul Titanic.
2. Scegliere un servizio Web facendo clic su di esso: in questo esempio "Stime sopravvissuti Titanic (esempio componente aggiuntivo Excel) [Score]".
   
    ![Selezionare il servizio Web][01]
3. Viene visualizzata la sezione **Stima**.  Questa cartella di lavoro contiene già dati di esempio, ma per una cartella di lavoro vuota è anche possibile selezionare una cella in Excel e fare clic su **Use sample data**(Usa dati di esempio).
4. Selezionare i dati con intestazioni e fare clic sull'icona dell'intervallo dei dati di input.  Assicurarsi che sia selezionata la casella "Dati con intestazioni".
5. In **Output** immettere il numero di cella in cui si vuole inserire l'output, in questo caso ad esempio "H1".
6. Fare clic su **Stima**.
   
    ![Sezione Stima][02]

Distribuire un servizio Web o usarne uno esistente. Per altre informazioni sulla distribuzione di un servizio Web, vedere [Passaggio 5 della procedura dettagliata: Distribuire il servizio Web di Machine Learning](walkthrough-5-publish-web-service.md).

Ottenere la chiave API per il servizio Web. La posizione in cui viene eseguita l'operazione varia a seconda che sia stato pubblicato un servizio Web classico o un nuovo servizio Web di Machine Learning.

**Usare un servizio Web classico** 

1. In Machine Learning Studio fare clic sulla sezione **SERVIZI WEB** sulla sinistra e quindi selezionare il servizio Web da usare.
   
    ![Selezione di un servizio Web in Studio][04]
2. Copiare la chiave dell'API per il servizio Web.
   
    ![Chiave API in Studio][05]
3. Nella scheda **DASHBOARD** per il servizio Web fare clic sul collegamento **RICHIESTA/RISPOSTA**.
4. Cercare la sezione **Request URI** (Richiedi URI).  Copiare e salvare l'URL.

> [!NOTE]
> È ora possibile accedere al [portale dei servizi Web di Azure Machine Learning](https://services.azureml.net) per ottenere la chiave API per un servizio Web classico di Machine Learning.
> 
> 

**Usare un nuovo servizio Web**

1. Nel [portale dei servizi Web di Azure Machine Learning](https://services.azureml.net) fare clic su **Servizi Web**, poi selezionare il servizio Web. 
2. Fare clic su **Consume**(Uso).
3. Cercare la sezione **Basic consumption info** (Informazioni di base sull'uso). Copiare e salvare il valore di **Primary Key** (Chiave primaria) e l'URL **Request-Response** (Richiesta-risposta).

## <a name="steps-to-add-a-new-web-service"></a>Procedura per aggiungere un nuovo servizio Web

1. Distribuire un servizio Web o usarne uno esistente. Per altre informazioni sulla distribuzione di un servizio Web, vedere [Passaggio 5 della procedura dettagliata: Distribuire il servizio Web di Machine Learning](walkthrough-5-publish-web-service.md).
2. Fare clic su **Consume**(Uso).
3. Cercare la sezione **Basic consumption info** (Informazioni di base sull'uso). Copiare e salvare il valore di **Primary Key** (Chiave primaria) e l'URL **Request-Response** (Richiesta-risposta).
4. In Excel passare alla sezione **Servizi Web** (se si è nella sezione **Stima**, fare clic sulla freccia indietro per tornare all'elenco dei servizi Web).
   
    ![Passare alla selezione del servizio Web][03]
5. Fare clic su **Aggiungi servizio Web**.
6. Incollare l'URL nella casella di testo **URL**del componente aggiuntivo Excel.
7. Incollare la chiave API/primaria nella casella di testo **Chiave API**.
8. Fare clic su **Aggiungi**.
   
    ![URL e chiave API per un servizio Web classico.][06]
9. Per usare il servizio Web, seguire le indicazioni illustrate in precedenza nella sezione "Procedura per usare un servizio Web esistente".

## <a name="sharing-your-workbook"></a>Condivisione della cartella di lavoro
Se si salva la cartella di lavoro, verrà salvata anche la chiave API/primaria per i servizi Web che sono stati aggiunti. Pertanto, è necessario condividere la cartella di lavoro solo con persone attendibili.

In caso di domande, inserirle nella sezione seguente dedicata ai commenti o nel [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
