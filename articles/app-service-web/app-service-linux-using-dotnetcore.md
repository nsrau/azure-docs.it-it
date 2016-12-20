---
title: Uso di .NET Core nelle app Web del servizio App di Azure in Linux | Documentazione Microsoft
description: Uso di .NET Core nelle app Web del servizio App di Azure in Linux
keywords: Servizio app di Azure, app Web, dotnet, core, linux, oss
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 317d6980d304cc503cc43358c4b91459d4abd1ba
ms.openlocfilehash: a54b7413c2eb6ae67b7424a8966d0cd82cd1ce6a


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Uso di .NET Core nelle app Web in Linux #

Con l'aggiornamento più recente al back-end è stato aggiunto il supporto per .NET Core v.1.0. Definendo la configurazione dell'app Web Linux è possibile modificare lo stack di applicazioni.


## <a name="using-xplat-cli"></a>Uso dell'interfaccia della riga di comando di XPlat ##

L'interfaccia della riga di comando multipiattaforma di Azure consente di usare il comando **azure webapp config set** per modificare lo stack di applicazioni. Ecco un esempio:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Si noti che il file **aspnetcore.dll** è il file DLL dell'app. È possibile usare qualsiasi nome nell'app.

Verrà caricata l'immagine di .Net Core e verrà avviata l'app Web. È possibile controllare che le impostazioni siano state configurate correttamente usando il comando **azure webapp config show**. Ecco un esempio:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione al servizio app in Linux](./app-service-linux-intro.md) 
* [What is App Service on Linux?](app-service-linux-intro.md) (Che cos'è Servizio app in Linux?)
* [Creating Apps in an App Service on Linux](./app-service-linux-how-to-create-a-web-app.md) (Creazione di app Web nel servizio app in Linux)
* [Interfaccia della riga di comando multipiattaforma per l'app Web di Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)



<!--HONumber=Nov16_HO3-->


