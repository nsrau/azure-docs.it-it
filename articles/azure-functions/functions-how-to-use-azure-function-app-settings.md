---
title: Configurare le impostazioni dell&quot;app per le funzioni di Azure | Microsoft Docs
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
ms.date: 04/23/2017
ms.author: rachelap, glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: e6f5798b260c4923452dcc96c18d4839f5c34bfc
ms.contentlocale: it-it
ms.lasthandoff: 04/25/2017


---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Come gestire un'app per le funzioni nel portale di Azure 

In Funzioni di Azure un'app per le funzioni fornisce il contesto di esecuzione per le singole funzioni. I comportamenti dell'app per le funzioni si applicano a tutte le funzioni ospitate da un'app per le funzioni specifica. In questo argomento viene descritto come configurare e gestire le app per le funzioni nel portale di Azure.

Innanzitutto passare al [portale di Azure](http://portal.azure.com) e accedere all'account di Azure. Nella barra di ricerca nella parte superiore del portale digitare il nome dell'app per le funzioni e selezionarla dall'elenco. Dopo aver selezionato l'app per le funzioni, viene visualizzata la pagina seguente:

![Panoramica dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="manage-app-service-settings"></a>Scheda delle impostazioni dell'app per le funzioni

![Panoramica dell'app per le funzioni nel portale di Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Nella scheda **Impostazioni** è possibile aggiornare la versione di runtime di Funzioni usata dall'app per le funzioni. È anche possibile gestire le chiavi host usate per limitare l'accesso HTTP a tutte le funzioni ospitate dall'app per le funzioni.

Funzioni supporta sia i piani di hosting a consumo che i piani di hosting del servizio app. Per altre informazioni vedere [Scegliere il piano di servizio corretto per Funzioni di Azure](functions-scale.md). Per una migliore prevedibilità nel piano di consumo, Funzioni consente di limitare l'uso della piattaforma impostando una quota di uso giornaliera, in secondi di gigabyte. Quando la quota di uso giornaliera viene raggiunta, l'app per le funzioni viene arrestata. In questo caso può essere riattivata dallo stesso contesto dell'impostazione della quota di spesa giornaliera. Vedere la [pagina prezzi di Funzioni di Azure](http://azure.microsoft.com/pricing/details/functions/) per altre informazioni sulla fatturazione.   

## <a name="platform-features-tab"></a>Scheda delle funzionalità della piattaforma

![Scheda delle funzionalità della piattaforma per l'app per le funzioni.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Le app per le funzioni vengono eseguite e gestite dalla piattaforma Servizio app di Azure. Di conseguenza, le app per le funzioni hanno accesso alla maggior parte delle funzionalità di piattaforma di hosting Web di base di Azure. Nella scheda **Funzionalità della piattaforma** è possibile accedere a molte funzionalità della piattaforma del servizio app che è possibile usare nelle app per le funzioni. 

> [!NOTE]
> Non tutte le funzionalità del servizio app sono disponibili quando un'app per le funzioni viene eseguita nel piano di hosting a consumo.

La restante parte di questo argomento illustra le seguenti funzionalità di servizio app nel portale di Azure utili per Funzioni:

+ [Editor del servizio app](#editor)
+ [Impostazioni dell'applicazione](#settings) 
+ [Console](#console)
+ [Strumenti avanzati (Kudu)](#kudu)
+ [Opzioni di distribuzione](#deployment)
+ [CORS](#cors)
+ [Autenticazione](#auth)
+ [Definizione dell'API](#swagger)

Per altre informazioni su come usare le impostazioni del servizio app, vedere [Configurare le impostazioni di del servizio app di Azure](../app-service-web/web-sites-configure.md).

### <a name="editor"></a>Editor del servizio app

| | |
|-|-|
| ![Editor del servizio app per l'app per le funzioni.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | L'editor del servizio app è un editor avanzato, disponibile nel portale, che si può usare per modificare i file di configurazione JSON e i file del codice nello stesso modo. Quando si sceglie questa opzione, viene aperta una scheda separata del browser con un editor di base. Ciò consente di realizzare l'integrazione con l'archivio Git, eseguire il codice e il relativo debug e modificare le impostazioni dell'app per le funzioni. Questo editor offre un ambiente di sviluppo migliorato per le funzioni confrontato con il pannello dell'app per le funzioni predefinito.    |

![Editor del servizio app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>Impostazioni dell'applicazione

| | |
|-|-|
| ![Impostazioni delle applicazioni per l'app per le funzioni.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | Nel pannello **Impostazioni applicazione** del servizio app è possibile configurare e gestire le versioni di framework, il debug remoto, le impostazioni dell'app e le stringhe di connessione. Quando l'app per le funzioni si integra con altri servizi di terze parti e di Azure, qui è possibile modificare tali impostazioni. |

![Configurare le impostazioni dell'applicazione](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Console

| | |
|-|-|
| ![Console dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | La console nel portale è uno strumento ideale per gli sviluppatori quando si desidera interagire con l'app per le funzioni dalla riga di comando. I comandi comuni includono la creazione e lo spostamento di file e directory, nonché l'esecuzione di script e file batch. |

![Console dell'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Strumenti avanzati (Kudu)

| | |
|-|-|
| ![App per le funzioni Kudu nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Gli strumenti avanzati per il servizio app, noto anche come Kudu, consentono l'accesso alle funzionalità amministrative avanzate dell'app per le funzioni. Dalla Kudu è possibile gestire informazioni di sistema, impostazioni dell'app, variabili di ambiente, estensioni del sito, intestazioni HTTP e variabili del server. È anche possibile avviare **Kudu** selezionando l'endpoint SCM per l'app per le funzioni, ad esempio`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Configurare Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opzioni di distribuzione

| | |
|-|-|
| ![Opzioni di distribuzione dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Funzioni consente di sviluppare il codice della funzione sul computer locale. È quindi possibile caricare il progetto dell'app per le funzioni locale in Azure. Oltre al caricamento FTP tradizionale, Funzioni consente di distribuire l'app per le funzioni usando le soluzioni di integrazione continua più diffusi, come GitHub, VSTS, Dropbox, Bitbucket e altre. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md). Per caricare manualmente tramite FTP o Git locale, è necessario anche [configurare le credenziali di distribuzione](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![CORS di app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Per impedire l'esecuzione di codici dannosi nei servizi, il servizio app consente di bloccare le chiamate alle app per le funzioni da origini esterne. Funzioni supporta la condivisione di risorse tra le origini, CORS per consentire la definizione di un "elenco" di origini consentite da cui le funzioni possono accettare le richieste remote.  |

![Configurare CORS per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Autenticazione

| | |
|-|-|
| ![Autenticazione dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Quando le funzioni usano un trigger HTTP, è possibile richiedere innanzitutto l'autenticazione delle chiamate. Servizio app supporta l'autenticazione di Azure Active Directory e consente di accedere ai provider social, quali Facebook, Microsoft e Twitter. Per informazioni dettagliate sulla configurazione di specifici provider di autenticazione, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md). |

![Configurare l'autenticazione per un'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definizione dell'API

| | |
|-|-|
| ![Definizione dello swagger API dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Funzioni supporta Swagger per consentire ai client di usare più facilmente le funzioni attivate da HTTP. Per altre informazioni sulla creazione di definizioni API con Swagger, visitare [Introduzione alle app per le API, ad ASP.NET e a Swagger in Azure](../app-service-api/app-service-api-dotnet-get-started.md). È anche possibile usare Proxy di Funzioni per definire una singola superficie API per le funzioni multiple. Per altre informazioni, vedere [Uso di proxy in Funzioni di Azure](functions-proxies.md). |

![Configurare l'API per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Passaggi successivi

+ [Configurare le impostazioni del servizio app di Azure](../app-service-web/web-sites-configure.md)
+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)




