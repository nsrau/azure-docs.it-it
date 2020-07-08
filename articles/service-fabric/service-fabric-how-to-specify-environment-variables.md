---
title: Specificare le variabili di ambiente per i servizi
description: Questo articolo illustra come usare le variabili di ambiente per le applicazioni in Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75614316"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Come specificare le variabili di ambiente per i servizi in Service Fabric

Questo articolo illustra come specificare le variabili di ambiente per un servizio o un contenitore in Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedura per specificare le variabili di ambiente per i servizi

In questo esempio si imposta una variabile di ambiente per un contenitore. Si presuppone che i manifesti dell'applicazione e del servizio siano già disponibili.

1. Aprire il file ServiceManifest.xml.
2. In `CodePackage` aggiungere un nuovo elemento `EnvironmentVariables` e un elemento `EnvironmentVariable` per ogni variabile di ambiente.

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

3. A tale scopo, usare l'elemento `EnvironmentOverrides`.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Specifica di variabili di ambiente in modo dinamico tramite Docker Compose

Service Fabric supporta la possibilità di [usare Docker compose per la distribuzione](service-fabric-docker-compose.md#supported-compose-directives). I file compose possono avere origini variabili di ambiente dalla Shell. Questo comportamento può essere usato per sostituire dinamicamente i valori di ambiente desiderati:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su alcuni dei concetti principali trattati in questo articolo, vedere [Gestire le applicazioni per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

Per informazioni su altre funzionalità di gestione delle app disponibili in Visual Studio, vedere [Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni di Service Fabric](service-fabric-manage-application-in-visual-studio.md).
