---
title: Azure Machine Learning e i dispositivi FPGA
description: Informazioni sull'accelerazione di modelli e reti neurali profonde con i dispositivi FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>Che cos'è un dispositivo FPGA?

I dispositivi FPGA (Field Programmable Gate Array) sono circuiti integrati che possono essere riconfigurati in base alle proprie esigenze. È possibile modificare un dispositivo FPGA in base alle proprie esigenze per implementare una nuova logica. I modelli di accelerazione hardware di Azure Machine Learning consentono di distribuire modelli sottoposti a training in dispositivi FPGA nel cloud di Azure.

Questa funzionalità è supportata dalla tecnologia Project Brainwave, che gestisce la conversione di reti neurali profonde (DNN, Deep Neural Network) in un programma FPGA. 

## <a name="why-use-an-fpga"></a>Perché usare un dispositivo FPGA?

I dispositivi FPGA forniscono latenza ridotta e sono particolarmente efficaci per assegnare punteggi ai dati in dimensioni batch (non c'è alcun requisito per dimensioni batch maggiori).  Sono convenienti in termini di costi e sono disponibili in Azure.  La tecnologia Project Brainwave è in grado di parallelizzare le reti neurali profonde con training preliminare tra i dispositivi FPGA, in modo da ridimensionare il servizio.

## <a name="next-steps"></a>Passaggi successivi

[Deploy a model as a web service on an FPGA](how-to-deploy-fpga-web-service.md) (Distribuire un modello come servizio Web in un dispositivo FPGA)

[Learn how to install the FPGA SDK](reference-fpga-package-overview.md) (Informazioni sull'installazione dell'SDK del dispositivo FPGA)