---
title: Definizione di FPGA e Project Brainwave - Azure Machine Learning
description: Informazioni sull'accelerazione di modelli e reti neurali profonde con i dispositivi FPGA su Azure. Questo articolo fornisce un'introduzione ai dispositivi field-programmable gate array (FPGA) e su come Azure Machine Learning fornisca intelligenza artificiale (AI) in tempo reale quando si distribuisce il modello a un FPGA di Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 9/24/2018
ms.openlocfilehash: adcd812bc63f98e4cbde949946bf32a037c6d704
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158688"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Definizione di FPGA e Project Brainwave

Questo articolo fornisce un'introduzione ai dispositivi field-programmable gate array (FPGA) e su come Azure Machine Learning fornisca intelligenza artificiale (AI) in tempo reale quando si distribuisce il modello a un FPGA di Azure.

Un dispositivo FPGA contiene una matrice di blocchi programmabili per la logica e una gerarchia di interconnessioni riconfigurabili. Le interconnessioni consentono di configurare questi blocchi in diversi modi nella post-produzione. I dispositivi FPGA offrono una combinazione di programmabilità e prestazioni superiore agli altri chip.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA e CPU, GPU, ASIC a confronto

![Confronto dei dispositivi FPGA in Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processore||DESCRIZIONE|
|---|:-------:|------|
|Application-specific integrated circuit|ASIC|I circuiti personalizzati, ad esempio Google TensorFlow Processor Unit (TPU), offrono la massima efficienza. Non possono essere riconfigurati in base alle esigenze.|
|Field-programmable Gate Arrays|FPGA|I dispositivi FPGA, ad esempio quelli disponibili su Azure, forniscono prestazioni simili agli ASIC, ma sono flessibili e possono essere riconfigurati nel corso del tempo per implementare la nuova logica.|
|Graphics processing unit|GPU|Un'opzione comune per i calcoli di intelligenza artificiale che offre funzionalità di elaborazione parallela e rende più veloce il rendering delle immagini rispetto alle CPU.|
|Central processing unit|CPU|Le prestazioni delle CPU non sono ideali per l'elaborazione di video e immagini.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave in Azure

[Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/) è l'architettura hardware economica di Microsoft, basata su dispositivi Intel FPGA che data Scientist e sviluppatori usano per accelerare i calcoli per l'intelligenza artificiale in tempo reale.  L'architettura abilitata FPGA offre **prestazioni**, **flessibilità**, **scalabilità** ed è disponibile su Azure.

**I dispositivi FPGA consentono di ottenere una bassa latenza per le richieste di inferenza in tempo reale.** L'invio in batch, raccoglie di una grande quantità di dati e li inserisce in un processore per migliorare l'uso dell'hardware. L'invio in batch può causare latenza perché rende necessario elaborare più dati, ma può migliorare la velocità effettiva. Le implementazioni delle unità di elaborazione neurale Project Brainwave non richiedono l'invio in batch; di conseguenza la latenza può essere molto inferiore rispetto a CPU e GPU.

### <a name="reconfigurable-power"></a>Potenza riconfigurabile
**I dispositivi FPGA possono essere riconfigurati per diversi tipi di modelli di machine learning.** Questa flessibilità consente di accelerare le applicazioni basandosi sul modello ottimale di memoria e precisione numerica utilizzato.

Nuove tecniche di machine learning vengano sviluppate costantemente e la progettazione hardware di Project Brainwave evolve altrettanto rapidamente. Dal momento che i dispositivi FPGA sono riconfigurabili, è possibile rimanere al passo della rapida evoluzione dei requisiti degli algoritmi AI.

### <a name="whats-supported-on-azure"></a>Attività supportate su Azure
**Microsoft Azure ha realizzato l'investimento in dispositivi FPGA per il cloud più importante al mondo.** È possibile eseguire Brainwave sull'infrastruttura scalabile di Azure.

Ad oggi, Project Brainwave supporta:
+ Scenari di riconoscimento e classificazione di immagini
+ Distribuzione di TensorFlow
+ DNNs: ResNet 50, ResNet 152, VGG-16, SSD-VGG, e DenseNet-121
+ Hardware Intel FPGA 

Con questa architettura hardware abilitata per FPGA, le reti neurali sottoposte a training vengono eseguite rapidamente e con una latenza inferiore. La tecnologia Project Brainwave è in grado di parallelizzare le reti neurali profonde con training preliminare tra i dispositivi FPGA, in modo da ridimensionare il servizio. È possibile eseguire training preliminare sulle reti neurali profonde, per utilità di funzioni complete per il trasferimento di apprendimento o per ottimizzazione con pesi aggiornati.

### <a name="scenarios-and-applications"></a>Scenari e applicazioni

Project Brainwave non è integrato con le funzionalità di Azure Machine Learning. Microsoft usa i dispositivi FPGA per la valutazione delle reti neurali profonde, per la classificazione della ricerca Bing e per accelerazione del software defined networking (SDN) in modo da ridurre la latenza mentre la CPU viene liberata da altre attività.

Negli scenari seguenti vengono usati i dispositivi FPGA sull'architettura Project Brainwave:
+ [Sistema di ispezione ottica automatizzato](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapping della copertura del suolo](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Distribuire ai dispositivi FPGA su Azure

Il flusso di lavoro per la creazione di un servizio di riconoscimento di immagini in Azure tramite reti neurali profonde supportate come utilità di funzioni per la distribuzione ai dispositivi FPGA su Azure:

1. Usare Azure Machine Learning SDK per Python per creare una definizione del servizio, ovvero un file che descrive una pipeline di grafici (input, utilità di funzioni e classificatore), basato su TensorFlow. Il comando di distribuzione comprime la definizione e i grafici in un file ZIP e carica il file zip in Archivio BLOB di Azure.  Le rete neurali profonde sono già distribuite in Project Brainwave per essere eseguite sul dispositivo FPGA.

1. Registrare il modello usando SDK con il file ZIP nell'archivio BLOB di Azure.

1. Distribuire il servizio con il modello registrato tramite SDK.

Con questo articolo è possibile iniziare a distribuire i modelli di rete neurale profonda su cui è stato eseguito il training ai dispositivi FPGA nel cloud di Azure **"[Distribuire un modello come servizio Web in un dispositivo FPGA](how-to-deploy-fpga-web-service.md)"**.


## <a name="next-steps"></a>Passaggi successivi

Consultare questi video e blog:

+ [Hyperscale hardware: ML at scale on top of Azure + FPGA : Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY) (Hardware iperscalabile: ML su scala basato su Azure + FPGA: Build 2018)

+ [Inside the Microsoft FPGA-based configurable cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063) (Informazioni sul cloud configurabile basato su Microsoft FPGA)

+ [Microsoft unveils Project Brainwave for real-time AI: project homepage](https://www.microsoft.com/research/project/project-brainwave/) (Microsoft presenta Project Brainwave per AI in tempo reale: home page)
