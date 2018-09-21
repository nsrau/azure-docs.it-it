---
title: Definizione di FPGA – Project Brainwave – Azure Machine Learning
description: Informazioni sull'accelerazione di modelli e reti neurali profonde con i dispositivi FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5c8efcbb5f2e9014c9edabfc2aee37c8c29ea0b4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "35634299"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Definizione di FPGA e Project Brainwave

In questo articolo viene fornita un'introduzione ai Field Programmable Gate Array (FPGA) e alle modalità di integrazione tra FPGA e Azure Machine Learning per fornire servizi di intelligenza artificiale (AI) in tempo reale.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA e CPU, GPU, ASIC a confronto

Un dispositivo FPGA contiene una matrice di blocchi di logica programmabile e una gerarchia di interconnessioni riconfigurabili che consentono di configurare i blocchi in modi diversi dopo la produzione.

I dispositivi FPGA offrono una combinazione di programmabilità e prestazioni superiore agli altri chip:

![Confronto dei dispositivi FPGA in Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- Le **CPU** sono processori per utilizzo generico. Le prestazioni delle CPU non sono ideali per l'elaborazione di video e immagini.
- Le **unità di elaborazione grafica (GPU)** offrono capacità di elaborazione parallela e rappresentano una scelta comune per i calcoli di intelligenza artificiale. L'elaborazione parallela con GPU consente un rendering delle immagine più veloce rispetto alle CPU.
- Gli **Application Specific Integrated Circuit (ASIC)**, come le unità del processore TensorFlow di Google, sono circuiti personalizzati. Nonostante questi chip offrano la massima efficienza, gli ASIC non sono flessibili.
- I dispositivi **FPGA**, ad esempio quelli disponibili in Azure, assicurano prestazioni simili a quelle degli ASIC e la flessibilità di essere riconfigurati in un secondo momento.

I dispositivi FPGA sono ora presenti in ogni nuovo server Azure. Microsoft sta già utilizzando i dispositivi FPGA per la classificazione delle ricerche Bing, la valutazione della rete neurale profonda (DNN) e l'accelerazione SDN. Queste implementazioni FPGA riducono la latenza e mantengono libere le CPU per altre attività.

## <a name="project-brainwave-on-azure"></a>Project Brainwave in Azure

Project Brainwave è un'architettura hardware progettata sui dispositivi FPGA Intel e usata per accelerare i calcoli AI in tempo reale. Con questa architettura economica abilitata per FPGA, una rete neurale con training può essere eseguita alla velocità massima e con una latenza più bassa. La tecnologia Project Brainwave è in grado di parallelizzare le reti neurali profonde con training preliminare tra i dispositivi FPGA, in modo da ridimensionare il servizio.

- Prestazioni

    I dispositivi FPGA consentono di ottenere una bassa latenza per le richieste di inferenza in tempo reale. L'invio in batch significa suddividere una richiesta in parti più piccole da sottoporre a un processore per migliorare l'utilizzo dell'hardware. L'invio in batch non è efficiente e può provocare latenza. Dal momento che Brainwave non richiede l'invio in batch, la latenza è 10 volte inferiore rispetto alle CPU e GPU.

- Flessibilità

    I dispositivi FPGA possono essere riconfigurati per diversi tipi di modelli di machine learning. Questa flessibilità consente di accelerare le applicazioni basandosi sul modello ottimale di memoria e precisione numerica utilizzato.

    Nuove tecniche di machine learning vengano sviluppate costantemente e la progettazione hardware di Project Brainwave evolve altrettanto rapidamente. Dal momento che i dispositivi FPGA sono riconfigurabili, è possibile rimanere al passo della rapida evoluzione dei requisiti degli algoritmi AI.

- Scalabilità

    Microsoft Azure ha realizzato l'investimento in dispositivi FPGA per il cloud più importante al mondo. È possibile eseguire Brainwave sull'infrastruttura scalabile di Azure.

Un'anteprima di Project Brainwave integrato con Azure Machine Learning è attualmente disponibile. È disponibile anche un'anteprima limitata per estendere l'utilizzo di Project Brainwave e sfruttare tale velocità di elaborazione in aziende e strutture.

Nell'anteprima corrente l'impiego di Brainwave è limitato alla distribuzione TensorFlow e alle reti neurali basate su ResNet50 su hardware FPGA Intel per il riconoscimento e la classificazione delle immagini. È previsto il supporto di più modelli di raccolta e altri framework.

Negli scenari seguenti vengono usati i dispositivi FPGA sull'architettura Project Brainwave:

- Sistema di ispezione ottica automatizzato. Vedere [Real-time AI: Microsoft announces preview of Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) (AI in tempo reale: Microsoft annuncia l'anteprima di Project Brainwave).
- Mapping della copertura del suolo. Vedere [How to Use FPGAs for Deep Learning Inference to Perform Land Cover Mapping on Terabytes of Aerial Images](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/) (Come usare i dispositivi FPGA per inferenze di apprendimento approfondito per eseguire il mapping della copertura del suolo su terabyte di immagini aeree).

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>Come distribuire un servizio Web in più dispositivi FPGA

Il flusso di alto livello per la creazione di un servizio di riconoscimento immagini in Azure utilizzando ResNet50 come creatore di funzioni è il seguente:

1. ResNet50 è già stato distribuito in Brainwave. È possibile compilare gli altri grafici con TensorFlow (immissione della data, classificazione e così via) e definire una pipeline (input -> creare funzioni -> classificare) utilizzando un file json di definizione del servizio. Comprimere la definizione e i grafici in un file zip e caricare il file zip in Archivio BLOB di Azure.
2. Registrare il modello tramite l'API Gestione modelli di ML di Azure insieme al file zip nell'archivio BLOB.
3. Distribuire il servizio con il modello registrato tramite l'API Gestione modelli di ML.

Per altre informazioni su questo processo, vedere l'articolo [Come distribuire un modello come servizio Web in un FPGA con Azure Machine Learning](how-to-deploy-fpga-web-service.md).


## <a name="start-using-fpga"></a>Iniziare a usare i dispositivi FPGA

I modelli con accelerazione hardware di Azure Machine Learning consentono di distribuire modelli di training DNN ai dispositivi FPGA nel cloud di Azure. Per iniziare, vedere:

- [Deploy a model as a web service on an FPGA](how-to-deploy-fpga-web-service.md) (Distribuire un modello come servizio Web in un dispositivo FPGA)
- [Modelli con accelerazione hardware di Microsoft Azure Machine Learning con tecnologia Project Brainwave](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>Passaggi successivi

[Deploy a model as a web service on an FPGA](how-to-deploy-fpga-web-service.md) (Distribuire un modello come servizio Web in un dispositivo FPGA)

[Learn how to install the FPGA SDK](reference-fpga-package-overview.md) (Informazioni sull'installazione dell'SDK del dispositivo FPGA)

[Hyperscale hardware: ML at scale on top of Azure + FPGA : Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY) (Hardware iperscalabile: ML su scala basato su Azure + FPGA: Build 2018)

[Inside the Microsoft FPGA-based configurable cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063) (Informazioni sul cloud configurabile basato su Microsoft FPGA)

[Microsoft unveils Project Brainwave for real-time AI](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/) (Microsoft presenta Project Brainwave per AI in tempo reale)