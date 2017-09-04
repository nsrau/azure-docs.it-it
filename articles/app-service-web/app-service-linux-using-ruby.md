---
title: Uso di Ruby in App Web del Servizio app di Azure in Linux | Microsoft Docs
description: Uso di Ruby in App Web del Servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, domande frequenti, Linux, OSS, Ruby
services: app-service
documentationCenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 56105d1bc153e552e12c0c408c8f6075e4eff9d0
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---

# <a name="using-ruby-in-web-app-on-linux"></a>Uso di Ruby in App Web in Linux #

Con l'aggiornamento più recente al back-end è stato aggiunto il supporto per Ruby v.2.3. Definendo la configurazione dell'app Web Linux è possibile modificare lo stack di applicazioni.

## <a name="using-the-azure-portal"></a>Uso del portale di Azure ##

Dal menu Nuovo nel [portale di Azure](https://portal.azure.com), è possibile scegliere di creare un'app Web in Linux dall'opzione Web e dispositivi mobili come illustrato nella figura seguente:

![Avviare la creazione di un'app Web nel portale di Azure][1]

Viene quindi aperto il **pannello Crea**, come illustrato nell'immagine seguente:

![Pannello Crea][2]

1. Assegnare all'app Web un nome.
2. Scegliere un gruppo di risorse esistente oppure crearne uno nuovo. Vedere le aree disponibili nella [sezione sulle limitazioni](app-service-linux-intro.md).
3. Scegliere un piano di servizio app di Azure esistente o crearne uno nuovo. Vedere le note sul piano di servizio app nella [sezione sulle limitazioni](app-service-linux-intro.md).
4. Scegliere Ruby tra gli stack di runtime predefiniti.

Dopo aver creato un'applicazione Web Ruby, è possibile distribuirla tramite Git o FTP.

Per altre informazioni sulla creazione di un'app Ruby, vedere la [guida introduttiva](app-service-linux-ruby-get-started.md)

## <a name="next-steps"></a>Passaggi successivi
* [Definizione di App Web in Linux](app-service-linux-intro.md)
* [Distribuzione dell'archivio Git locale nel servizio app di Azure](app-service-deploy-local-git.md)
* [Domande frequenti su App Web del Servizio app di Azure su Linux](app-service-linux-faq.md)
* [Creare un'app Ruby con App Web di Azure in Linux](app-service-linux-ruby-get-started.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
