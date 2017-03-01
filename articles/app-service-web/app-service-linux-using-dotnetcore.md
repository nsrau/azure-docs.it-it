---
title: Uso di .NET Core nelle app Web del servizio App di Azure in Linux | Documentazione Microsoft
description: Uso di .NET Core nelle app Web del servizio App di Azure in Linux
keywords: Servizio app di Azure, app Web, dotnet, core, linux, oss
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 769de52a85d1d5078b2ba583e94cabd22b0fde65
ms.lasthandoff: 02/17/2017


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Uso di .NET Core nelle app Web in Linux #

Con l'aggiornamento più recente al back-end è stato aggiunto il supporto per .NET Core v.1.0. Definendo la configurazione dell'app Web Linux è possibile modificare lo stack di applicazioni.


## <a name="using-xplat-cli"></a>Uso dell'interfaccia della riga di comando di XPlat ##

L'interfaccia della riga di comando multipiattaforma di Azure consente di usare il comando **azure webapp config set** per modificare lo stack di applicazioni. Di seguito è fornito un esempio:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Il file **aspnetcore.dll** è il file DLL dell'app. È possibile usare qualsiasi nome nell'app.

Viene caricata l'immagine di .Net Core e avviata l'app Web. È possibile controllare che le impostazioni siano state configurate correttamente usando il comando **azure webapp config show**. Di seguito è fornito un esempio:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Passaggi successivi
* [What is App Service on Linux?](app-service-linux-intro.md) (Che cos'è Servizio app in Linux?)
* [Creating Apps in an App Service on Linux](./app-service-linux-how-to-create-a-web-app.md) (Creazione di app Web nel servizio app in Linux)
* [Interfaccia della riga di comando multipiattaforma per l'app Web di Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Azure App Service Web Apps on Linux FAQ](app-service-linux-faq.md) (App Web del Servizio app di Azure nelle domande frequenti su Linux)
