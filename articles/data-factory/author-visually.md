---
title: Creazione visiva
description: Informazioni su come usare la creazione di oggetti visivi in Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: e7de92878dac72470c0b65d1cf18c1a2d526a0bb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418491"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Creazione di oggetti visivi in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'interfaccia utente di Azure Data Factory consente di creare e distribuire con strumenti visivi le risorse della data factory senza dover scrivere il codice. È possibile trascinare le attività in un'area di disegno della pipeline, eseguire test, eseguire il debug in modo iterativo, nonché distribuire e monitorare le esecuzioni della pipeline.

Attualmente, l'esperienza utente di Azure Data Factory è supportata solo in Microsoft Edge e Google Chrome.

## <a name="authoring-canvas"></a>Area di disegno creazione

Per aprire l'area di disegno di **creazione,** fare clic sull'icona a matita. 

![Area di disegno di creazione](media/author-visually/authoring-canvas.png)

Qui verranno create pipeline, attività, set di dati, servizi collegati, flussi di dati, trigger e runtime di integrazione che costituiscono la factory. Per iniziare a creare una pipeline usando il canvas di creazione, vedere [Copiare dati usando l'attività](tutorial-copy-data-portal.md)di copia . 

L'esperienza di creazione visiva predefinita funziona direttamente con il servizio Data Factory.The default visual authoring experience is directly working with the Data Factory service. L'integrazione di Azure Repos Git o GitHub è supportata anche per consentire il controllo del codice sorgente e la collaborazione per il lavoro nelle pipeline di data factory. Per altre informazioni sulle differenze tra queste esperienze di creazione, vedere [Controllo del codice sorgente in Azure Data Factory.](source-control.md)

## <a name="expressions-and-functions"></a>Espressioni e funzioni

Le espressioni e le funzioni possono essere usate al posto dei valori statici per specificare molte proprietà in Azure Data Factory.Expressions and functions can be used instead of static values to specify many properties in Azure Data Factory.

Per specificare un'espressione per un valore di proprietà, selezionare **Aggiungi contenuto dinamico** o fare clic su **Alt e P** mentre si concentra sul campo.

![Aggiungere il contenuto dinamico](media/author-visually/dynamic-content-1.png)

Verrà aperto il Generatore di espressioni **data factory** in cui è possibile compilare espressioni da variabili di sistema supportate, output dell'attività, funzioni e variabili o parametri specificati dall'utente. 

![Generatore di espressioni](media/author-visually/dynamic-content-2.png)

Per informazioni sul linguaggio delle espressioni, vedere [Espressioni e funzioni in Azure Data Factory.](control-flow-expression-language-functions.md)

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Selezionare **Commenti e suggerimenti** per lasciare un commento sulle funzionalità o per notificare a Microsoft i problemi con gli strumenti:

![Commenti e suggerimenti](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio e sulla gestione delle pipeline, vedere [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
