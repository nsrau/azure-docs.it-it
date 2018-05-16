---
title: Pacchetti Python per Azure Machine Learning
description: Informazioni sui pacchetti Python disponibili per gli utenti di Azure Machine Learning.
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jmartens
author: j-martens
ms.date: 05/07/2018
ms.openlocfilehash: 7ff80a812ebf704315524ffb5ea518704e472429
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="python-packages-for-azure-machine-learning"></a>Pacchetti Python per Azure Machine Learning

Informazioni sui pacchetti Python proprietari di Microsoft per Azure Machine Learning. È possibile usare queste funzioni e librerie in combinazione con altri pacchetti open source o di terze parti, ma per usare i pacchetti proprietari, il codice Python deve essere eseguito su un servizio o un computer che fornisce gli interpreti.

I pacchetti di Azure Machine Learning sono **estensioni Python installabili con pip per Azure Machine Learning** che consentono ai data scientist e agli sviluppatori di intelligenza artificiale di compilare e distribuire rapidamente modelli estremamente accurati di apprendimento automatico e apprendimento avanzato per vari domini.

<a name="amlpcv"></a>
## <a name="azure-ml-package-for-computer-vision"></a>Pacchetto di Azure Machine Learning per la visione artificiale

Con il pacchetto di Azure Machine Learning per la visione artificiale, è possibile compilare, ottimizzare e distribuire modelli di apprendimento avanzato per la classificazione delle immagini, il rilevamento degli oggetti e la somiglianza tra le immagini.

Per questo pacchetto, eseguire i passaggi seguenti:
1. [Scaricare](https://aka.ms/aml-packages/vision/download) il pacchetto.
1. Leggere il [documento di installazione](https://aka.ms/aml-packages/vision).
1. [Compilare e distribuire un modello di visione artificiale](how-to-build-deploy-image-classification-models.md) con un notebook di Jupyter.
1. Consultare la [documentazione di riferimento](https://aka.ms/aml-packages/vision) del pacchetto.

<a name="amlpf"></a>
## <a name="azure-ml-package-for-forecasting"></a>Pacchetto di Azure Machine Learning per la previsione

Con il pacchetto di Azure Machine Learning per la previsione, è possibile creare e distribuire modelli di previsione della serie temporale per scenari finanziari e di previsione della domanda.

Per questo pacchetto, eseguire i passaggi seguenti:
1. [Scaricare](https://aka.ms/aml-packages/forecasting/download) il pacchetto.
1. Leggere il [documento di installazione](https://aka.ms/aml-packages/forecasting).
1. [Compilare e distribuire un modello di previsione](how-to-build-deploy-forecast-models.md) con un notebook di Jupyter.
1. Consultare la [documentazione di riferimento](https://aka.ms/aml-packages/forecasting) del pacchetto.

<a name="amlpta"></a>
## <a name="azure-ml-package-for-text-analytics"></a>Pacchetto di Azure Machine Learning per l'analisi del testo

Con il pacchetto di Azure Machine Learning per l'analisi del testo, è possibile compilare modelli di apprendimento avanzato del testo per la classificazione del testo, l'estrazione di entità personalizzate e la rappresentazione distribuita delle parole.

Per questo pacchetto, eseguire i passaggi seguenti:
1. [Scaricare](https://aka.ms/aml-packages/text/download) il pacchetto.
1. Leggere il [documento di installazione](https://aka.ms/aml-packages/text).
1. [Compilare e distribuire un modello di classificazione del testo](how-to-build-deploy-text-classification-models.md) con un notebook di Jupyter.
1. Consultare la [documentazione di riferimento](https://aka.ms/aml-packages/text) del pacchetto.

<a name="amlrealtimeai"></a>
## <a name="amlrealtimeai-fpga-acceleration"></a>Intelligenza artificiale in tempo reale per Azure Machine Learning (accelerazione FPGA)

Con il pacchetto di Azure Machine Learning per l'accelerazione hardware, i data scientist e gli sviluppatori di intelligenza artificiale possono trasformare le immagini con una versione quantizzata di ResNet 50, eseguire il training di classificatori in base a tali funzionalità e quindi distribuire i modelli a un dispositivo [FPGA (Field Programmable Gate Array)](concept-accelerate-with-fpgas.md) in Azure per inferenze a latenza estremamente bassa.

Per questo pacchetto, eseguire i passaggi seguenti:
1. [Scaricare](https://aka.ms/aml-real-time-ai-package) il pacchetto.
1. Leggere il [documento di installazione](reference-fpga-package-overview.md).
1. [Distribuire un modello in un dispositivo FPGA](how-to-deploy-fpga-web-service.md).

