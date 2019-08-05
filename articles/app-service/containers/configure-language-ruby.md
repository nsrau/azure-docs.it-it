---
title: Configurare le app Ruby - Servizio app di Azure
description: Questa esercitazione descrive le opzioni per la creazione e la configurazione delle app Ruby per il Servizio app di Azure in Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: cephalin
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: 222ded620610957e752e2081bda638d78eba4867
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619448"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Configurare un'app Ruby in Linux per il Servizio app di Azure

Questo articolo descrive in che modo il [Servizio app di Azure](app-service-linux-intro.md) esegue le app Ruby e come è possibile personalizzare il comportamento del servizio app quando è necessario. Le app Ruby devono essere distribuite con tutti i moduli [pip](https://pypi.org/project/pip/) richiesti.

Questa guida illustra i concetti chiave e le istruzioni per gli sviluppatori Ruby che usano un contenitore Linux predefinito nel servizio app. Se non si è mai usato il Servizio app di Azure, è consigliabile seguire per prima cosa l'[Avvio rapido di Ruby](quickstart-ruby.md) e l'[esercitazione di Ruby con PostgreSQL](tutorial-ruby-postgres-app.md).

## <a name="show-ruby-version"></a>Visualizzare la versione di Ruby

Per visualizzare la versione corrente di Ruby, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni di Ruby supportate, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

È possibile eseguire una versione non supportata di Ruby creando un'immagine del contenitore personalizzata. Per altre informazioni, vedere [Usare un'immagine Docker personalizzata](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Configurare la versione di Ruby

Eseguire il comando seguente in [Cloud Shell](https://shell.azure.com) per impostare la versione di Ruby su 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Se vengono visualizzati errori simili al seguente durante la fase di distribuzione:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> oppure
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Significa che la versione di Ruby configurata nel progetto è diversa rispetto alla versione installata nel contenitore in esecuzione (`2.3.3` nell'esempio precedente). Nell'esempio precedente controllare sia *Gemfile* che *.ruby-version* e verificare che la versione di Ruby non sia configurata o che sia impostata sulla versione installata nel contenitore in esecuzione (`2.3.3` nell'esempio precedente).

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) al di fuori del codice dell'app. Quindi è possibile accedervi usando il modello standard [ENV['\<nome-percorso>']](https://ruby-doc.org/core-2.3.3/ENV.html). Ad esempio, per accedere a un'impostazione dell'app denominata `WEBSITE_SITE_NAME`, usare il codice seguente:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Personalizzare la distribuzione

Quando si distribuisce un [repository Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) o un [pacchetto Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) con processi di compilazione attivati, per impostazione predefinita il motore di distribuzione (Kudu) esegue automaticamente la procedura di post-distribuzione seguente:

1. Controllare se *Gemfile* esiste.
1. Eseguire `bundle clean`. 
1. Eseguire `bundle install --path "vendor/bundle"`.
1. Eseguire `bundle package` per creare un pacchetto di gemme nella cartella vendor/cache.

### <a name="use---without-flag"></a>Usare il flag --without

Per eseguire `bundle install` con il flag [--without](https://bundler.io/man/bundle-install.1.html), impostare l'[impostazione dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `BUNDLE_WITHOUT` su un elenco di gruppi delimitato da virgole. Ad esempio, il comando seguente la imposta su `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Se questa impostazione viene definita, il motore di distribuzione esegue `bundle install` con `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Precompilare gli asset

Per impostazione predefinita, la procedura di post-distribuzione non precompila gli asset. Per attivare la precompilazione degli asset, impostare l'[impostazione dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `ASSETS_PRECOMPILE` su `true`. Il comando `bundle exec rake --trace assets:precompile` verrà eseguito al termine della procedura di post-distribuzione. Ad esempio:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Per altre informazioni, vedere [Gestire asset statici](#serve-static-assets).

## <a name="customize-start-up"></a>Personalizzare l’avvio

Per impostazione predefinita, il contenitore Ruby avvia il server Rails nella sequenza seguente (per altre informazioni, vedere lo [script di avvio](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Generare un valore [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security), se non esiste già. Questo valore è obbligatorio per l'esecuzione dell'app in modalità di produzione.
1. Impostare la variabile di ambiente `RAILS_ENV` su `production`.
1. Eliminare eventuali file con estensione *pid* nella directory *tmp/pids* originata da un server Rails eseguito in precedenza.
1. Controllare se sono state installate tutte le dipendenze. In caso contrario, provare a installare le gemme dalla directory *vendor/cache* locale.
1. Eseguire `rails server -e $RAILS_ENV`.

È possibile personalizzare il processo di avvio nei modi seguenti:

- [Gestire asset statici](#serve-static-assets)
- [Procedere all'esecuzione in modalità non di produzione](#run-in-non-production-mode)
- [Impostare manualmente secret_key_base](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Gestire asset statici

Per impostazione predefinita, il server Rails nel contenitore Ruby viene eseguito in modalità di produzione e [presuppone che gli asset vengano precompilati e siano gestiti dal server Web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Per gestire asset statici dal server Rails, è necessario eseguire due operazioni:

- **Precompilare gli asset** - [Precompilare gli asset statici localmente](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) e distribuirli manualmente. In alternativa, possono essere gestiti dal motore di distribuzione (vedere [Precompilare gli asset](#precompile-assets).
- **Abilitare la gestione di file statici** - Per gestire asset statici dal contenitore Ruby, [impostare l'impostazione `RAILS_SERVE_STATIC_FILES` dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) su `true`. Ad esempio:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Procedere all'esecuzione in modalità non di produzione

Per impostazione predefinita, il server Rails viene eseguito in modalità di produzione. Per l'esecuzione in modalità di sviluppo, ad esempio, impostare l'[impostazione dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `RAILS_ENV` su `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Tuttavia, questa impostazione da sola fa sì che il server Rails venga avviato in modalità di sviluppo, accettando solo richieste localhost e non risultando accessibile al di fuori del contenitore. Per accettare le richieste dei client remoti, impostare l'[impostazione dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `APP_COMMAND_LINE` su `rails server -b 0.0.0.0`. Questa impostazione dell'app consente di eseguire un comando personalizzato nel contenitore Ruby. Ad esempio:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a>Impostare manualmente secret_key_base

Per usare un valore `secret_key_base` personalizzato invece di quello generato automaticamente dal servizio app, configurare l'[impostazione dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `SECRET_KEY_BASE` con il valore desiderato. Ad esempio:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Rails con PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)
