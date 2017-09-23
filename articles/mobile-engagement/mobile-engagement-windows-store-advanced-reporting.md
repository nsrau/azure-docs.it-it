---
title: Segnalazione avanzata di Windows universale con MobileApps Engagement
description: Come integrare Azure Mobile Engagement con le app universali di Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Segnalazione avanzata con Engagement SDK per le app di Windows universali
> [!div class="op_single_selector"]
> * [Windows universale](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Questo argomento descrive scenari di segnalazione aggiuntivi nell'applicazione di Windows universale. Questi scenari includono le opzioni che è possibile scegliere di applicare all'app creata nell'esercitazione [introduttiva](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Prerequisiti
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Prima di iniziare questa esercitazione, è necessario completare l'esercitazione [introduttiva](mobile-engagement-windows-store-dotnet-get-started.md) che è intenzionalmente diretta e semplice . Questa esercitazione illustra le opzioni aggiuntive disponibili.

## <a name="specifying-engagement-configuration-at-runtime"></a>Specifica della configurazione di Engagement in fase di esecuzione
La configurazione di Engagement è centralizzata nel file `Resources\EngagementConfiguration.xml` del progetto, ovvero quello in cui è stata specificata nell'argomento [introduttivo](mobile-engagement-windows-store-dotnet-get-started.md) .

Se si vuole specificarlo in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Metodo consigliato: eseguire l'overload delle classi `Page`
Per attivare la segnalazione di tutti i log richiesti da Engagement per calcolare utenti, sessioni, attività, arresti anomali e statistiche tecniche, fare in modo che tutte le sottoclassi `Page` ereditino dalle classi `EngagementPage`.

Di seguito è riportato un esempio per una pagina dell'applicazione. È possibile procedere allo stesso modo per tutte le pagine dell'applicazione.

### <a name="c-source-file"></a>File di origine C#
Modificare il file `.xaml.cs` della pagina:

* Aggiungere quanto segue alle istruzioni `using`:
  
      using Microsoft.Azure.Engagement;
* Sostituire `Page` con `EngagementPage`:

**Senza Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Con Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> Se la pagina esegue l'override del metodo `OnNavigatedTo`, accertarsi di chiamare `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verrà segnalata, ovvero `EngagementPage` chiama `StartActivity` all'interno del relativo metodo `OnNavigatedTo`.
> 
> 

### <a name="xaml-file"></a>File XAML
Modificare il file `.xaml` della pagina:

* Aggiungere le dichiarazioni di spazi dei nomi:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Sostituire `Page` con `engagement:EngagementPage`:

**Senza Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Con Engagement:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>Eseguire l'override del comportamento predefinito
Per impostazione predefinita, il nome della classe della pagina viene indicato come nome dell'attività, senza elementi aggiuntivi. Se la classe usa il suffisso "Page", Engagement rimuoverà questo elemento.

Per sostituire il comportamento predefinito per il nome, aggiungere il codice seguente:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Per segnalare informazioni aggiuntive con l'attività, aggiungere il codice seguente:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Questi metodi vengono chiamati dall'interno del metodo `OnNavigatedTo` della pagina.

### <a name="alternate-method-call-startactivity-manually"></a>Metodo alternativo: chiamare `StartActivity()` manualmente
Se non si può o non si vuole eseguire l'overload delle classi `Page`, in alternativa è possibile avviare le attività chiamando direttamente i metodi `EngagementAgent`.

È consigliabile chiamare `StartActivity` all'interno del metodo `OnNavigatedTo` della pagina.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Assicurarsi che la sessione venga terminata correttamente.
> 
> Windows Universal SDK chiama automaticamente il metodo `EndActivity` quando l'applicazione viene chiusa. È quindi **ALTAMENTE** consigliabile chiamare il metodo `StartActivity` ogni volta che l'attività dell'utente cambia e non chiamare **MAI** il metodo `EndActivity`. Questo metodo notifica al server di Engagement che l'utente ha chiuso l'applicazione e ciò influirà su tutti i log delle applicazioni.
> 
> 

## <a name="advanced-reporting"></a>Segnalazione avanzata
Facoltativamente, è possibile segnalare eventi specifici dell'applicazione, errori e processi. A tale scopo, usare gli altri metodi disponibili nella classe `EngagementAgent`. L'API di Engagement consente di usare tutte le funzionalità avanzate di Engagement.

Per altre informazioni, vedere [Come usare l'API di Engagement in un'app di Windows universale](mobile-engagement-windows-store-use-engagement-api.md).


