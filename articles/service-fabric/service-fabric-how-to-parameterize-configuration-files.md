---
title: Come aggiungere parametri ai file di configurazione in Azure Service Fabric | Microsoft Docs
description: Questo articolo illustra come aggiungere parametri ai file di configurazione in Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 1e7d59ecb231440711b8ed3dc0b27a2b105890c4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Come aggiungere parametri ai file di configurazione in Service Fabric

Questo articolo illustra come aggiungere parametri a un file di configurazione in Service Fabric.

## <a name="procedure-for-parameterizing-configuration-files"></a>Procedura per aggiungere parametri ai file di configurazione

In questo esempio si esegue l'override di un valore di configurazione definendo parametri nella distribuzione dell'applicazione.

1. Aprire il file Config\Settings.xml.
1. Impostare un parametro di configurazione aggiungendo il codice XML seguente:

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. Salvare e chiudere il file.
1. Aprire il file `ApplicationManifest.xml`.
1. Aggiungere un elemento `ConfigOverride`, che fa riferimento al pacchetto di configurazione, alla sezione e al parametro.

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. Sempre nel file ApplicationManifest.xml specificare il parametro nell'elemento `Parameters`.

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. Definire `DefaultValue`.

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> Quando si aggiunge un elemento ConfigOverride, Service Fabric sceglie sempre i parametri dell'applicazione o il valore predefinito specificato nel manifesto dell'applicazione.
>
>

Quando si pubblica l'applicazione da Visual Studio usando il profilo di pubblicazione Cloud.xml, il servizio è configurato per l'uso della porta 80. Se si distribuisce l'applicazione senza specificare il parametro MyWebAPI_PortNumber, il servizio usa la porta 8080.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su alcuni dei concetti principali trattati in questo articolo, vedere [Gestire le applicazioni per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

Per informazioni su altre funzionalità di gestione delle app disponibili in Visual Studio, vedere [Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni di Service Fabric](service-fabric-manage-application-in-visual-studio.md).