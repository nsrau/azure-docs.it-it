---
title: Resilienza della configurazione di app Azure e ripristino di emergenza
description: Come implementare la resilienza e il ripristino di emergenza con la configurazione app Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 5c62f10d67345d68cde27af7d0a7663b22d978a0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207202"
---
# <a name="resiliency-and-disaster-recovery"></a>Resilienza e ripristino di emergenza

Attualmente Configurazione app di Azure è un servizio a livello di area. Ogni archivio di configurazione viene creato in una specifica area di Azure. Un'interruzione a livello di area interessa tutti gli archivi in tale area. Configurazione app non offre il failover automatico in un'altra area. Questo articolo offre indicazioni generali su come è possibile usare più archivi di configurazione nelle aree di Azure per aumentare la resilienza geografica dell'applicazione.

## <a name="high-availability-architecture"></a>Architettura a disponibilità elevata

Per realizzare la ridondanza tra aree, è necessario creare più archivi di Configurazione app in aree diverse. Con questa configurazione l'applicazione include almeno un archivio di configurazione aggiuntivo in cui eseguire il fallback quando l'archivio primario diventa inaccessibile. Il diagramma seguente illustra la topologia tra l'applicazione e i relativi archivi di configurazione primario e secondario:

![Archivi con ridondanza geografica](./media/geo-redundant-app-configuration-stores.png)

L'applicazione carica la configurazione da entrambi gli archivi, primario e secondario, in parallelo. Questo comportamento aumenta la probabilità di ottenere correttamente i dati di configurazione. L'utente è responsabile del mantenimento della sincronizzazione dei dati in entrambi i punti vendita. Le sezioni seguenti illustrano come è possibile creare la resilienza geografica nell'applicazione.

## <a name="failover-between-configuration-stores"></a>Failover tra archivi di configurazione

Dal punto di vista tecnico l'applicazione non sta eseguendo un failover. Sta provando a recuperare contemporaneamente lo stesso set di dati di configurazione da due archivi di Configurazione app. Organizzare il codice in modo che carichi prima dall'archivio secondario e poi dall'archivio primario. Questo approccio garantisce che, quando disponibili, i dati di configurazione nell'archivio primario abbiano sempre la precedenza. Il frammento di codice seguente mostra come è possibile implementare questa disposizione in .NET Core:

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

Si noti il parametro `optional` passato alla funzione `AddAzureAppConfiguration`. Se impostato su `true`, questo parametro fa in modo che l'esecuzione dell'applicazione prosegua anche se la funzione non riesce a caricare i dati di configurazione.

## <a name="synchronization-between-configuration-stores"></a>Sincronizzazione tra gli archivi di configurazione

È importante che gli archivi di configurazione con ridondanza geografica abbiano tutti lo stesso set di dati. A questo scopo è possibile procedere in due modi:

### <a name="backup-manually-using-the-export-function"></a>Eseguire manualmente il backup tramite la funzione Export

È possibile usare la funzione **Esporta** in Configurazione app per copiare dati dall'archivio primario al secondario su richiesta. La funzione è disponibile sia tramite il portale di Azure che tramite l'interfaccia della riga di comando.

Dal portale di Azure è possibile eseguire il push di una modifica in un altro archivio di configurazione seguendo questa procedura.

1. Passare alla scheda **Importazione/Esportazione** e selezionare **Esporta** > **Configurazione app** > **Destinazione** > **Selezionare una risorsa**.

1. Nel pannello nuovo visualizzato specificare la sottoscrizione, il gruppo di risorse e il nome della risorsa dell'archivio secondario, quindi selezionare **applica**.

1. L'interfaccia utente verrà aggiornata in modo che sia possibile scegliere i dati di configurazione da esportare nell'archivio secondario. È possibile lasciare il valore di ora predefinito così come è e impostare sia **da Label** che da **Label** sullo stesso valore. Selezionare **Applica**. Ripetere questa operazione per tutte le etichette nell'archivio primario.

1. Ripetere i passaggi precedenti ogni volta che viene modificata la configurazione.

È possibile ottenere il processo di esportazione anche usando l'interfaccia della riga di comando di Azure. Il comando seguente mostra come esportare tutte le configurazioni dall'archivio primario a quello secondario:

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>Backup automatico con funzioni di Azure

Il processo di backup può essere automatizzato con funzioni di Azure. Sfrutta l'integrazione con griglia di eventi di Azure nella configurazione dell'app. Una volta configurata, la configurazione dell'app pubblicherà gli eventi in griglia di eventi per tutte le modifiche apportate ai valori di chiave in un archivio di configurazione. Un'app funzioni di Azure può quindi restare in ascolto di questi eventi e di eseguire il backup dei dati di conseguenza. Per informazioni dettagliate, vedere l'esercitazione su [come eseguire automaticamente il backup degli archivi di configurazione dell'app](./howto-backup-config-store.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come migliorare l'applicazione per ottenere la resilienza geografica in fase di esecuzione per Configurazione app. È anche possibile incorporare i dati di configurazione da Configurazione app in fase di compilazione o di distribuzione. Per altre informazioni, vedere [Integrare una pipeline CI/CD](./integrate-ci-cd-pipeline.md).
