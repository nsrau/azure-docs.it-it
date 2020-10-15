---
title: Usare la configurazione per ambiente
titleSuffix: Azure App Configuration
description: Usare le etichette per fornire valori di configurazione per ambiente.
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: 689fdbd444aa4b20f5bef225faa259788c47cf9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206657"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Usare le etichette per abilitare le configurazioni per ambienti diversi

Molte applicazioni richiedono l'uso di configurazioni diverse per ambienti diversi. Si supponga che un'applicazione abbia un valore di configurazione che definisce la stringa di connessione da usare per il database back-end. Gli sviluppatori dell'applicazione usano un database diverso da quello usato nell'ambiente di produzione. La stringa di connessione del database usata dall'applicazione deve cambiare quando l'applicazione passa dall'ambiente di sviluppo a quello di produzione.

In Configurazione app di Azure è possibile usare *etichette* per definire valori diversi per la stessa chiave. È ad esempio possibile definire una singola chiave con valori diversi per l'ambiente di sviluppo e quello di produzione. È possibile specificare l'etichetta da caricare quando ci si connette a Configurazione app.

Per illustrare questa funzionalità, si modificherà l'app Web creata in [Guida di avvio rapido: Creare un'app ASP.NET Core con Configurazione app di Azure](./quickstart-aspnet-core-app.md) per usare impostazioni di configurazione diverse per l'ambiente di sviluppo e quello di produzione. Prima di procedere, completare la guida di avvio rapido.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Specificare un'etichetta quando si aggiunge un valore di configurazione

Nel portale di Azure passare a **Esplora configurazioni** e trovare la chiave *TestApp:Settings:FontColor* creata nella guida di avvio rapido. Selezionare il relativo menu di scelta rapida e quindi scegliere **Aggiungi valore**.

> [!div class="mx-imgBorder"]
> ![Voce di menu Aggiungi valore](media/labels-add-value.png)

Nella schermata **Aggiungi valore** immettere **red** per **Valore** e **Development** per **Etichetta**. Lasciare vuoto il campo **Tipo di contenuto**. Selezionare **Applica**.

## <a name="load-configuration-values-with-a-specified-label"></a>Caricare i valori di configurazione con un'etichetta specificata

Per impostazione predefinita, Configurazione app di Azure carica solo i valori di configurazione senza etichetta. Se sono state definite etichette per i valori di configurazione, è possibile specificare le etichette da usare quando si esegue la connessione a Configurazione app.

Nella sezione precedente è stato creato un valore di configurazione diverso per l'ambiente di sviluppo. Usare la variabile `HostingEnvironment.EnvironmentName` per determinare in modo dinamico l'ambiente in cui è attualmente in esecuzione l'app. Per altre informazioni, vedere [Usare più ambienti in ASP.NET Core](/aspnet/core/fundamentals/environments).

Per caricare i valori di configurazione con l'etichetta corrispondente all'ambiente corrente, passare il nome dell'ambiente nel metodo `Select`:

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
> Il frammento di codice precedente carica la stringa di connessione di Configurazione app da una variabile di ambiente denominata `AppConfigConnectionString`. Assicurarsi che la variabile di ambiente sia impostata correttamente.

Il metodo `Select` viene chiamato due volte. La prima volta, carica i valori di configurazione senza etichetta. Successivamente, carica i valori di configurazione con l'etichetta corrispondente all'ambiente corrente. Questi valori specifici dell'ambiente eseguono l'override dei valori corrispondenti senza etichetta. Non è necessario definire valori specifici dell'ambiente per ogni chiave. Se una chiave non ha un valore con un'etichetta corrispondente all'ambiente corrente, usa il valore senza etichetta.

## <a name="test-in-different-environments"></a>Eseguire il test in ambienti diversi

Aprire il file `launchSettings.json` nella directory `Properties`. Individuare la voce `config` in `profiles`. Nella sezione `environmentVariables` impostare la variabile `ASPNETCORE_ENVIRONMENT` su `Production`.

Con i nuovi valori impostati, compilare ed eseguire l'applicazione.

```dotnetcli
dotnet build
dotnet run
```

Usare un Web browser per passare a `http://localhost:5000`. Si noterà che il colore del carattere è nero.

![Applicazione Web in esecuzione con la configurazione di produzione](media/labels-website-prod.png)

Aggiornare `launchSettings.json` per impostare la variabile `ASPNETCORE_ENVIRONMENT` su `Development`. Eseguire di nuovo `dotnet run`. 

Si noterà che il colore del carattere è ora rosso. Ciò avviene perché l'applicazione ora usa il valore di `TestApp:Settings:FontColor` con l'etichetta `Development`. Tutti gli altri valori di configurazione rimangono uguali ai valori di produzione.

![Applicazione Web in esecuzione con la configurazione di sviluppo](media/labels-website-dev.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione in ASP.NET Core](/aspnet/core/fundamentals/configuration/)
