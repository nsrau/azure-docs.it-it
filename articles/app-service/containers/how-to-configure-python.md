---
title: Configurare le app Python - Servizio app di Azure
description: Questa esercitazione descrive le opzioni per la creazione e la configurazione delle app Python per il Servizio app di Azure in Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: cephalin
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: 8563e0ac060e5cce6853472dfb1c51c6c2c36a4d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071080"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Configurare un'app Python in Linux per il servizio app di Azure

Questo articolo descrive il modo in cui il [servizio app di Azure](app-service-linux-intro.md) esegue le app Python e come è possibile personalizzare il comportamento del servizio app quando è necessario. Le app Python devono essere distribuite con tutti i moduli [pip](https://pypi.org/project/pip/) richiesti.

Il motore di distribuzione del servizio app attiva un ambiente virtuale ed esegue automaticamente `pip install -r requirements.txt` quando si distribuisce un [repository Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) o un [pacchetto Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) con processi di compilazione attivati.

Questa guida illustra i concetti chiave e le istruzioni per gli sviluppatori Python che usano un contenitore Linux predefinito nel servizio app. Se non si è mai usato il servizio app di Azure, è consigliabile seguire per prima cosa l'[avvio rapido di Python](quickstart-python.md) e l'[esercitazione di Python con PostgreSQL](tutorial-python-postgresql-app.md).

> [!NOTE]
> Linux è attualmente la soluzione consigliata per l'esecuzione di app Python nel servizio app. Per informazioni sulla possibilità di usare Windows, vedere [Python per la versione Windows del servizio app ](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service).
>

## <a name="show-python-version"></a>Visualizzare la versione di Python

Per visualizzare la versione corrente di Python, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Per visualizzare tutte le versioni supportate di Python, eseguire il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

È possibile eseguire una versione non supportata di Python creando un'immagine del contenitore personalizzata. Per altre informazioni, vedere [Usare un'immagine Docker personalizzata](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Configurare la versione di Python

Eseguire il comando seguente in [Cloud Shell](https://shell.azure.com) per impostare la versione di Python su 3.7:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="container-characteristics"></a>Caratteristiche del contenitore

Le app Python distribuite nel servizio app in Linux vengono eseguite all'interno di un contenitore Docker definito nel repository GitHub, [Python 3.6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) o [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Questo contenitore presenta le caratteristiche seguenti:

- Le app vengono eseguite con il [Server HTTP WSGI Gunicorn](https://gunicorn.org/), usando gli argomenti aggiuntivi `--bind=0.0.0.0 --timeout 600`.

- Per impostazione predefinita, l'immagine di base include il framework Web Flask, ma il contenitore supporta altri framework conformi a WSGI e compatibili con Python 3.7 come ad esempio Django.

- Per installare pacchetti aggiuntivi, ad esempio Django, creare un file [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) nella radice del progetto con `pip freeze > requirements.txt`. Pubblicare quindi il progetto nel servizio app con la distribuzione Git, che esegue automaticamente `pip install -r requirements.txt` nel contenitore per installare le dipendenze dell'app.

## <a name="container-startup-process"></a>Processo di avvio del contenitore

Durante l'avvio il servizio app in un contenitore Linux esegue i passaggi seguenti:

1. Usa un [comando di avvio personalizzato](#customize-startup-command), se specificato.
2. Verifica l'esistenza di un'[app Django](#django-app) e avvia Gunicorn, se rilevato.
3. Verifica l'esistenza di un'[app Flask](#flask-app) e avvia Gunicorn, se rilevato.
4. Se non vengono trovate altre app, avvia un'app predefinita integrata nel contenitore.

Le sezioni seguenti forniscono informazioni dettagliate aggiuntive su ogni opzione.

### <a name="django-app"></a>App Django

Per le app Django il servizio app cerca un file denominato `wsgi.py` all'interno del codice dell'app e quindi esegue Gunicorn usando il comando seguente:

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Se si vuole un controllo più specifico sul comando di avvio, usare un [comando di avvio personalizzato](#customize-startup-command) e sostituire `<module>` con il nome del modulo che contiene *wsgi.py*.

### <a name="flask-app"></a>App Flask

Per Flask il servizio app cerca un file denominato *application.py* o *app.py* e avvia Gunicorn come segue:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Se il modulo principale dell'app è contenuto in un file diverso, usare un nome diverso per l'oggetto app oppure se si vogliono fornire argomenti aggiuntivi a Gunicorn, usare un [comando di avvio personalizzato](#customize-startup-command).

### <a name="default-behavior"></a>Comportamento predefinito

Se il servizio app non trova un comando personalizzato, un'app Django o un'app Flask, verrà eseguita un'app predefinita di sola lettura che si trova nella cartella _opt/defaultsite_. L'app predefinita è simile alla seguente:

![Pagina Web predefinita del servizio app in Linux](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>Personalizzare un comando di avvio

È possibile controllare il comportamento di avvio del contenitore specificando un comando di avvio Gunicorn personalizzato. A questo scopo, immettere il comando seguente in [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Se, ad esempio, si ha un'app Flask il cui modulo principale è *hello.py* e l'oggetto app Flask incluso nel file è denominato `myapp`, il *\<custom-command>* dovrà essere:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Se il modulo principale è contenuto in una sottocartella, ad esempio `website`, specificare tale cartella con l'argomento `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

È anche possibile aggiungere al *\<custom-command>* anche altri argomenti per Gunicorn, ad esempio `--workers=4`. Per altre informazioni, vedere l'articolo relativo all'[esecuzione di Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Per un server non Gunicorn, ad esempio [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), è possibile sostituire il *\<custom-command >* con un comando simile al seguente:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> Il servizio app ignora gli eventuali errori che possono verificarsi durante l'elaborazione di un file di comando personalizzato, quindi continua il processo di avvio cercando le app Django e Flask. Se non viene visualizzato il comportamento previsto, verificare che il file di avvio sia stato distribuito nel servizio app e che non contenga errori.

## <a name="access-environment-variables"></a>Accedere alle variabili di ambiente

Nel servizio app è possibile [configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) al di fuori del codice dell'app. Quindi è possibile accedervi usando il modello standard [os.environ](https://docs.python.org/3/library/os.html#os.environ). Ad esempio, per accedere a un'impostazione dell'app denominata `WEBSITE_SITE_NAME`, usare il codice seguente:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Rilevare una sessione HTTPS

Nel servizio app la [terminazione SSL](https://wikipedia.org/wiki/TLS_termination_proxy) si verifica nei servizi di bilanciamento del carico di rete, pertanto tutte le richieste HTTPS raggiungano l'app come richieste HTTP non crittografate. Se la logica dell'app deve controllare se le richieste degli utenti sono crittografate o meno, esaminare l'intestazione `X-Forwarded-Proto`.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

I framework Web più diffusi consentono di accedere alle informazioni `X-Forwarded-*` nel modello di app standard. In [CodeIgniter](https://codeigniter.com/) [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) controlla il valore di `X_FORWARDED_PROTO` per impostazione predefinita.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Aprire una sessione SSH nel browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi

- **Dopo avere distribuito il codice app personalizzato viene visualizzata l'app predefinita.** L'app predefinita viene visualizzata perché il codice app personalizzato non è stato distribuito nel servizio app o perché il servizio app non è riuscito a trovare il codice app personalizzato e ha quindi eseguito l'app predefinita.
- Riavviare il servizio app, attendere 15-20 secondi e verifica di nuovo l'app.
- Assicurarsi di usare il servizio app per Linux anziché un'istanza basata su Windows. Dall'interfaccia della riga di comando di Azure, eseguire il comando `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`, sostituendo `<resource_group_name>` e `<app_service_name>` di conseguenza. L'output dovrebbe essere `app,linux`; in caso contrario, creare di nuovo il servizio app e scegliere Linux.
- Usare SSH o la console Kudu per connettersi direttamente al servizio app e verificare che i file siano presenti in *site/wwwroot*. Se i file non sono presenti, controllare il processo di distribuzione e ridistribuire l'app.
- Se i file sono presenti, il servizio app non è riuscito a identificare il file di avvio specifico. Verificare che l'app sia strutturata come prevista dal servizio app per [Django](#django-app) o [Flask](#flask-app) oppure usare un [comando di avvio personalizzato](#customize-startup-command).
- **Nel browser viene visualizzato il messaggio "Servizio non disponibile".** Si è verificato un timeout del browser durante l'attesa di una risposta dal servizio app, che indica che il servizio app ha avviato il server Gunicorn, ma gli argomenti che specificano il codice app non sono corretti.
- Aggiornare il browser, in particolare se si usano i piani tariffari inferiori nel piano di servizio app. Ad esempio, l'avvio dell'app potrebbe richiedere più tempo quando si usano i livelli gratuiti e l'app potrebbe rispondere dopo l'aggiornamento del browser.
- Verificare che l'app sia strutturata come prevista dal servizio app per [Django](#django-app) o [Flask](#flask-app) oppure usare un [comando di avvio personalizzato](#customize-startup-command).
- [Accedere al flusso di log](#access-diagnostic-logs).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Python con PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire la distribuzione da un repository di contenitore privato](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Domande frequenti sul Servizio app di Azure in Linux](app-service-linux-faq.md)
