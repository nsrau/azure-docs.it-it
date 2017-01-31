---
title: Configurare le impostazioni dell&quot;app per le funzioni di Azure | Documentazione Microsoft
description: Informazioni su come configurare le impostazioni dell&quot;app per le funzioni di Azure.
services: 
documentationcenter: .net
author: rachelappel
manager: erikre
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 771d285597b62f55f5f2bf10ccecc6872e6c8838
ms.openlocfilehash: 042d4680a7957ebfa47618c159e36527921f9bfe


---
# <a name="how-to-configure-azure-function-app-settings"></a>Come configurare le impostazioni dell'app per le funzioni di Azure
## <a name="settings-overview"></a>Panoramica sulle impostazioni
È possibile gestire le impostazioni dell'app per le funzioni di Azure facendo clic sul collegamento **Impostazioni dell'app per le funzioni** nell'angolo inferiore sinistro del portale. Le impostazioni dell'app per le funzioni di Azure si applicano a tutte le funzioni dell'app.

1. Passare al [portale di Azure](http://portal.azure.com) e accedere con il proprio account Azure.
2. Fare clic su **Impostazioni dell'app per le funzioni** nell'angolo inferiore sinistro del portale. Vengono visualizzate varie opzioni di configurazione tra cui scegliere. 

![Impostazioni dell'app per le funzioni di Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="memory-size"></a>Dimensioni memoria
È possibile configurare la quantità di memoria da allocare alle funzioni nell'app per le funzioni corrente. 

Per configurare la memoria, spostare il dispositivo di scorrimento sulla quantità di memoria desiderata. Il valore massimo è 128 MB.

![Configurare le dimensioni della memoria per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-memory-size.png)

## <a name="continuous-integration"></a>Integrazione continua
È possibile integrare l'app per le funzioni con GitHub, Visual Studio Team Services e altri servizi.

1. Fare clic sul collegamento **Configura integrazione continua**. Viene visualizzare il riquadro **Distribuzioni** con varie opzioni.
2. Nel pannello **Distribuzioni** fare clic su **Installa**. Viene visualizzato il riquadro **Origine distribuzione** con un'unica opzione: fare clic su **Scegliere l'origine** per visualizzare le origini disponibili. 
3. Fare clic su una delle origini di distribuzione disponibili per selezionarla: Visual Studio Team Services, OneDrive, Local Git Repository, GitHub, Bitbucket, DropBox o un Repository esterno. 
   
    ![Configurare l'integrazione continua per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-ci.png)
4. Immettere le credenziali e le informazioni richieste dalle varie origini di distribuzione. Le credenziali e le informazioni richieste possono variare leggermente in base all'origine selezionata. 

Dopo aver configurato l'integrazione continua, il codice connesso passato all'origine configurata viene automaticamente distribuito nell'app per le funzioni.

## <a name="authenticationauthorization"></a>Autenticazione/autorizzazione
Per le funzioni che usano un trigger HTTP, è possibile richiedere l'autenticazione delle chiamate.

1. Per configurare l'autenticazione fare clic sul collegamento **Configura autenticazione**.
2. Posizionare il pulsante **Autenticazione servizio app** su **On**.

![Configurare l'integrazione continua per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

La maggior parte dei provider di autenticazione richiede un ID client o una chiave API e un segreto; se si esegue l'accesso con il proprio account Microsoft o Facebook, tuttavia, è possibile definire gli ambiti, ovvero specifiche credenziali di autorizzazione. Active Directory offre la possibilità di impostare diverse impostazioni di configurazione rapida o avanzata.

Per informazioni dettagliate sulla configurazione di specifici provider di autenticazione, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md).

## <a name="cors"></a>CORS
In genere, per motivi di sicurezza, non sono consentite chiamate agli host (domini) da origini esterne, ad esempio chiamate Ajax da un browser. In caso contrario, è possibile che venga inviato al back-end ed eseguito codice dannoso. L'approccio più sicuro è quello di disattivare tutte le origini di codice, ad eccezione delle origini attendibili. È possibile configurare le origini da cui si accettano chiamate alle funzioni di Azure tramite la condivisione di risorse tra le origini (CORS). Questo servizio consente di elencare i domini origine di JavaScript che possono chiamare funzioni nell'app per le funzioni di Azure. 

1. Per configurare il servizio CORS, fare clic sul collegamento **Configura CORS**. 
2. Specificare i domini che si vuole inserire nell'elenco dei domini consentiti.

![Configurare CORS per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

## <a name="api-definition"></a>Definizione API
Consente ai client di usare più facilmente le funzioni attivate da HTTP.

1. Per configurare un'API, fare clic su **Configura metadati dell'API**. 
2. Immettere un URL che punti a un file json di Swagger.

![Configurare l'API per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)

Per altre informazioni sulla creazione di definizioni API con Swagger, visitare [Introduzione alle app per le API, ad ASP.NET e a Swagger in Azure](../app-service-api/app-service-api-dotnet-get-started.md).

## <a name="application-settings"></a>Impostazioni dell'applicazione
Gestire variabili di ambiente, versioni del framework, il debug remoto, le impostazioni dell'app, le stringhe di connessione, i documenti predefiniti e così via: queste impostazioni sono specifiche di ogni app per le funzioni. 

Per configurare le impostazioni dell'app, fare clic sul collegamento **Configurare le impostazioni dell'app**. 

![Configurare le impostazioni dell'app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

## <a name="in-portal-console"></a>Console incorporata nel portale
Nella console incorporata nel portale delle funzioni di Azure è possibile eseguire i comandi in stile DOS. I comandi comuni includono la creazione e lo spostamento di file e directory, nonché l'esecuzione di script e file batch. 

> [!NOTE]
> È anche possibile caricare script, ma prima è necessario configurare un client FTP nella sezione **Impostazioni avanzate** delle funzioni di Azure.
> 
> 

Per aprire la console incorporata nel portale, fare clic su **Apri console sviluppo**.

![Configurare le dimensioni della memoria per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

> [!NOTE]
> La grafica ASCII conferisce alla console un aspetto più accattivante.
> 
> 

## <a name="kudu"></a>Kudu
Kudu consente di accedere alle funzionalità di amministrazione avanzate di un'app per le funzioni.

Per aprire Kudu, fare clic su **Vai a Kudu**. Viene aperta una nuova finestra del browser con le funzioni di amministrazione Web di Kudu.

> [!NOTE]
> In alternativa, è possibile avviare **Kudu** inserendo "scm" nell'URL della funzione, come illustrato di seguito: ```https://<YourFunctionName>.scm.azurewebsites.net/```
> 
> 

Dalla pagina Web Kudu è possibile visualizzare e gestire informazioni di sistema, impostazioni dell'app, variabili di ambiente, intestazioni HTTP, variabili del server e altro ancora.

![Configurare Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

## <a name="advanced-settings"></a>Impostazioni avanzate
È possibile gestire l'app per le funzioni come qualsiasi altra istanza del servizio app. Questa opzione consente di accedere a tutte le impostazioni descritte in precedenza, oltre ad altre impostazioni.  

Per aprire le impostazioni avanzate, fare clic sul collegamento **Impostazioni avanzate**. 

![Configurare le impostazioni del servizio app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-settings.png)

Per informazioni dettagliate su come configurare ogni impostazione del servizio app, vedere [Configurare le impostazioni del servizio app di Azure](../app-service-web/web-sites-configure.md).

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Dec16_HO3-->


