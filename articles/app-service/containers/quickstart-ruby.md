---
title: "Guida introduttiva: Creare un'app Ruby in Linux"
description: Introduzione all'uso delle app Linux nel servizio app di Azure distribuendo la prima app Ruby in un contenitore Linux nel servizio app.
keywords: servizio app di azure, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: 17c81ab71352f57731d9b36138c88c34421e374c
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689022"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>Creare un'app Ruby on Rails con il Servizio app di Azure in Linux

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa esercitazione di avvio rapido illustra come distribuire un'app Ruby on Rails in Servizio app di Azure in Linux tramite [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

> [!NOTE]
> In questo momento lo stack di sviluppo di Ruby supporta solo Ruby on Rails. Per usare una piattaforma diversa, ad esempio Sinatra, o per usare una [versione non supportata di Ruby](app-service-linux-intro.md), è necessario [eseguirla in un contenitore personalizzato](quickstart-docker-go.md).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Installare Ruby 2.6 o versione successiva</a>
* <a href="https://git-scm.com/" target="_blank">Installare Git</a>

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra del terminale eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale

Eseguire l'applicazione in locale, in modo da verificare l'aspetto che assumerà dopo la distribuzione in Azure. Aprire una finestra del terminale, passare alla directory `hello-world` e usare il comando `rails server` per avviare il server.

Il primo passaggio consiste nell'installare le gemme necessarie. Nell'esempio è incluso un `Gemfile`, quindi è sufficiente eseguire il comando seguente:

```bash
bundle install
```

Una volta installate le gemme, useremo il bundler per avviare l'app:

```bash
bundle exec rails server
```

Tramite il Web browser, passare a `http://localhost:3000` per testare l'app in locale.

![Hello World configurato](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Passare all'app per visualizzare l'app Web con immagine incorporata appena creata. Sostituire _&lt;app name>_ con il nome dell'app Web.

```bash
http://<app_name>.azurewebsites.net
```

Ecco l'aspetto che avrà la nuova app Web:

![Pagina iniziale](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Distribuire l'applicazione

Eseguire i comandi seguenti per distribuire l'applicazione locale nel sito Web di Azure:

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

Verificare che per le operazioni di distribuzione in remoto venga segnalato l'esito positivo. I comandi generano un output simile al testo seguente:

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

Al termine della distribuzione, attendere circa 10 secondi per il riavvio dell'app Web, quindi passare all'app Web e verificare i risultati.

```bash
http://<app-name>.azurewebsites.net
```

![app Web aggiornata](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Durante il riavvio dell'app potrebbe essere visualizzato il codice di stato HTTP `Error 503 Server unavailable` nel browser o la pagina predefinita `Hey, Ruby developers!`. Il completamento del riavvio dell'app potrebbe richiedere alcuni minuti.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Ruby on Rails con Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Configurare l'app Ruby](configure-language-ruby.md)
