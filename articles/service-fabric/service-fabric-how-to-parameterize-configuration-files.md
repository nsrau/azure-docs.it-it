---
title: Aggiungere parametri ai file di configurazione in Azure Service Fabric | Microsoft Docs
description: Informazioni su come aggiungere parametri ai file di configurazione in Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 9057cdc22e277e4e12e9f439f3fbe0c5a5cda2a2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900514"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Come aggiungere parametri ai file di configurazione in Service Fabric

Questo articolo illustra come aggiungere parametri a un file di configurazione in Service Fabric.  Se non si ha già familiarità con i concetti di base di gestione delle applicazioni per più ambienti, leggere [Gestire le applicazioni per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedura per aggiungere parametri ai file di configurazione

In questo esempio si esegue l'override di un valore di configurazione definendo parametri nella distribuzione dell'applicazione.

1. Aprire il file *<MyService>\PackageRoot\Config\Settings.xml* nel progetto di servizio.
1. Impostare un nome e un valore di un parametro di configurazione, ad esempio una dimensione della cache pari a 25, aggiungendo il codice XML seguente:

  ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
  ```

1. Salvare e chiudere il file.
1. Aprire il file *<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml*.
1. Nel file ApplicationManifest.xml dichiarare un parametro e il valore predefinito nell'elemento `Parameters`.  È consigliabile assegnare al parametro un nome che includa quello del servizio, ad esempio "MyService".

  ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
  ```
1. Nella sezione `ServiceManifestImport` del file ApplicationManifest.xml aggiungere un elemento `ConfigOverride` che fa riferimento al pacchetto di configurazione, alla sezione e al parametro.

  ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  ```

> [!NOTE]
> Quando si aggiunge un elemento ConfigOverride, Service Fabric sceglie sempre i parametri dell'applicazione o il valore predefinito specificato nel manifesto dell'applicazione.
>
>

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su altre funzionalità di gestione delle app disponibili in Visual Studio, vedere [Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni di Service Fabric](service-fabric-manage-application-in-visual-studio.md).