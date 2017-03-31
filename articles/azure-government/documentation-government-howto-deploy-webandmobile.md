---
title: Distribuire nel Servizio app di Azure usando Visual Studio 2015 | Documentazione Microsoft
description: Questo articolo descrive come distribuire un&quot;App Web, un&quot;app per le API o un&quot;app per dispositivi mobili in Azure per enti pubblici usando Visual Studio 2015 e Azure SDK.
services: azure-government
cloud: gov
documentationcenter: 
author: sdubeymsft
manager: zakramer
ms.assetid: 8f9a3700-b9ee-43b7-b64d-2e6c3b57d4c0
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/03/2016
ms.author: sdubeymsft
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 159c891dedb66dfaa238981b395b4bba074c5716
ms.lasthandoff: 02/22/2017


---
# <a name="deploy-to-azure-app-services-using-visual-studio-2015"></a>Distribuire al Servizio app di Azure usando Visual Studio 2015
Questo articolo descrive come distribuire un'app del Servizio app di Azure (app per le API, App Web, app per dispositivi mobili) in Azure per enti pubblici usando Visual Studio 2015.

## <a name="prerequisites"></a>Prerequisiti
* Vedere la sezione [prerequisiti di Visual Studio] (... / app-service-api/app-service-api-dotnet-get-started.md#prerequisites) per installare e configurare Visual Studio 2015 e Azure SDK.
* Seguire [queste istruzioni] (documentation-government-manage-subscriptions.md) per configurare Visual Studio per la connessione all'account di Azure per enti pubblici.

## <a name="open-app-project-in-visual-studio"></a>Aprire il progetto dell'app in Visual Studio
* Aprire la soluzione/il progetto dell'app in Visual Studio, creare un progetto seguendo [queste istruzioni] (../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application) oppure scaricare l'app di esempio seguendo [questa procedura] (../app-service-api/app-service-api-dotnet-get-started.md#download-the-sample-application).
* Eseguire l'app in Visual Studio per accertarsi che funzioni in locale.

## <a name="deploy-to-azure-government"></a>Distribuire in Azure per enti pubblici
* Una volta che **Visual Studio è configurato per connettersi all'account Azure per enti pubblici** (operazione già effettuata nella sezione dei prerequisiti), le istruzioni per distribuire nel servizio app sono esattamente le stesse per Azure pubblico.
* Per distribuire l'app, seguire [questa procedura] (../app-service-api/app-service-api-dotnet-get-started.md#a-idcreateapiappa-create-an-api-app-in-azure-and-deploy-code-to-it).

### <a name="references"></a>Riferimenti
* [Distribuire un'app Web ASP.NET nel Servizio app di Azure con Visual Studio] (../app-service-web/web-sites-dotnet-get-started.md)
* Per altri modi di distribuire un'app, vedere [Distribuire l'app nel Servizio app di Azure] (../app-service-web/web-sites-deploy.md)
* Per la documentazione generale sul Servizio app, vedere [Documentazione sul Servizio app - App per le API] (../app-service-api/index.md)

## <a name="next-steps"></a>Passaggi successivi
Per maggiori informazioni e aggiornamenti, iscriversi al [blog di Microsoft Azure per enti pubblici](https://blogs.msdn.microsoft.com/azuregov/).

