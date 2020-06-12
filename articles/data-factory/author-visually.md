---
title: Creazione di oggetti visivi
description: Informazioni su come usare la creazione di oggetti visivi in Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 05/15/2020
ms.openlocfilehash: 79b89c6a6a7598dd3fbdfc5030fd59fe1aab6625
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832782"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Creazione di oggetti visivi in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'interfaccia utente di Azure Data Factory consente di creare e distribuire con strumenti visivi le risorse della data factory senza dover scrivere il codice. È possibile trascinare le attività in un'area di disegno della pipeline, eseguire test, eseguire il debug in modo iterativo, nonché distribuire e monitorare le esecuzioni della pipeline.

L'esperienza utente di Azure Data Factory è attualmente supportata solo in Microsoft Edge e Google Chrome.

## <a name="authoring-canvas"></a>Area di disegno di creazione

Per aprire l'**area di disegno di creazione**, fare clic sull'icona della matita. 

![Area di disegno di creazione](media/author-visually/authoring-canvas.png)

Qui è possibile creare pipeline, attività, set di dati, servizi collegati, flussi di dati, trigger e runtime di integrazione che comprendono la factory. Per iniziare a creare una pipeline usando l'area di disegno di creazione, vedere [Copiare i dati usando l'attività di copia](tutorial-copy-data-portal.md). 

L'esperienza di creazione di oggetti visivi predefinita funziona direttamente con il servizio Data Factory. L'integrazione di GIT Azure Repos o GitHub è supportata anche per consentire il controllo del codice sorgente e la collaborazione per il lavoro sulle pipeline di data factory. Per altre informazioni sulle differenze tra queste esperienze di creazione, vedere [Controllo del codice sorgente in Azure Data Factory](source-control.md).

### <a name="properties-pane"></a>riquadro Proprietà

Per le risorse di livello superiore, ad esempio pipeline, i set di dati e i flussi di dati, le proprietà di alto livello sono modificabili nel riquadro delle proprietà sul lato destro dell'area di disegno. Il riquadro delle proprietà contiene proprietà quali nome, descrizione, annotazioni e altre proprietà di alto livello. Le risorse secondarie quali le attività della pipeline e le trasformazioni del flusso di dati vengono modificate utilizzando il pannello nella parte inferiore dell'area di disegno. 

![Area di disegno di creazione](media/author-visually/properties-pane.png)

Il riquadro delle proprietà si apre per impostazione predefinita solo per la creazione di risorse. Per modificarlo, fare clic sull'icona del riquadro delle proprietà che si trova nell'angolo in alto a destra dell'area di disegno.

## <a name="expressions-and-functions"></a>Espressioni e funzioni

Le espressioni e le funzioni possono essere usate al posto dei valori statici per specificare molte proprietà in Azure Data Factory.

Per specificare un'espressione per un valore di proprietà, selezionare **Aggiungi contenuto dinamico** oppure fare clic su **ALT + P** posizionandosi sul campo.

![Aggiungere il contenuto dinamico](media/author-visually/dynamic-content-1.png)

Viene visualizzato il **Generatore di espressioni di Data Factory** in cui è possibile generare espressioni dalle variabili di sistema supportate, dall'output delle attività, dalle funzioni e da variabili o parametri specificati dall'utente. 

![Generatore di espressioni](media/author-visually/dynamic-content-2.png)

Per informazioni sulla lingua delle espressioni, vedere [Espressioni e funzioni in Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Selezionare **Commenti e suggerimenti** per lasciare un commento sulle funzionalità o per notificare a Microsoft i problemi con gli strumenti:

![Commenti e suggerimenti](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
