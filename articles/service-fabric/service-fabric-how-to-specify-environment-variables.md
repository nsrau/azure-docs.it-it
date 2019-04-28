---
title: Come specificare le variabili di ambiente per i servizi in Azure Service Fabric | Microsoft Docs
description: Questo articolo illustra come usare le variabili di ambiente per le applicazioni in Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f75de635f08ae06db349387a436c636c149ec9f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720230"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Come specificare le variabili di ambiente per i servizi in Service Fabric

Questo articolo illustra come specificare le variabili di ambiente per un servizio o un contenitore in Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedura per specificare le variabili di ambiente per i servizi

In questo esempio si imposta una variabile di ambiente per un contenitore. Si presuppone che i manifesti dell'applicazione e del servizio siano già disponibili.

1. Aprire il file ServiceManifest.xml.
1. In `CodePackage` aggiungere un nuovo elemento `EnvironmentVariables` e un elemento `EnvironmentVariable` per ogni variabile di ambiente.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    È possibile eseguire l'override delle variabili di ambiente nel manifesto dell'applicazione.

1. A tale scopo, usare l'elemento `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su alcuni dei concetti principali trattati in questo articolo, vedere [Gestire le applicazioni per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

Per informazioni su altre funzionalità di gestione delle app disponibili in Visual Studio, vedere [Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni di Service Fabric](service-fabric-manage-application-in-visual-studio.md).