---
title: Quali sono le matrici di campo-programmable gate (FPGA)
titleSuffix: Azure Machine Learning service
description: Informazioni sull'accelerazione di modelli e reti neurali profonde con i dispositivi FPGA su Azure. Questo articolo contiene un'introduzione ai Field-Programmable Gate Array (FPGA) e illustra come il servizio Azure Machine Learning fornisca l'intelligenza artificiale (AI) in tempo reale quando si distribuisce il modello in un circuito FPGA di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 1a690ea350ea98589e9134cd6f401c6ac3c58083
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851015"
---
# <a name="what-are-field-programmable-gate-arrays-fpga"></a>Quali sono le matrici di campo-programmable gate (FPGA)

Questo articolo contiene un'introduzione ai Field-Programmable Gate Array (FPGA) e illustra come il servizio Azure Machine Learning fornisca l'intelligenza artificiale (AI) in tempo reale quando si distribuisce il modello in un circuito FPGA di Azure.

Un circuito FPGA contiene un array di blocchi programmabili per la logica e una gerarchia di interconnessioni riconfigurabili. Le interconnessioni consentono di configurare questi blocchi in diversi modi dopo la produzione. I circuiti FPGA offrono una combinazione di programmabilità e prestazioni superiore agli altri chip.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA e CPU, GPU, ASIC a confronto

Il diagramma e la tabella seguenti evidenziano un confronto tra i circuiti FPGA e gli altri processori.

![Diagramma di confronto dei dispositivi FPGA nel servizio Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processore||Descrizione|
|---|:-------:|------|
|Application-specific integrated circuit|ASIC|I circuiti personalizzati, ad esempio Google TensorFlow Processor Unit (TPU), offrono la massima efficienza. Non possono essere riconfigurati in base alle esigenze.|
|Field-programmable Gate Arrays|FPGA|I circuiti FPGA, ad esempio quelli disponibili in Azure, assicurano prestazioni simili a quelle dei circuiti ASIC. Sono flessibili e possono essere riconfigurati nel corso del tempo per implementare la nuova logica.|
|Graphics processing unit|GPU|Una scelta comune per i calcoli di intelligenza artificiale che offre funzionalità di elaborazione parallela e rende più veloce il rendering delle immagini rispetto alle CPU.|
|Central processing unit|CPU|Processori generici, le cui prestazioni non sono ideali per l'elaborazione di video e grafica.|

FPGA in Azure si basa sui dispositivi di Intel FPGA, quali i data Scientist e sviluppatori usano per accelerare i calcoli per intelligenza artificiale in tempo reale. L'architettura abilitata per i circuiti FPGA offre un livello elevato di prestazioni, flessibilità, scalabilità ed è disponibile in Azure.

FPGA consentono di ottenere le richieste a bassa latenza per l'inferenza in tempo reale (o modello di punteggio). Non sono necessarie richieste asincrone (invio in batch). L'invio in batch può causare latenza perché rende necessario elaborare più dati. Le implementazioni delle unità di elaborazione neurale non richiedono l'invio in batch; di conseguenza la latenza può essere più volte inferiore, rispetto ai processori CPU e GPU.

### <a name="reconfigurable-power"></a>Potenza riconfigurabile
I circuiti FPGA possono essere riconfigurati per diversi tipi di modelli di Machine Learning. Questa flessibilità consente di accelerare le applicazioni basandosi sul modello ottimale di memoria e precisione numerica utilizzato. Dal momento che sono riconfigurabili, è possibile rimanere al passo con i requisiti degli algoritmi AI in costante evoluzione.

### <a name="whats-supported-on-azure"></a>Attività supportate su Azure
Microsoft Azure ha realizzato l'investimento in dispositivi FPGA per il cloud più importante al mondo. FPGA su Azure supporta:

+ Scenari di riconoscimento e classificazione di immagini
+ Distribuzione di TensorFlow
+ Reti neurali profonde: ResNet 50, ResNet 152, VGG-16, SSD-VGG e DenseNet-121
+ Hardware Intel FPGA 

Con questa architettura hardware abilitata per FPGA, le reti neurali sottoposte a training vengono eseguite rapidamente e con una latenza inferiore. Azure è possibile parallelizzare FPGA per scalare orizzontalmente il servizio di con training preliminare reti neurali profonde (DNN). È possibile eseguire training preliminare sulle reti neurali profonde, per utilità di funzioni complete per il trasferimento di apprendimento o per ottimizzazione con pesi aggiornati.

### <a name="scenarios-and-applications"></a>Scenari e applicazioni

Azure FPGA è integrate con Azure Machine Learning. Microsoft usa i circuiti FPGA per la valutazione delle reti neurali profonde, per la classificazione delle ricerche Bing e per l'accelerazione della tecnologia SDN (Software Defined Networking) per ridurre la latenza, rendendo disponibile la CPU per altre attività.

Gli scenari seguenti usano FPGA:
+ [Sistema di ispezione ottica automatizzato](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapping della copertura del suolo](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Distribuire ai dispositivi FPGA su Azure

Per creare un servizio di riconoscimento di immagini in Azure, è possibile usare le reti neurali profonde supportate come utilità di funzioni per la distribuzione su circuiti FPGA in Azure:

1. Usare [Azure Machine Learning SDK per Python](https://aka.ms/aml-sdk) per creare una definizione del servizio. Una definizione del servizio è un file che descrive una pipeline di grafici (input, utilità di funzioni e classificatore) basata su TensorFlow. Il comando di distribuzione comprime automaticamente la definizione e i grafici in un file ZIP e carica il file zip in Archiviazione BLOB di Azure. La rete neurale profonda è già stata distribuita per eseguirvi il FPGA.

1. Registrare il modello usando l'SDK con il file ZIP in Archiviazione BLOB di Azure.

1. Distribuire il servizio con il modello registrato usando l'SDK.

Per iniziare a distribuire i modelli di rete neurale profonda su cui è stato eseguito il training ai circuiti FPGA nel cloud di Azure, vedere [Distribuire un modello come servizio Web in un circuito FPGA](how-to-deploy-fpga-web-service.md).


## <a name="next-steps"></a>Passaggi successivi

Consultare questi video e blog:

+ [Hyperscale hardware: ML at scale on top of Azure + FPGA: Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY) (Hardware iperscalabile: ML su scala basato su Azure + FPGA: Build 2018)

+ [Inside the Microsoft FPGA-based configurable cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063) (Informazioni sul cloud configurabile basato su Microsoft FPGA)

+ [Microsoft unveils Project Brainwave for real-time AI: project homepage](https://www.microsoft.com/research/project/project-brainwave/) (Microsoft presenta Project Brainwave per AI in tempo reale: home page)
