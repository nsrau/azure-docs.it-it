---
title: Come usare un&quot;immagine Docker personalizzata per App Web di Azure in Linux | Microsoft Docs
description: Come usare un&quot;immagine Docker personalizzata per App Web di Azure in Linux.
keywords: Servizio app di Azure, app Web, Linux, Docker, contenitore
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: d55cfc354ad5a9fc0f06b671f441ba4a0616bb9a
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017


---

# <a name="using-a-custom-docker-image-for-azure-web-app-on-linux"></a>Uso di un'immagine Docker personalizzata per App Web di Azure in Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Il servizio app fornisce stack di applicazioni predefiniti in Linux con il supporto per versioni specifiche, ad esempio PHP 7.0 e Node.js 4.5. Il servizio app in Linux usa i contenitori Docker per ospitare questi stack di applicazioni predefiniti. È anche possibile usare un'immagine Docker personalizzata per distribuire l'app Web in uno stack di applicazioni non ancora definito in Azure. Le immagini Docker personalizzate possono essere ospitate in un repository Docker pubblico o privato.


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>Procedura: Impostare un'immagine Docker personalizzata per un'app Web
È possibile impostare l'immagine Docker personalizzata per app Web sia nuove che esistenti. Quando si crea un'app Web in Linux nel [portale di Azure](https://portal.azure.com), fare clic su **Configura contenitore** per impostare un'immagine Docker personalizzata:

![Immagine Docker personalizzata per una nuova app Web in Linux][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>Procedura: usare un'immagine Docker personalizzata dall'hub Docker ##
Per usare un'immagine Docker personalizzata dall'hub Docker:

1. Nel [portale di Azure](https://portal.azure.com) trovare l'app Web in Linux, quindi in **Impostazioni** fare clic su **Contenitore Docker**.

2.  Selezionare **Docker Hub** (Hub Docker) come **Origine immagine**, quindi fare clic su **Pubblico** o **Privato** e digitare il **nome Immagine e tag facoltativo**, ad esempio `node:4.5`. Il **Comando Avvia** viene impostato automaticamente in base al contenuto definito nel file di immagine Docker, ma è possibile impostare i propri comandi.  

    ![Configurare l'immagine del repository pubblico dell'hub Docker][2]

    Quando l'immagine proviene da un repository privato, è anche necessario immettere le credenziali dell'hub Docker (**Nome utente di accesso** e **Password**) per il repository dell'hub Docker privato.

    ![Configurare l'immagine del repository privato dell'hub Docker][3]

3. Dopo avere configurato il contenitore, fare clic su **Salva**.

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>Come usare un'immagine Docker da un registro di sistema di immagini privato ##
Per usare un'immagine Docker personalizzata proveniente da un registro di sistema di immagini privato:

1. Nel [portale di Azure](https://portal.azure.com) trovare l'app Web in Linux, quindi in **Impostazioni** fare clic su **Contenitore Docker**.

2.  Fare clic su **Registro di sistema privato** come **Origine immagine**. Immettere il **Image and optional tag name** (Nome tag opzionale e immagine), l'**URL server** del registro di sistema privato, insieme alle credenziali (**nome utente di accesso** e **password**). Fare clic su **Save**.

    ![Configurare l'immagine Docker dal registro di sistema privato][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>Procedura: Impostare la porta usata dall'immagine Docker ##

Quando si usa un'immagine Docker personalizzata per l'app Web, è possibile usare la variabile di ambiente `PORT` nel file Docker, che viene aggiunta al contenitore generato. Considerare l'esempio seguente di file Docker per un'applicazione Ruby:

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p $PORT -e production

Nell'ultima riga del comando è possibile osservare che la variabile di ambiente PORT viene passata in fase di esecuzione. Tenere presente che la distinzione tra maiuscole e minuscole è importante nei comandi.

Quando si usa un'immagine Docker esistente compilata da un altro utente, potrebbe essere necessario specificare una porta diversa dalla porta 80 per l'immagine. Per configurare la porta, aggiungere un'impostazione di applicazione denominata `PORT` con il valore come illustrato di seguito:

![Configurare l'impostazione app PORT per l'immagine Docker personalizzata][6]


## <a name="how-to-switch-back-to-using-a-built-in-image"></a>Procedura: Usare di nuovo un'immagine predefinita ##

Per passare da un'immagine personalizzata a un'immagine predefinita:

1. Nel [portale di Azure](https://portal.azure.com) trovare l'app Web in Linux, quindi in **Impostazioni** fare clic su **Servizio app**.

2. Selezionare lo **Stack di runtime** da usare per l'immagine predefinita, quindi fare clic su **Salva**. 

![Configurare l'immagine Docker predefinita][5]


## <a name="troubleshooting"></a>Risoluzione dei problemi ##

Quando non è possibile avviare l'applicazione con l'immagine Docker personalizzata, controllare i log di Docker nella directory LogFiles/docker. È possibile accedere a questa directory tramite il sito SCM o tramite FTP.
Per registrare `stdout` e `stderr` dal contenitore, è necessario abilitare **Registrazione server Web** in **Log di diagnostica**.

![Abilitazione della registrazione][8]

![Uso di Kudu per visualizzare i log di Docker][7]

È possibile accedere al sito SCM da **Strumenti avanzati** nel menu **Strumenti di sviluppo**.

## <a name="next-steps"></a>Passaggi successivi ##

Fare clic sui collegamenti seguenti per iniziare a usare App Web in Linux.   

* [Introduzione ad App Web di Azure in Linux](./app-service-linux-intro.md)
* [Creazione di app Web in App Web di Azure in Linux](./app-service-linux-how-to-create-web-app.md)
* [Uso della configurazione PM2 per Node.js in App Web in Linux](./app-service-linux-using-nodejs-pm2.md)
* [Domande frequenti su App Web del Servizio app di Azure su Linux](app-service-linux-faq.md)

Pubblicare domande e dubbi nel [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png
[8]: ./media/app-service-linux-using-custom-docker-image/logging.png

