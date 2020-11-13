---
title: Abilitare l'implementazione temporanea delle funzionalità per destinatari specifici
titleSuffix: Azure App Configuration
description: Informazioni su come abilitare la distribuzione temporanea delle funzionalità per destinatari specifici
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: d1574b8a3f8cda3341c0aaf355911e2e93a7bcab
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557932"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>Abilitare l'implementazione temporanea delle funzionalità per destinatari specifici

I flag funzionalità consentono di attivare o disattivare dinamicamente le funzionalità nell'applicazione. I filtri delle funzionalità determinano lo stato di un flag di funzionalità ogni volta che viene valutato. La `Microsoft.FeatureManagement` libreria include `TargetingFilter` , che Abilita un flag funzionalità per un elenco specificato di utenti e gruppi o per una percentuale specificata di utenti. `TargetingFilter` è "Sticky". Ciò significa che, una volta che un singolo utente riceve una funzionalità, continuerà a vedere tale funzionalità in tutte le richieste future. È possibile usare `TargetingFilter` per abilitare una funzionalità per un account specifico durante una demo, per implementare progressivamente nuove funzionalità per gli utenti in gruppi diversi o "anelli" e molto altro ancora.

In questo articolo si apprenderà come implementare una nuova funzionalità di un'applicazione Web di ASP.NET Core per utenti e gruppi specificati, usando `TargetingFilter` con app Azure configurazione.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Creare un'applicazione Web con flag funzionalità e autenticazione

Per implementare le funzionalità basate su utenti e gruppi, è necessaria un'applicazione Web che consenta agli utenti di eseguire l'accesso.

1. Creare un'applicazione Web che esegue l'autenticazione in un database locale usando il comando seguente:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Compilare ed eseguire, quindi selezionare il collegamento **registra** nell'angolo superiore destro per creare un nuovo account utente. Usare un indirizzo di posta elettronica di `test@contoso.com` . Nella schermata **conferma registrazione** selezionare **fare clic qui per confermare l'account**.

1. Seguire le istruzioni nella [Guida introduttiva: aggiungere flag funzionalità a un'app ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) per aggiungere un flag funzionalità alla nuova applicazione Web.

1. Consente di abilitare o disabilitare il flag funzionalità nella configurazione dell'app. Verificare che questa azione controlli la visibilità dell'elemento **beta** sulla barra di spostamento.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>Aggiornare il codice dell'applicazione Web per l'uso di TargetingFilter

A questo punto, è possibile usare il flag funzionalità per abilitare o disabilitare la `Beta` funzionalità per tutti gli utenti. Per abilitare il flag funzionalità per alcuni utenti e disabilitarlo per altri utenti, aggiornare il codice per l'uso di `TargetingFilter` . In questo esempio si userà l'indirizzo di posta elettronica dell'utente connesso come ID utente e la parte relativa al nome di dominio dell'indirizzo di posta elettronica come gruppo. L'utente e il gruppo verranno aggiunti al `TargetingContext` . `TargetingFilter`Usa questo contesto per determinare lo stato del flag di funzionalità per ogni richiesta.

1. Eseguire l'aggiornamento alla versione più recente del `Microsoft.FeatureManagement.AspNetCore` pacchetto.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. Aggiungere un file *TestTargetingContextAccessor.cs* :

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. In *Startup.cs* aggiungere un riferimento allo spazio dei nomi *Microsoft. FeatureManagement. FeatureFilters* :

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Aggiornare il metodo *ConfigureServices* per `TargetingFilter` la registrazione, dopo la chiamata a `AddFeatureManagement()` :

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Aggiornare il metodo *ConfigureServices* per aggiungere l'oggetto `TestTargetingContextAccessor` creato nel passaggio precedente alla raccolta di servizi. *TargetingFilter* lo usa per determinare il contesto di destinazione ogni volta che viene valutato il flag funzionalità.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

L'intero metodo *ConfigureServices* sarà simile al seguente:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>Aggiornare il flag funzionalità per usare TargetingFilter

1. Nella portale di Azure passare all'archivio di configurazione dell'app e selezionare **Gestione funzionalità**.

1. Selezionare il menu di scelta rapida per il flag della funzionalità *beta* creato nella Guida introduttiva. Selezionare **Modifica**.

    > [!div class="mx-imgBorder"]
    > ![Modifica flag funzionalità beta](./media/edit-beta-feature-flag.png)

1. Nella schermata di **modifica** selezionare la casella di controllo **Abilita flag funzionalità** se non è già selezionata. Selezionare quindi la casella di controllo **Usa filtro funzionalità** .

1. Selezionare il pulsante di opzione **destinazione** .

1. Selezionare le opzioni seguenti:

    - **Percentuale predefinita** : 0
    - **Gruppi** : immettere il **nome** _contoso.com_ e una **percentuale** di _50_
    - **Utenti** : `test@contoso.com`

    La schermata filtro funzionalità sarà simile alla seguente:

    > [!div class="mx-imgBorder"]
    > ![Flag funzionalità condizionale](./media/feature-flag-filter-enabled.png)

    Queste impostazioni generano il comportamento seguente:

    - Il flag funzionalità è sempre abilitato per l'utente `test@contoso.com` , perché `test@contoso.com` è elencato nella sezione _utenti_ .
    - Il flag funzionalità è abilitato per il 50% di altri utenti nel gruppo _contoso.com_ , perché _contoso.com_ è elencato nella sezione _Groups_ con una _percentuale_ di _50_.
    - La funzionalità è sempre disabilitata per tutti gli altri utenti, perché la _percentuale predefinita_ è impostata su _0_.

1. Selezionare **applica** per salvare le impostazioni e tornare alla schermata di **gestione delle funzionalità** .

1. Il **filtro funzionalità** per il flag funzionalità ora viene visualizzato come *destinazione*. Questo stato indica che il flag funzionalità verrà abilitato o disabilitato in base alle singole richieste, in base ai criteri applicati dal filtro della funzionalità di *destinazione* .

## <a name="targetingfilter-in-action"></a>TargetingFilter in azione

Per visualizzare gli effetti di questo flag di funzionalità, compilare ed eseguire l'applicazione. Inizialmente, l'elemento *beta* non viene visualizzato sulla barra degli strumenti, perché l'opzione _percentuale predefinita_ è impostata su 0.

A questo punto, accedere come `test@contoso.com` , usando la password impostata durante la registrazione. L'elemento *beta* viene ora visualizzato sulla barra degli strumenti, perché `test@contoso.com` viene specificato come utente di destinazione.

Il video seguente mostra questo comportamento in azione.

> [!div class="mx-imgBorder"]
> ![TargetingFilter in azione](./media/feature-flags-targetingfilter.gif)

È possibile creare altri utenti con `@contoso.com` indirizzi di posta elettronica per visualizzare il comportamento delle impostazioni di gruppo. 50% di questi utenti visualizzeranno l'elemento *beta* . L'altro 50% non vedrà l'elemento *beta* .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Panoramica della gestione delle funzionalità](./concept-feature-management.md)