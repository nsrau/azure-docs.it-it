---
title: Ripristino di emergenza e resilienza in Configurazione app di Azure | Microsoft Docs
description: Panoramica su come implementare resilienza e ripristino di emergenza con Configurazione app di Azure.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: 39e7a62899a7d1d6feb5bfd3b45ad4adc3c996a0
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394070"
---
# <a name="resiliency-and-disaster-recovery"></a>Resilienza e ripristino di emergenza

Attualmente Configurazione app di Azure è un servizio a livello di area. Ogni archivio di configurazione viene creato in una specifica area di Azure. Un'interruzione a livello di area interesserà tutti gli archivi in tale area. Configurazione app non offre il failover automatico in un'altra area. Questo articolo offre indicazioni generali su come è possibile usare più archivi di configurazione nelle aree di Azure per aumentare la resilienza geografica dell'applicazione.

## <a name="high-availability-architecture"></a>Architettura a disponibilità elevata

Per realizzare la ridondanza tra aree, è necessario creare più archivi di configurazione app in aree diverse. Con questa configurazione, l'applicazione avrà almeno un archivio di configurazione aggiuntivo in cui eseguire il fallback quando l'archivio primario diventa inaccessibile. Di seguito è riportato un diagramma che illustra la topologia tra l'applicazione e i relativi archivi di configurazione primario e secondario.

![Archivi con ridondanza geografica](./media/geo-redundant-app-configuration-stores.png)

L'applicazione caricherà la configurazione da entrambi gli archivi, primario e secondario, in parallelo. Questo aumenta in modo significativo la probabilità di ottenere correttamente i dati di configurazione. Mantenere sincronizzati i dati in entrambi gli archivi è responsabilità dell'utente. Le sezioni seguenti illustrano come implementare la resilienza geografica nell'applicazione.

## <a name="failover-between-configuration-stores"></a>Failover tra archivi di configurazione

Tecnicamente l'applicazione non sta eseguendo un failover. Sta tentando di recuperare contemporaneamente lo stesso set di dati di configurazione da due archivi di configurazione app. È consigliabile organizzare il codice in modo che carichi prima dall'archivio secondario e poi dall'archivio primario. Questo approccio garantisce che, quando disponibili, i dati di configurazione nell'archivio primario abbiano sempre la precedenza. Il frammento di codice seguente illustra l'implementazione in .NET Core.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    }
```

Si noti il parametro `optional` passato alla funzione `AddAzureAppConfiguration`. Se impostato su `true`, questo parametro farà in modo che l'applicazione continui se la funzione non riesce a caricare i dati di configurazione.

## <a name="synchronization-between-configuration-stores"></a>Sincronizzazione tra gli archivi di configurazione

È importante che gli archivi di configurazione con ridondanza geografica abbiano tutti lo stesso set di dati. È possibile usare la funzione **Esporta** in Configurazione app per copiare dati dall'archivio primario al secondario on demand. La funzione è disponibile sia tramite il portale che tramite l'interfaccia della riga di comando di Azure.

Dal portale di Azure, è possibile eseguire il push di una modifica in un altro archivio di configurazione seguendo questa procedura:

1. Passare alla scheda **Importazione/Esportazione**, selezionare **Esporta**, selezionare **Configurazione app** come servizio di **Destinazione** e fare clic su **Selezionare una risorsa**.

2. Nel nuovo pannello che si apre specificare la sottoscrizione, il gruppo di risorse e il nome risorsa dell'archivio secondario e quindi fare clic su **Applica**.

3. L'interfaccia utente verrà aggiornata in modo che sia possibile scegliere quali dati di configurazione esportare nell'archivio secondario. È possibile lasciare il valore di data e ora predefinito così com'è e impostare sia **Da etichetta** che **A etichetta** sullo stesso valore. Fare clic su **Apply**.

4. Ripetere i passaggi precedenti per tutte le modifiche di configurazione.

È possibile automatizzare questo processo di esportazione tramite l'interfaccia della riga di comando di Azure. Il comando riportato di seguito mostra come esportare una singola modifica di configurazione dall'archivio primario al secondario.

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come migliorare l'applicazione per ottenere la resilienza geografica in fase di esecuzione per Configurazione app. In alternativa, è possibile incorporare i dati di configurazione da Configurazione app in fase di compilazione o di distribuzione. Per altre informazioni, vedere [Integrare una pipeline CI/CD](./integrate-ci-cd-pipeline.md).

