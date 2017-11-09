---
title: Procedure di aggiornamento dell'SDK per Windows Phone Silverlight
description: Procedure di aggiornamento di Azure Mobile Engagement SDK per Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f87f65788075c7f4067e77946e1bcbc8f3709317
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Procedure di aggiornamento dell'SDK per Windows Phone Silverlight
Se è già stata eseguita l'integrazione di una versione precedente dell'SDK nell'applicazione, sarà necessario tenere in considerazione gli aspetti seguenti durante l'aggiornamento dell'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Se ad esempio si esegue la migrazione dalla versione 0.10.1 alla 0.11.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 0.9.0 alla 0.10.1" e quindi la procedura per la migrazione "dalla 0.10.1 alla 0.11.0".

## <a name="from-200-to-330"></a>Dalla versione 2.0.0 alla 3.3.0
### <a name="test-logs"></a>Log di test
I log della console generati da SDK possono essere abilitati/disattivati/filtrati. Per eseguire una personalizzazione, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` scegliendo uno dei valori disponibili nell'enumerazione `EngagementTestLogLevel`, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

## <a name="from-111-to-200"></a>Dalla versione 1.1.1 alla 2.0.0
La sezione seguente illustra come eseguire la migrazione di un'integrazione dell'SDK dal servizio Capptain offerto da Capptain SAS a un'app basata su Azure Mobile Engagement. 

> [!IMPORTANT]
> Capptain e Mobile Engagement sono servizi diversi e la procedura seguente illustra solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON comporta la migrazione dei dati dai server di Capptain ai server di Mobile Engagement
> 
> 

Se si esegue la migrazione da una versione precedente, consultare il sito web Capptain per eseguire prima la migrazione a 1.1.1, quindi applicare la procedura seguente

### <a name="nuget-package"></a>Pacchetto NuGet
Sostituire **Capptain.WindowsPhone** con il pacchetto NuGet **MicrosoftAzure.MobileEngagement**.

### <a name="applying-mobile-engagement"></a>Applicazione di Mobile Engagement
L'SDK usa il termine `Engagement`. È necessario aggiornare il progetto per tenere conto di questa modifica.

È necessario disinstallare il pacchetto nuget corrente di Capptain. Si consideri che verranno rimosse tutte le modifiche nella cartella Risorse di Capptain. Se si desidera mantenere tali file, eseguirne una copia.

Successivamente, installare il nuovo pacchetto NuGet di Microsoft Azure Engagement nel progetto. È possibile trovarlo direttamente sul sito Web di [NuGet](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Questa operazione sostituisce tutti i file di risorse utilizzati da Engagement e aggiunge la nuova DLL di Engagement ai riferimenti del progetto.

È necessario eliminare i riferimenti del progetto rimuovendo i riferimenti DLL di Capptain. Se non si effettua questa operazione, la versione di Capptain creerà un conflitto e si verificheranno errori.

Se sono state personalizzate risorse Capptain, copiare il contenuto dei file precedenti e incollarlo in nuovi file di progetto. Si noti che è necessario aggiornare sia i file xaml che i file cs.

Al termine di queste operazioni, è necessario sostituire i riferimenti di Capptain precedenti con i nuovi riferimenti di Engagement.

1. Tutti gli spazi dei nomi Capptain devono essere aggiornati.
   
    Prima della migrazione:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Dopo la migrazione:
   
        using Microsoft.Azure.Engagement;
2. Tutte le classi Capptain che contengono "Capptain" devono contenere "Engagement".
   
    Prima della migrazione:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Dopo la migrazione:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Per i file xaml cambiano anche attributi e spazio dei nomi di Capptain.
   
    Prima della migrazione:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Dopo la migrazione:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Per altre risorse come le immagini di Capptain, tenere presente che sono state rinominate per l'utilizzo di "Engagement".

### <a name="application-id--sdk-key"></a>ID applicazione / chiave SDK
Engagement utilizza una stringa di connessione. Non è necessario specificare un ID applicazione e una chiave SDK con Mobile Engagement, è sufficiente specificare una stringa di connessione. È possibile configurarla nel file EngagementConfiguration.

La configurazione di Engagement può essere impostata nel file `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare:

* La stringa di connessione dell'applicazione tra i tag `<connectionString>` and `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale di Azure classico.

### <a name="items-name-change"></a>Modifica del nome di elementi
Tutti gli elementi denominati *capptain* sono stati rinominati in *engagement*. Lo stesso vale per *Capptain*, che è stato ridenominato in *Engagement*.

Esempi di elementi di Capptain di uso comune:

* CapptainConfiguration è diventato EngagementConfiguration
* CapptainAgent è diventato EngagementAgent
* CapptainReach è diventato EngagementReach
* CapptainHttpConfig è diventato EngagementHttpConfig
* GetCapptainPageName è diventato GetEngagementPageName

Si noti la ridenominazione influisce anche sui metodi sottoposti a override.

