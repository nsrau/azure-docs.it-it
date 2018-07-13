---
title: Eseguire il debug di contenitori Windows con Service Fabric e Visual Studio | Microsoft Docs
description: Informazioni su come eseguire il debug dei contenitori Windows in Azure Service Fabric con Visual Studio 2017.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/14/2018
ms.author: mikhegn
ms.openlocfilehash: 437c38a8e674fcdf06e26a7191ceecef9d901470
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968321"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Procedura: eseguire il debug dei contenitori Windows in Azure Service Fabric con Visual Studio 2017

Con Visual Studio 2017 Update 7 (15.7) è possibile eseguire il debug delle applicazioni .NET nei contenitori come servizi di Service Fabric. Questo articolo illustra come configurare l'ambiente ed eseguire il debug di un'applicazione .NET in un contenitore in esecuzione in un cluster di Service Fabric locale.

## <a name="prerequisites"></a>prerequisiti

* In Windows 10, seguire questa guida introduttiva per [configurare Windows 10 per l'esecuzione dei contenitori Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* In Windows Server 2016, seguire questa guida introduttiva per [configurare Windows 2016 per l'esecuzione dei contenitori Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Configurare l'ambiente di Service Fabric locale seguendo le indicazioni in [Preparare l'ambiente di sviluppo in Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Configurare l'ambiente di sviluppo per l'esecuzione del debug dei contenitori

1. Assicurarsi che il servizio Docker per Windows sia in esecuzione prima di procedere con il passaggio successivo.

1. Per supportare la risoluzione DNS tra i contenitori, sarà necessario configurare il cluster di sviluppo locale usando il nome del computer.
    1. Aprire PowerShell come amministratore
    1. Passare alla cartella di installazione del cluster SDK, in genere `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`
    1. Eseguire lo script `DevClusterSetup.ps1` con il parametro `-UseMachineName`

    ``` PowerShell
      C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -UseMachineName
    ```

    > [!NOTE]
    > È possibile usare `-CreateOneNodeCluster` per configurare un cluster con un solo nodo. Per impostazione predefinita, verrà creato un cluster locale con cinque nodi.
    >

    Per altre informazioni sul servizio DNS in Service Fabric, vedere [Servizio DNS in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Limitazioni note durante il debug dei contenitori in Service Fabric

Di seguito sono elencate le limitazioni note per il debug dei contenitori in Service Fabric e le possibili risoluzioni:

* L'uso di localhost per ClusterFQDNorIP non supporta la risoluzione DNS nei contenitori.
    * Soluzione: configurare il cluster locale usando il nome del computer (vedere sopra)
* Windows 10 in esecuzione in una macchina virtuale non reinvia la risposta DNS al contenitore.
    * Soluzione: disabilitare l'offload del checksum UDP per IPv4 sulla scheda di interfaccia di rete delle macchine virtuali
    * Tenere presente che questo influirà negativamente sulle prestazioni di rete del computer.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* La risoluzione dei servizi nella stessa applicazione tramite il nome del servizio DNS non funziona in Windows 10, se l'applicazione è stata distribuita tramite Docker Compose.
    * Soluzione: usare servicename.applicationname per la risoluzione degli endpoint del servizio
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Se si usa l'indirizzo IP per ClusterFQDNorIP, la modifica dell'indirizzo IP primario nell'host interromperà la funzionalità DNS.
    * Soluzione: ricreare il cluster usando il nuovo indirizzo IP primario nell'host o usare il nome del computer. Si tratta di un comportamento previsto da progettazione.
* Se il nome FQDN con cui è stato creato il cluster non è risolvibile nella rete, il DNS avrà esito negativo.
    * Soluzione: ricreare il cluster locale usando l'indirizzo IP primario dell'host. Si tratta di un comportamento previsto da progettazione.
* Durante il debug di un contenitore, i log di Docker saranno disponibili solo nella finestra di output di Visual Studio, non tramite le API di Service Fabric, incluso Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Eseguire il debug di un'applicazione .NET in esecuzione in contenitori Docker in Service Fabric

1. Eseguire Visual Studio come amministratore.

1. Aprire un'applicazione .NET esistente o crearne una nuova.

1. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Aggiungi -> Supporto per l'agente di orchestrazione del contenitore -> Service Fabric**

1. Premere **F5** per avviare il debug dell'applicazione.

    Visual Studio supporta i tipi di progetto console e ASP.NET per .NET e .NET Core.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità di Service Fabric e dei contenitori, vedere la [panoramica dei contenitori di Service Fabric](service-fabric-containers-overview.md).