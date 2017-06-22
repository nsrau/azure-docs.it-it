---
title: Uso di Ruby in App Web del Servizio app di Azure in Linux | Microsoft Docs
description: Uso di Ruby in App Web del Servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, domande frequenti, Linux, OSS, Ruby
services: app-service
documentationCenter: 
authors: aelnably
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5aeef6f31dacb1b27c605d39a35a81bd0211e06a
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


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

## <a name="next-steps"></a>Passaggi successivi
* [Definizione di App Web in Linux](app-service-linux-intro.md)
* [Creazione di app Web in App Web in Linux](app-service-linux-how-to-create-web-app.md)
* [Distribuzione dell'archivio Git locale nel servizio app di Azure](app-service-deploy-local-git.md)
* [Domande frequenti su App Web del Servizio app di Azure su Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
