---
title: Usa configurazione per ambiente
titleSuffix: Azure App Configuration
description: Usare etichette per fornire valori di configurazione per ambiente
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056808"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Usare le etichette per abilitare configurazioni diverse per ambienti diversi

Molte applicazioni devono usare configurazioni diverse per ambienti diversi. Si supponga che un'applicazione disponga di un valore di configurazione che definisce la stringa di connessione da utilizzare per il database back-end. Gli sviluppatori dell'applicazione usano un database diverso da quello usato nell'ambiente di produzione. La stringa di connessione del database usata dall'applicazione deve cambiare quando l'applicazione passa dallo sviluppo alla produzione.

In app Azure configurazione è possibile usare le *etichette* per definire valori diversi per la stessa chiave. Ad esempio, è possibile definire una singola chiave con valori diversi per *lo sviluppo* e la *produzione*. È possibile specificare le etichette da caricare durante la connessione alla configurazione dell'app.

Per illustrare questa funzionalità, si modificherà l'app Web creata nella [Guida introduttiva: creare un'app ASP.NET Core con app Azure configurazione](./quickstart-aspnet-core-app.md) per usare impostazioni di configurazione diverse per lo sviluppo e la produzione. Prima di continuare, completare la Guida introduttiva.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Specificare un'etichetta quando si aggiunge un valore di configurazione

Nel portale di Azure passare a **Configuration Explorer** e individuare la chiave *TestApp: Settings: fontcolor* creata nella Guida introduttiva. Selezionare il menu di scelta rapida e quindi fare clic su **Aggiungi valore**.

> [!div class="mx-imgBorder"]
> ![Voce di menu Aggiungi valore](media/labels-add-value.png)

Nella schermata **Aggiungi valore** immettere un **valore** di **rosso** e un' **etichetta** dello **sviluppo**. Lasciare vuoto il **tipo di contenuto** . Selezionare **Applica**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Caricamento dei valori di configurazione con un'etichetta specificata

Per impostazione predefinita, app Azure configurazione carica solo i valori di configurazione senza etichetta. Se sono state definite etichette per i valori di configurazione, è necessario specificare le etichette da usare per la connessione alla configurazione dell'app.

Nella sezione precedente è stato creato un valore di configurazione diverso per l'ambiente di *sviluppo* . Usare la `HostingEnvironment.EnvironmentName` variabile per determinare in modo dinamico l'ambiente in cui è attualmente in esecuzione l'app. Per altre informazioni, vedere [usare più ambienti in ASP.NET Core](/aspnet/core/fundamentals/environments).

Caricare i valori di configurazione con l'etichetta corrispondente all'ambiente corrente passando il nome dell'ambiente nel `Select` metodo:

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
> Il frammento di codice precedente carica la stringa di connessione di configurazione dell'app `AppConfigConnectionString`da una variabile di ambiente denominata. Assicurarsi che la variabile di ambiente sia impostata correttamente.

Il metodo `Select` viene chiamato due volte. La prima volta, carica i valori di configurazione senza etichetta. Quindi carica i valori di configurazione con l'etichetta corrispondente all'ambiente corrente. Questi valori specifici dell'ambiente eseguono l'override di qualsiasi valore corrispondente senza etichetta. Non è necessario definire valori specifici dell'ambiente per ogni chiave. Se una chiave non ha un valore con un'etichetta corrispondente all'ambiente corrente, viene usato il valore senza etichetta.

## <a name="testing-in-different-environments"></a>Test in ambienti diversi

Per testare i diversi valori di configurazione, aprire `launchSettings.json` il file nella `Properties` directory. Individuare la `config` voce sotto `profiles`. Nella `environmentVariables` sezione impostare la `ASPNETCORE_ENVIRONMENT` variabile su. `Production`

Con i nuovi valori impostati, compilare ed eseguire l'applicazione.

```dotnetcli
dotnet build
dotnet run
```

Usare un Web browser per passare a `http://localhost:5000`. Si noterà che il colore del carattere è nero.

![Applicazione Web in esecuzione con la configurazione di produzione](media/labels-website-prod.png)

A questo `launchSettings.json` punto, aggiornare `ASPNETCORE_ENVIRONMENT` per impostare `Development`la variabile su. Eseguire di nuovo `dotnet run`. Si noterà che il colore del carattere è ora rosso. Questo perché l'applicazione usa ora il valore di `TestApp:Settings:FontColor` con l' `Development` etichetta. Tutti gli altri valori di configurazione rimangono uguali ai valori di produzione.

![Applicazione Web in esecuzione con configurazione di sviluppo](media/labels-website-dev.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione in ASP.NET Core](/aspnet/core/fundamentals/configuration/)
