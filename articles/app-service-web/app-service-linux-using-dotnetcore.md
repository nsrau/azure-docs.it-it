---
title: Uso di .NET Core in App Web del Servizio app di Azure in Linux | Microsoft Docs
description: Uso di .NET Core in App Web del Servizio app di Azure in Linux.
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
ms.date: 05/07/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 2d979b954912b8f344aa681980514821058ce5da
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---

# <a name="using-net-core-in-azure-web-app-on-linux"></a>Uso di .NET Core in App Web di Azure in Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Con l'aggiornamento più recente al back-end è stato aggiunto il supporto per .NET Core v.1.0. Definendo la configurazione dell'app Web Linux è possibile modificare lo stack di applicazioni.


## <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure ##

L'[interfaccia della riga di comando di Azure più recente](https://docs.microsoft.com/cli/azure/install-azure-cli) consente di usare il comando **azure webapp config set** per modificare lo stack di applicazioni. Di seguito è fornito un esempio:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Il file **aspnetcore.dll** è il file DLL dell'app. È possibile usare qualsiasi nome nell'app.

Viene caricata l'immagine di .Net Core e avviata l'app Web. È possibile controllare che le impostazioni siano state configurate correttamente usando il comando **azure webapp config show**. Di seguito è fornito un esempio:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Passaggi successivi
* [Definizione di App Web di Azure in Linux](app-service-linux-intro.md)
* [Creazione di app Web in App Web di Azure in Linux](./app-service-linux-how-to-create-web-app.md)
* [Interfaccia della riga di comando multipiattaforma per l'app Web di Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Domande frequenti su App Web del Servizio app di Azure su Linux](app-service-linux-faq.md)
