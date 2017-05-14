---
title: Introduzione al servizio app in Linux | Microsoft Docs
description: Informazioni sul servizio app in Linux.
keywords: Servizio app di Azure, Linux, OSS
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 94aaca68827ea847be02d879088a85d96dd24663
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="introduction-to-app-service-on-linux"></a>Introduzione al servizio app in Linux
Il servizio app di Azure in Linux è attualmente in anteprima pubblica e supporta l'esecuzione di app Web in modo nativo in Linux.

## <a name="overview"></a>Panoramica
I clienti possono usare il servizio app in Linux per ospitare app Web in modo nativo in Linux per stack di applicazioni supportate. La sezione seguente elenca gli stack di applicazioni attualmente supportati. 

## <a name="features"></a>Funzionalità
Il servizio app in Linux supporta attualmente gli stack di applicazioni seguenti:

* Node.js
    * 4.4
    * 4.5
    * 6.2
    * 6.6
    * 6.9
* PHP
    * 5.6
    * 7.0
* .Net Core
    * 1.0
    * 1.1
* Ruby
    * 2.3

I clienti possono distribuire le applicazioni tramite:

* FTP
* Repository Git locale
* GitHub
* Bitbucket

Per il ridimensionamento delle applicazioni:

* I clienti possono aumentare e ridurre le prestazioni delle app Web modificando il livello nel piano di servizio app.
* I clienti possono scalare orizzontalmente le applicazioni ed eseguire più istanze di un'app entro i confini dello SKU.

Per Kudu, alcune delle funzionalità di base vengono usate con gli elementi seguenti:

* Ambienti
* Distribuzioni
* Console di base

## <a name="limitations"></a>Limitazioni
Il portale di Azure visualizza solo le funzionalità che possono essere usate attualmente per il servizio app in Linux e nasconde le altre. Man mano che verranno abilitate nuove funzionalità, queste diventeranno visibili nel portale.

Alcune funzionalità, quali l'integrazione delle reti virtuali, l'autenticazione di Azure Active Directory o di terze parti o le estensioni del sito Kudu, attualmente non funzionano. Man mano che queste funzionalità verranno abilitate, la documentazione verrà aggiornata e le modifiche pubblicate nei blog.

Questa anteprima pubblica è attualmente disponibile solo nelle aree seguenti:

* Stati Uniti occidentali
* Europa occidentale 
* Asia sudorientale

App Web in Linux è supportato solo nei piani di servizio app dedicati e non ha un livello Gratuito o Condiviso. I piani di servizio app per le app Web normali e di Linux si escludono a vicenda, quindi non è possibile creare un'app Web di Linux in un piano di servizio app non Linux.

Le app Web in Linux devono essere create in un gruppo di risorse che non contiene app Web non Linux nella stessa area.

Le app Web in Linux non supportano ancora la distribuzione di app .NET Core da un'origine non compilata. È necessario prima di tutto pubblicare/compilare l'app .NET Core in locale e quindi eseguire il push dei bit del sito pubblicato nell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
Vedere i collegamenti seguenti per iniziare a usare il servizio app in Linux. È possibile pubblicare domande e dubbi nel [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creating Apps in an App Service on Linux](app-service-linux-how-to-create-a-web-app.md) (Creazione di app Web nel servizio app in Linux)
* [Come usare un'immagine Docker personalizzata per il servizio app in Linux](app-service-linux-using-custom-docker-image.md)
* [Uso della configurazione PM2 per Node.js nelle app Web in Linux](app-service-linux-using-nodejs-pm2.md)
* [Uso di .NET Core nelle app Web del servizio App di Azure in Linux](app-service-linux-using-dotnetcore.md)
* [Uso di Ruby nelle app Web del servizio app di Azure in Linux](app-service-linux-using-ruby.md)
* [Azure App Service Web Apps on Linux FAQ](app-service-linux-faq.md) (App Web del Servizio app di Azure nelle domande frequenti su Linux)


