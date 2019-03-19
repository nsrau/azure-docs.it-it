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
ms.openlocfilehash: 0ab6e3f189d4a2e7e8f3bc96108d7979c99fffa8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102670"
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