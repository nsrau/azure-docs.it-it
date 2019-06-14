---
title: Ambiente di sviluppo per Azure IoT Edge | Microsoft Docs
description: Informazioni su sistemi supportati e strumenti di sviluppo di Microsoft che possono aiutare a creare moduli IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6fc2af0cbe770ee787da757966bbc1647717e5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66302677"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Preparare l'ambiente di sviluppo e test per IoT Edge

Azure IoT Edge sposta la logica di business esistente ai dispositivi perimetrali. Per preparare le applicazioni e i carichi di lavoro per l'esecuzione come [moduli IoT Edge](iot-edge-modules.md), è necessario compilarli come contenitori. Questo articolo offre indicazioni su come configurare l'ambiente di sviluppo in modo da poter creare una soluzione IoT Edge. Dopo aver configurato l'ambiente di sviluppo, è possibile imparare a [sviluppare i propri moduli di IoT Edge](module-development.md).

In qualsiasi soluzione IoT Edge sono presenti almeno due computer da tenere in considerazione. Uno è il dispositivo (o i dispositivi) IoT Edge stesso, che esegue il modulo IoT Edge. L'altro è il computer di sviluppo che viene usato per compilare, testare e distribuire i moduli. Questo articolo è incentrato principalmente sul computer di sviluppo. A scopo di test, i due computer possono corrispondere. È possibile eseguire IoT Edge nel computer di sviluppo e distribuire i moduli a tale computer.

## <a name="operating-system"></a>Sistema operativo

Azure IoT Edge viene eseguito in un set specifico di [sistemi operativi supportati](support.md). Per lo sviluppo per IoT Edge, è possibile usare la maggior parte dei sistemi operativi che possono eseguire un motore del contenitore. Il motore del contenitore è un requisito nel computer di sviluppo per compilare i moduli come contenitori ed eseguirne il push in un registro contenitori. 

Se il computer di sviluppo non può eseguire Azure IoT Edge, continuare a leggere l'articolo per conoscere gli [strumenti di test](#testing-tools) per il test e il debug in locale. 

Il sistema operativo del computer di sviluppo non deve necessariamente corrispondere al sistema operativo del dispositivo IoT Edge. Tuttavia il sistema operativo del contenitore deve essere coerente nel computer di sviluppo e nel dispositivo IoT Edge. Ad esempio, è possibile sviluppare moduli in un computer Windows e distribuirli a un dispositivo Linux. Il computer Windows deve eseguire contenitori Linux per compilare i moduli per il dispositivo Linux. 

## <a name="container-engine"></a>Motore del contenitore

Il concetto centrale di IoT Edge è che è possibile distribuire in modalità remota la logica di business e cloud ai dispositivi creando pacchetti nei contenitori. Per compilare i contenitori, è necessario un motore del contenitore nel computer di sviluppo. 

L'unico motore del contenitore supportato per i dispositivi IoT Edge nell'ambiente di produzione è Moby. Tuttavia qualsiasi motore del contenitore compatibile con Open Container Initiative, come Docker, è in grado di compilare immagini del modulo IoT Edge. 

## <a name="development-tools"></a>Strumenti di sviluppo

Sia Visual Studio che Visual Studio Code hanno estensioni aggiuntive per sviluppare soluzioni IoT Edge. Queste estensioni forniscono modelli specifici della lingua per aiutare a creare e distribuire nuovi scenari di IoT Edge. Le estensioni di Azure IoT Edge per Visual Studio e Visual Studio Code aiutano a codificare, compilare e distribuire soluzioni IoT Edge e a eseguirne il debug. È possibile creare un'intera soluzione IoT Edge contenente più moduli; le estensioni aggiornano automaticamente un modello di manifesto della distribuzione con ogni nuova aggiunta di moduli. Con le estensioni, è anche possibile gestire i dispositivi IoT dall'interno di Visual Studio o Visual Studio Code. Distribuire moduli in un dispositivo, monitorare lo stato e visualizzare i messaggi man mano che arrivano all'hub IoT. Entrambe le estensioni usano lo [strumento di sviluppo IoT EdgeHub](#iot-edgehub-dev-tool) per consentire l'esecuzione e il debug locale dei moduli anche nel computer di sviluppo. 

