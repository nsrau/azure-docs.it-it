---
title: Rilevare il movimento e generare eventi - Azure
description: Questo avvio rapido illustra come usare Live Video Analytics in IoT Edge per rilevare il movimento e generare eventi mediante la chiamata diretta dei metodi a livello di codice.
ms.topic: quickstart
ms.date: 08/10/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 45595e8e6aa53b41d6d8b0d9d11457468dc47013
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690935"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Avvio rapido: Rilevare il movimento e generare eventi

Questo avvio rapido illustra la procedura per iniziare a usare Live Video Analytics in IoT Edge. Usa una macchina virtuale di Azure come dispositivo IoT Edge e un flusso video live simulato. Dopo aver completato i passaggi di configurazione, sarà possibile eseguire un flusso video live simulato tramite un grafo multimediale che rileva e segnala qualsiasi movimento in tale flusso. Il diagramma seguente mostra una rappresentazione grafica del grafo multimediale.

![Analisi di video live basata su un rilevamento del movimento](./media/analyze-live-video/motion-detection.png) 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-emit-events-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Prerequisiti

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-emit-events-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-emit-events-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="set-up-azure-resources"></a>Configurare le risorse di Azure

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup-azure-resources](includes/detect-motion-emit-events-quickstart/csharp/setup-azure-resources.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup-azure-resources](includes/detect-motion-emit-events-quickstart/python/setup-azure-resources.md)]
::: zone-end

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup-development-environment](includes/detect-motion-emit-events-quickstart/csharp/setup-development-environment.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup-development-environment](includes/detect-motion-emit-events-quickstart/python/setup-development-environment.md)]
::: zone-end

## <a name="examine-the-sample-files"></a>Esaminare i file di esempio

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/detect-motion-emit-events-quickstart/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/detect-motion-emit-events-quickstart/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione

::: zone pivot="programming-language-csharp"
[!INCLUDE [generate-deploy-deployment-manifest](includes/detect-motion-emit-events-quickstart/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [generate-deploy-deployment-manifest](includes/detect-motion-emit-events-quickstart/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="prepare-to-monitor-events"></a>Preparare il monitoraggio degli eventi

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitor-events](includes/detect-motion-emit-events-quickstart/csharp/prepare-monitor-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitor-events](includes/detect-motion-emit-events-quickstart/python/prepare-monitor-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Eseguire il programma di esempio

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-emit-events-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-emit-events-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretare i risultati

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-emit-events-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare gli altri argomenti di avvio rapido, non eliminare le risorse create. In caso contrario, nel portale di Azure passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stato eseguito questo argomento di avvio rapido e quindi eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

Eseguire gli altri argomenti di avvio rapido, ad esempio quello relativo al rilevamento di un oggetto in un feed video live.        
