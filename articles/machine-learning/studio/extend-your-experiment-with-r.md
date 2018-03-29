---
title: Estendere l'esperimento con R | Microsoft Docs
description: Come estendere le funzionalità di Azure Machine Learning Studio tramite il linguaggio R usando il modulo Execute R Script.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 99c57cc095c3eb017ada68417b7b36c8773ed3d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="extend-your-experiment-with-r"></a>Estendere l'esperimento con R
È possibile estendere le funzionalità di Azure Machine Learning Studio tramite il linguaggio R usando il modulo [Execute R Script][execute-r-script].

Questo modulo accetta più set di dati di input e produce un singolo set di dati come output. È possibile digitare uno script R nel parametro **R Script** del modulo [Execute R Script][execute-r-script] (Esegui script R).

Per accedere a ogni porta di input del modulo, usare codice simile al seguente:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Elenco di tutti i pacchetti installati attualmente
L'elenco dei pacchetti installati può cambiare. Un elenco dei pacchetti attualmente installati è disponibile in [R Packages Supported by Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt741980.aspx) (Pacchetti R supportati da Azure Machine Learning).

Per ottenere l'elenco completo e aggiornato dei pacchetti installati, immettere il codice seguente nel modulo [Execute R Script][execute-r-script]:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

In questo modo l'elenco dei pacchetti viene inviato alla porta di output del modulo [Execute R Script][execute-r-script] (Esegui script R).
Per visualizzare l'elenco di pacchetti, connettere un modulo di conversione, ad esempio [Convert to CSV][convert-to-csv] (Converti in CSV) all'output di sinistra del modulo [Execute R Script][execute-r-script] (Esegui script R), eseguire l'esperimento, quindi fare clic sull'output del modulo di conversione e selezionare **Download**. 

![Scaricare l'output del modulo "Convert to CSV" (Converti in CSV)](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importazione di pacchetti
Per importare i pacchetti non ancora installati, è possibile usare i comandi seguenti nel modulo [Execute R Script][execute-r-script]:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

Il file `my_favorite_package.zip` contiene il pacchetto.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
