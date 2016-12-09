---
title: 'App per le API del servizio app: Modifiche apportate | Documentazione Microsoft'
description: "Informazioni sulle novità relative alle app per le API nel servizio app di Azure."
services: app-service\api
documentationcenter: .net
author: mohitsriv
manager: wpickett
editor: tdykstra
ms.assetid: a9b58066-e8fd-48b8-a651-4613b1736433
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d60ca38364d7ea7991af18e39407f42a8c9abb84


---
# <a name="app-service-api-apps---whats-changed"></a>App per le API del servizio app: Modifiche apportate
All'evento Connect() di novembre 2015 è stata [annunciata](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/)una serie di miglioramenti al servizio app di Azure. Tra i miglioramenti introdotti ci sono modifiche sottostanti alle app per le API, per ottenere un allineamento ottimale con le app Web e per dispositivi mobili, ridurre il numero di concetti e migliorare le prestazioni a livello di runtime e di distribuzione. A partire dal 30 novembre 2015, le nuove app per le API create usando il portale di gestione di Azure o gli strumenti più recenti includono queste modifiche. Questo articolo illustra le modifiche apportate e come ridistribuire le app esistenti per sfruttare le nuove funzionalità.

## <a name="feature-changes"></a>Modifiche apportate alle funzionalità
Le funzionalità principali delle app per le API, vale a dire autenticazione, CORS e metadati API, sono state spostate direttamente nel servizio app. Con questa modifica, le funzionalità sono disponibili in tutte le app Web, le app per dispositivi mobili e le app per le API, che di fatto condividono lo stesso tipo di risorse **Microsoft.Web/sites** in Resource Manager. Il gateway di app per le API non è più necessario e non viene più fornito con le app per le API. Questo miglioramento semplifica anche l'uso di Gestione API di Azure, perché sarà presente il solo gateway di Gestione API.

![Panoramica delle app per le API](./media/app-service-api-whats-changed/api-apps-overview.png)

Uno dei principi chiave adottati nella progettazione dell'aggiornamento delle app per le API è la possibilità di usare le API così come sono, nel linguaggio preferito.  Se l'API è già distribuita come app Web o come app per dispositivi mobili, non è necessario ridistribuirla per sfruttare i vantaggi delle nuove funzionalità. Se le app per le API vengono usate in anteprima, seguire le istruzioni per la migrazione riportate di seguito.

