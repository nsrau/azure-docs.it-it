---
title: Data science con la macchina virtuale per l'apprendimento avanzato in data science in Azure | Microsoft Docs
description: Come eseguire varie attività comuni di data science con la VM per l'apprendimento avanzato in data science.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 2053ed8cc420183d493097eeb2cd2ad93c82c70c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-deep-learning-virtual-machine"></a>Uso della macchina virtuale per l'apprendimento avanzato

Dopo aver eseguito il provisioning della macchina virtuale per l'apprendimento avanzato (DLVM), è possibile iniziare a creare modelli di reti neurali profonde per compilare applicazioni AI in domini come la visione artificiale e la comprensione del linguaggio. 

Sono disponibili numerosi strumenti forniti nella macchina virtuale per l'apprendimento avanzato per AI. La [pagina dedicata all'apprendimento avanzato e ai framework AI](dsvm-deep-learning-ai-frameworks.md) contiene informazioni dettagliate su come usare questi strumenti. 

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Esercitazioni e procedure dettagliate di apprendimento avanzato

Oltre a esempi basati su framework viene fornito anche un set di procedure dettagliate complete che sono state convalidate nella DLVM. Queste procedure dettagliate consentono di iniziare subito a sviluppare applicazioni di apprendimento avanzato in domini come la comprensione delle immagini e del linguaggio/testo. Vengono aggiunte continuamente nuove esercitazioni end-to-end riguardanti domini e tecnologie diverse.   


- [Esecuzione di reti neurali in diversi framework](https://github.com/ilkarman/DeepLearningFrameworks): procedura dettagliata completa che illustra come eseguire la migrazione del codice da un framework a un altro. Illustra anche come confrontare le prestazioni del modello e di runtime fra i framework. 

- [Guida dettagliata alla creazione di una soluzione end-to-end per individuare prodotti all'interno di immagini](https://github.com/Azure/cortana-intelligence-product-detection-from-images): il rilevamento immagine è una tecnica che consente di individuare e classificare gli oggetti all'interno delle immagini. Questa tecnologia può portare enormi vantaggi in molte applicazioni aziendali reali. Ad esempio, i negozianti possono usare questa tecnica per determinare quale prodotto un cliente ha prelevato dallo scaffale. Queste informazioni a loro volta consentono ai negozi di gestire l'inventario dei prodotti. 

- [Estrazione di un'entità denominata dai sunti di PubMed](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition) Questa esercitazione illustra come estrarre le entità denominate, ad esempio nomi di farmaci o nomi di malattie, da un testo non strutturato. Esegue il training dell'incorporamento personalizzato di una parola su un corpus di testi di 18 milioni di sunti di PubMed, usa tale modello per compilare un modello di rete neurale ricorrente Long Short-Term Memory, ovvero LSTM, per l'estrazione delle entità e mostra che il modello di incorporamento della parola specifico per il dominio può avere prestazioni migliori di un incorporamento delle parole generico per l'estrazione delle entità.

- [Apprendimento per l'audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) Questa esercitazione illustra come eseguire il training di un modello di apprendimento avanzato per il rilevamento di eventi audio nel [set di dati dei suoni urbani](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) e offre una panoramica di come usare i dati audio.

- [Classificazione di documenti di testo](https://github.com/anargyri/lstm_han): questa procedura dettagliata illustra come creare e addestrare due architetture di rete neurale diverse, ovvero rete Hierarchical Attention Network e Long Short Term Memory (LSTM). Queste reti neurali usano l'API Keras per l'apprendimento avanzato per classificare i documenti di testo. Keras è un front-end a tre dei più popolari framework di apprendimento avanzato: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="next-steps"></a>Passaggi successivi

La [pagina degli esempi](dsvm-samples-and-walkthroughs.md) fornisce puntatori agli esempi di codice pre-caricati nella VM per ognuno dei framework, grazie ai quali è possibile iniziare rapidamente. 