Se si preferisce sviluppare con altri editor o dall'interfaccia della riga di comando, lo strumento di sviluppo di Azure IoT Edge fornisce comandi in modo che sia possibile sviluppare e testare dalla riga di comando. È possibile creare nuovi scenari IoT Edge, compilare immagini del modulo, eseguire moduli in un simulatore e monitorare i messaggi inviati all'hub IoT. 

### <a name="visual-studio-code-extension"></a>Estensione Visual Studio Code

L'estensione Azure IoT Edge per Visual Studio Code offre modelli di moduli IoT Edge basati su linguaggi di programmazione come C, C#, Java, Node.js e Python, nonché funzioni di Azure in C#. 

Per altre informazioni e per il download, vedere [Azure IoT Tools for Visual Studio Code (Azure IoT Tools per Visual Studio Code)](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Oltre alle estensioni di IoT Edge, può essere utile installare estensioni aggiuntive per lo sviluppo. Ad esempio, è possibile usare [il supporto di Docker per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) per gestire immagini, contenitori e registri. Inoltre, tutti i principali linguaggi supportati hanno estensioni per Visual Studio Code che possono risultare utili quando si sviluppano i moduli. 

#### <a name="prerequisites"></a>Prerequisiti

I modelli di moduli per alcuni linguaggi e servizi prevedono prerequisiti per compilare le cartelle di progetto nel computer di sviluppo con Visual Studio Code.

| Modello di modulo | Prerequisito |
| --------------- | ------------ |
| Funzioni di Azure | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [Impostare la variabile di ambiente JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Generatore del modulo Azure IoT Edge Node.js](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/installation.html) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Estensione di Visual Studio 2017/2019

Gli strumenti di Azure IoT Edge per Visual Studio forniscono un IoT Edge basato su modello di modulo C# e C. 

Per altre informazioni e per scaricare, vedere [strumenti di Azure IoT Edge per Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) oppure [strumenti di Azure IoT Edge per Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>Strumento di sviluppo IoT Edge

Lo strumento di sviluppo di Azure IoT Edge consente di semplificare lo sviluppo di IoT Edge con le capacità della riga di comando. Questo strumento offre comandi dell'interfaccia della riga di comando per sviluppare e testare i moduli ed eseguirne il debug. Lo strumento di sviluppo IoT Edge è compatibile con il sistema di sviluppo, se sono state installate manualmente le dipendenze nel computer ma anche se si sta usando il contenitore di sviluppo IoT Edge. 

Per altre informazioni e per iniziare, vedere il [wiki sullo strumento di sviluppo IoT Edge](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Strumenti di test

Sono disponibili vari strumenti di test per aiutare a simulare i dispositivi IoT Edge o eseguire il debug dei moduli in modo più efficiente. La tabella seguente illustra un confronto generale tra gli strumenti, mentre le singole sezioni descrivono ogni strumento in modo più specifico. 

Per le distribuzioni di produzione è supportato solo il runtime IoT Edge, tuttavia gli strumenti seguenti consentono di simulare o creare facilmente dispositivi IoT Edge per scopi di sviluppo e test. Questi strumenti non si escludono a vicenda, ma possono essere combinati per un'esperienza completa di sviluppo. 

| Strumento | Noto anche come | Piattaforme supportate | Ideale per |
| ---- | ------------- | ------------------- | --------- |
| Strumento di sviluppo IoT EdgeHub  | iotedgehubdev | Windows, Linux, MacOS | Simulazione di un dispositivo per il debug dei moduli. |
| Contenitore per lo sviluppo IoT Edge | microsoft/iotedgedev | Windows, Linux, MacOS | Sviluppo senza installazione delle dipendenze. |
| Runtime IoT Edge in un contenitore | iotedgec | Windows, Linux, MacOS, ARM | Test su un dispositivo che potrebbe non supportare il runtime. |
| Contenitore di dispositivi IoT Edge | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | Test di uno scenario con numerosi di dispositivi IoT Edge su larga scala. |

### <a name="iot-edgehub-dev-tool"></a>Strumento di sviluppo IoT EdgeHub

Lo strumento di sviluppo Azure IoT EdgeHub offre un'esperienza di sviluppo e debug locale. Lo strumento consente di avviare moduli senza runtime IoT Edge, in modo da poter creare, sviluppare ed eseguire moduli e soluzioni IoT Edge in locale ed eseguirne il debug. Non è necessario eseguire il push delle immagini in un registro contenitori e distribuirli a un dispositivo per il test.

Lo strumento di sviluppo IoT EdgeHub è stato progettato per funzionare in abbinamento con le estensioni di Visual Studio e Visual Studio Code, nonché con lo strumento di sviluppo IoT Edge. Supporta lo sviluppo a ciclo interno, nonché il test di ciclo esterno, pertanto si integra anche con gli strumenti DevOps. 

Per altre informazioni e per l'installazione, vedere [Azure IoT EdgeHub dev tool](https://pypi.org/project/iotedgehubdev/) (Strumento di sviluppo Azure IoT EdgeHub).

### <a name="iot-edge-dev-container"></a>Contenitore per lo sviluppo IoT Edge

Il contenitore per lo sviluppo Azure IoT Edge è un contenitore Docker con tutte le dipendenze necessarie per lo sviluppo di IoT Edge. Questo contenitore rende più semplice iniziare con qualsiasi linguaggio di programmazione desiderato per lo sviluppo, tra cui C#, Python, Node.js e Java. Occorre solo installare un motore di contenitore, come Docker o Moby, per eseguire il pull del contenitore nel computer di sviluppo. 

Per altre informazioni, vedere [Azure IoT Edge dev container](https://hub.docker.com/r/microsoft/iotedgedev/) (Contenitore per lo sviluppo Azure IoT Edge).

### <a name="iot-edge-runtime-in-a-container"></a>Runtime IoT Edge in un contenitore

Il runtime IoT Edge in un contenitore offre un runtime completo che accetta la stringa di connessione del dispositivo come variabile di ambiente. Questo contenitore consente di testare scenari e moduli IoT Edge in un sistema che potrebbe non supportare il runtime in modo nativo, ad esempio MacOS. Eventuali moduli distribuiti verranno avviati all'esterno del contenitore di runtime. Se si desidera che il runtime e gli eventuali moduli distribuiti esistano all'interno dello stesso contenitore, prendere invece in considerazione il contenitore di dispositivi IoT Edge.

Per altre informazioni, vedere [Running Azure IoT Edge in a container](https://github.com/Azure/iotedgedev/tree/master/docker/runtime) (Eseguire Azure IoT Edge in un contenitore).

### <a name="iot-edge-device-container"></a>Contenitore di dispositivi IoT Edge

Il contenitore di dispositivi IoT Edge è un dispositivo IoT Edge completo, pronto per essere avviato in qualsiasi computer con un motore del contenitore. Il contenitore di dispositivi include il runtime IoT Edge e il motore del contenitore stesso. Ogni istanza del contenitore è un dispositivo IoT Edge di provisioning automatico completamente funzionale. Il contenitore di dispositivi supporta il debug remoto dei moduli, purché sia presente una route di rete verso il modulo. Il contenitore di dispositivi è ideale per la creazione rapida di un numero elevato di IoT Edge ai dispositivi di test di scenari su larga scala o le pipeline di Azure. Supporta inoltre la distribuzione in Kubernetes tramite helm. 

Per altre informazioni, vedere [Azure IoT Edge device container](https://github.com/toolboc/azure-iot-edge-device-container) (Contenitore di dispositivi Azure IoT Edge).

## <a name="devops-tools"></a>Strumenti DevOps

Quando si è pronti per sviluppare soluzioni su larga scala per scenari di produzione completi, sfruttare i vantaggi dei principi DevOps moderni tra cui automazione, monitoraggio e processi semplificati di ingegneria del software. IoT Edge include estensioni per supportare strumenti DevOps, tra cui Azure DevOps, Azure DevOps Projects e Jenkins. Se si desidera personalizzare una pipeline esistente oppure usare un altro strumento DevOps, ad esempio CircleCI o TravisCI, è possibile farlo con le funzionalità dell'interfaccia della riga di comando incluse nello strumento di sviluppo IoT Edge.

Per altre informazioni, indicazioni ed esempi, vedere le pagine seguenti:
* [Integrazione e distribuzione continue in Azure IoT Edge](how-to-ci-cd.md)
* [Creare una pipeline CI/CD per IoT Edge con Azure DevOps Projects](how-to-devops-project.md)
* [Azure IoT Edge Jenkins plugin](https://plugins.jenkins.io/azure-iot-edge) (Plugin Jenkins per Azure IoT Edge)
* [IoT Edge DevOps GitHub repo](https://github.com/toolboc/IoTEdge-DevOps) (Repository GitHub DevOps IoT Edge)
