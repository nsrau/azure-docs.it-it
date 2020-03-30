---
title: Utilizzare la configurazione per ambienteUse per-environment configuration
titleSuffix: Azure App Configuration
description: Usare le etichette per fornire valori di configurazione per ambienteUse labels to provide per-environment configuration values
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056808"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Usare le etichette per abilitare configurazioni diverse per ambienti diversiUse labels to enable different configurations for different environments

Molte applicazioni devono utilizzare configurazioni diverse per ambienti diversi. Si supponga che un'applicazione disponga di un valore di configurazione che definisce la stringa di connessione da utilizzare per il relativo database back-end. Gli sviluppatori dell'applicazione utilizzano un database diverso da quello utilizzato nell'ambiente di produzione. La stringa di connessione al database utilizzata dall'applicazione deve essere modificata quando l'applicazione passa dallo sviluppo alla produzione.

In Configurazione app di Azure è possibile usare *le etichette* per definire valori diversi per la stessa chiave. Ad esempio, è possibile definire una singola chiave con valori diversi per *Sviluppo* e *Produzione*. È possibile specificare le etichette da caricare quando ci si connette a Configurazione app.

Per dimostrare questa funzionalità, modificheremo l'app Web creata in [Guida introduttiva: Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md) di Azure per usare impostazioni di configurazione diverse per lo sviluppo e l'ambiente di produzione. Completare l'avvio rapido prima di procedere.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Specificare un'etichetta quando si aggiunge un valore di configurazioneSpecify a label when adding a configuration value

Nel portale di Azure passare a **Esplora configurazione** e individuare la chiave *TestApp:Settings:FontColor* creata nella guida introduttiva. Selezionare il relativo menu di scelta rapida e quindi fare clic su **Aggiungi valore**.

> [!div class="mx-imgBorder"]
> ![Aggiungi voce di menu Valore](media/labels-add-value.png)

Nella schermata **Aggiungi valore** immettere un **valore** **di rosso** e **un'etichetta** di **sviluppo**. Lasciare vuoto **Tipo di contenuto.** Selezionare **Applica**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Caricamento dei valori di configurazione con un'etichetta specificata

Per impostazione predefinita, La configurazione delle app di Azure carica solo i valori di configurazione senza etichetta. Se sono stati definiti etichette per i valori di configurazione, è consigliabile specificare le etichette da utilizzare per la connessione a Configurazione app.

Nell'ultima sezione è stato creato un valore di configurazione diverso per l'ambiente *di sviluppo.* La variabile `HostingEnvironment.EnvironmentName` viene usata in modo dinamico per determinare in modo dinamico in quale ambiente l'app è attualmente in esecuzione. Per altre informazioni, vedere [Usare più ambienti in ASP.NET Core](/aspnet/core/fundamentals/environments).

Caricare i valori di configurazione con l'etichetta corrispondente `Select` all'ambiente corrente passando il nome dell'ambiente nel metodo:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Il frammento di codice precedente carica la `AppConfigConnectionString`stringa di connessione di configurazione dell'app da una variabile di ambiente denominata . Assicurarsi che questa variabile di ambiente sia impostata correttamente.

Il metodo `Select` viene chiamato due volte. La prima volta, carica i valori di configurazione senza etichetta. Quindi, carica i valori di configurazione con l'etichetta corrispondente all'ambiente corrente. Questi valori specifici dell'ambiente sostituiscono i valori corrispondenti senza etichetta. Non è necessario definire valori specifici dell'ambiente per ogni chiave. Se una chiave non ha un valore con un'etichetta corrispondente all'ambiente corrente, viene utilizzato il valore senza etichetta.

## <a name="testing-in-different-environments"></a>Test in ambienti diversi

Per verificare i diversi `launchSettings.json` valori di `Properties` configurazione, aprire il file nella directory. Individuare `config` la `profiles`voce in . Nella `environmentVariables` sezione impostare `ASPNETCORE_ENVIRONMENT` la `Production`variabile su .

Con i nuovi valori impostati, compilare ed eseguire l'applicazione.

```dotnetcli
dotnet build
dotnet run
```

Utilizzare un browser Web `http://localhost:5000`per passare a . Si noterà che il colore del carattere è nero.

![Applicazione Web in esecuzione con la configurazione di produzione](media/labels-website-prod.png)

Ora `launchSettings.json` aggiornare `ASPNETCORE_ENVIRONMENT` per `Development`impostare la variabile su . Eseguire di nuovo `dotnet run`. Si noterà che il colore del carattere è ora rosso. Questo perché l'applicazione ora `TestApp:Settings:FontColor` utilizza il `Development` valore di che ha l'etichetta. Tutti gli altri valori di configurazione rimangono gli stessi dei relativi valori di produzione.

![Applicazione Web in esecuzione con configurazione di sviluppo](media/labels-website-dev.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione in ASP.NET Core](/aspnet/core/fundamentals/configuration/)