### <a name="authentication"></a>Autenticazione
Le funzionalità di autenticazione di app per le API, servizi mobili o app per dispositivi mobili e app Web pronte all'uso esistenti sono state unificate e sono disponibili in un unico pannello di autenticazione del servizio app di Azure nel portale di gestione. Per un'introduzione ai servizi di autenticazione nel servizio app, vedere il post di blog relativo all' [espansione dell'autenticazione/autorizzazione del servizio app](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

Per gli scenari di API, sono disponibili varie nuove funzionalità:

* **Supporto per l'uso diretto di Azure Active Directory**, senza la necessità di scambiare il token AAD con un token di sessione per il codice client. Il client può semplicemente includere i token AAD nell'intestazione dell'autorizzazione, in base alla specifica del token di connessione. Ciò significa che anche sul lato client o server non è necessario alcun SDK specifico del servizio app. 
* **Accesso da servizio a servizio o "interno"**: se un processo del daemon o un altro client deve accedere alle API senza un'interfaccia, è possibile richiedere un token usando un'entità servizio AAD e passarlo al servizio app per l'autenticazione nell'applicazione.
* **Autorizzazione posticipata**: molte applicazioni presentano restrizioni di accesso differenti per diverse parti dell'applicazione. Alcune API potrebbero essere disponibili pubblicamente, mentre altre potrebbero richiedere l'accesso. La funzionalità di autenticazione/autorizzazione originale è all-or-nothing, con l'intero sito che richiede un account di accesso. Questa opzione esiste ancora, ma in alternativa è possibile consentire al codice dell'applicazione di eseguire il rendering di decisioni di accesso dopo che il servizio app ha autenticato l'utente.

Per altre informazioni sulle nuove funzionalità di autenticazione, vedere [Autenticazione e autorizzazione per app per le API nel servizio app di Azure](app-service-api-authentication.md). Per informazioni su come eseguire la migrazione di app per le API esistenti dal modello di app per le API precedente a quello nuovo, vedere [Migrazione delle app per le API esistenti](#migrating-existing-api-apps) più avanti in questo articolo.

### <a name="cors"></a>CORS
Invece di un'impostazione dell'app **MS_CrossDomainOrigins** delimitata da virgole, ora è disponibile un pannello nel portale di gestione di Azure per la configurazione di CORS. In alternativa, è possibile eseguirne la configurazione usando strumenti di Gestione risorse come Azure PowerShell, l'interfaccia della riga di comando o [Esplora risorse](https://resources.azure.com/). Impostare la proprietà **cors** sul tipo di risorsa **Microsoft.Web/sites/config** per la risorsa **&lt;nome sito&gt;/web**. Ad esempio:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### <a name="api-metadata"></a>Metadati dell'API
Il pannello Definizione API è ora disponibile in tutte le app Web, le app per dispositivi mobili e le app per le API. Nel portale di gestione è possibile specificare un URL relativo o un URL assoluto che punta a un endpoint che ospita una rappresentazione Swagger 2.0 dell'API. In alternativa, è possibile eseguire la configurazione usando gli strumenti di Gestione risorse. Impostare la proprietà **apiDefinition** sul tipo di risorsa **Microsoft.Web/sites/config** per la risorsa **&lt;nome sito&gt;/web**. Ad esempio:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

A questo punto, l'endpoint dei metadati deve essere accessibile pubblicamente senza autenticazione per l'utilizzo da parte di molti client downstream, ad esempio, la generazione di client API REST di Visual Studio e il flusso di "aggiunta di API" di PowerApps. Ciò significa che se si usa l'autenticazione del servizio app e si vuole esporre la definizione dell'API dall'interno dell'app stessa, è necessario usare l'opzione di autenticazione posticipata descritta in precedenza in modo che la route ai metadati Swagger sia pubblica.

## <a name="management-portal"></a>Portale di gestione
Selezionando **Nuovo > Web e dispositivi mobili > App per le API** nel portale è possibile creare app per le API che includono le nuove funzionalità descritte nell'articolo. **Esplora > App per le API** consente solo di visualizzare le nuove app per le API. Quando si passa a un'app per le API, il pannello presenta lo stesso layout e le stesse funzionalità dei pannelli delle app Web e per dispositivi mobili. Le uniche differenze sono il contenuto delle guide introduttive e l'ordinamento delle impostazioni.

Le app per le API esistenti (o le app per le API Marketplace create da app per la logica) con le funzionalità di anteprima precedenti saranno ancora visibili nella finestra di progettazione delle app per la logica e quando si esplorano tutte le risorse in un gruppo di risorse.

## <a name="visual-studio"></a>Visual Studio
La maggior parte degli strumenti delle app Web è compatibile con le nuove app per le API perché condivide lo stesso tipo di risorsa **Microsoft.Web/sites** sottostante. È tuttavia necessario eseguire l'aggiornamento degli strumenti di Visual Studio di Azure almeno alla versione 2.8.1 perché espone diverse funzionalità specifiche delle API. Scaricare l'SDK dalla [pagina dei download di Azure](https://azure.microsoft.com/downloads/).

Con la razionalizzazione dei tipi del servizio app, anche la pubblicazione è stata unificata in **Pubblica > Servizio app di Microsoft Azure**:

![Pubblicazione di app per le API](./media/app-service-api-whats-changed/api-apps-publish.png)

Per altre informazioni sull'SDK 2.8.1, leggere il [post di blog](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/)relativo all'annuncio.

In alternativa, è possibile importare manualmente il profilo di pubblicazione dal portale di gestione per abilitare la pubblicazione. Per Cloud Explorer, la generazione di codice e la selezione/creazione di app per le API, tuttavia, sarà necessario l'SDK 2.8.1 o versione successiva.

## <a name="migrating-existing-api-apps"></a>Migrazione delle app per le API esistenti
Se l'API personalizzata viene distribuita in una versione di anteprima precedente delle app per le API, è necessario eseguire la migrazione al nuovo modello di app per le API entro il 31 dicembre 2015. Poiché sia il nuovo modello che quello precedente sono basati su API Web ospitate nel servizio app, è possibile riutilizzare la maggior parte del codice esistente.

### <a name="hosting-and-redeployment"></a>Hosting e ridistribuzione
I passaggi per la ridistribuzione sono gli stessi della distribuzione nel servizio app di qualsiasi API Web esistente. Passaggi:

1. Creare un'app per le API vuota. Questa operazione può essere eseguita nel portale selezionando Nuovo > App per le API, dalla pubblicazione in Visual Studio o dagli strumenti di Gestione risorse. Se si usano gli strumenti o i modelli di Resource Manager, impostare il valore del **tipo** su **api** nel tipo di risorsa **Microsoft.Web/sites** per orientare le guide introduttive e le impostazioni nel portale di gestione verso scenari di API.
2. Connettere e distribuire il progetto nell'app per le API vuota usando uno dei meccanismi di distribuzione supportati dal servizio app. Per altre informazioni, vedere [Documentazione sulla distribuzione del servizio app di Azure](../app-service-web/web-sites-deploy.md) . 

### <a name="authentication"></a>Autenticazione
I servizi di autenticazione del servizio app supportano le stesse funzionalità disponibili con il modello di app per le API precedente. Se si usano i token di sessione e sono necessari SDK, usare gli SDK di client e server indicati di seguito:

* Client: [Azure Mobile Client SDK](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
* Server: [pacchetto di estensione di Microsoft Azure Mobile .NET Server Authentication](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) 

Se invece si usano gli SDK alfa del servizio app, gli SDK seguenti sono deprecati:

* Client: [Microsoft Azure AppService SDK](http://www.nuget.org/packages/Microsoft.Azure.AppService)
* Server: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

In particolare con Azure Active Directory, tuttavia, se si usa direttamente il token AAD non è necessario un SDK specifico del servizio app.

### <a name="internal-access"></a>Accesso interno
Il modello di app per le API precedente include un livello di accesso integrato interno che impone l'uso dell'SDK per le richieste di firma. Come descritto in precedenza, con il nuovo modello di app per le API le entità servizio AAD possono essere usate come alternativa all'autenticazione da servizio a servizio senza la necessità di un SDK specifico del servizio app. Per altre informazioni, vedere [Autenticazione dell'entità servizio per app per le API nel servizio app di Azure](app-service-api-dotnet-service-principal-auth.md).

### <a name="discovery"></a>Individuazione
Il modello di app per le API precedente contiene API per l'individuazione di altre app per le API in fase di esecuzione nello stesso gruppo di risorse dietro lo stesso gateway. Ciò risulta particolarmente utile nelle architetture che implementano modelli di microservizio. Questa funzionalità non è più supportata direttamente, ma sono disponibili diverse opzioni:

1. Usare l'API di Gestione risorse di Azure per l'individuazione.
2. Inserire Gestione API di Azure prima delle API ospitate dal servizio app. Usare Gestione API di Azure come un'interfaccia che può fornire un URL esterno stabile anche se la topologia interna viene modificata.
3. Compilare un'app per le API di individuazione e fare in modo che le altre app per le API eseguano la registrazione nell'app di individuazione all'avvio.
4. In fase di distribuzione, popolare le impostazioni app di tutte le app per le API (e dei client) con gli endpoint delle altre app per le API. Questo è possibile nelle distribuzioni di modelli, anche grazie al controllo dell'URL ora offerto dalle app per le API.

## <a name="using-api-apps-with-logic-apps"></a>Uso delle app per le API con app per la logica
Il nuovo modello di app per le API interagisce in modo ottimale con lo [schema delle app per la logica versione 2015-08-01](../app-service-logic/app-service-logic-schema-2015-08-01.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, leggere gli articoli nella sezione [Documentazione di App per le API](https://azure.microsoft.com/documentation/services/app-service/api/). Gli articoli sono stati aggiornati in base al nuovo modello di app per le API. Per informazioni dettagliate o indicazioni sulla migrazione è possibile rivolgersi ai forum:

* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-api-apps)




<!--HONumber=Nov16_HO3-->


