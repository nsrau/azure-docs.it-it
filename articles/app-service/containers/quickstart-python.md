---
title: "Guida introduttiva: Creare un'app Python in Linux"
description: Introduzione all'uso delle app Linux nel servizio app di Azure distribuendo la prima app Python in un contenitore Linux nel servizio app.
ms.topic: quickstart
ms.date: 10/22/2019
ms.custom: seo-python-october2019
experimental: false
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 67fbffbe96bc32b6ec38fa75c1e754c7f11d38d6
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687476"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Guida introduttiva: Creare un'app Python nel Servizio app di Azure in Linux

In questa esercitazione dell'avvio rapido si distribuirà una semplice app Web Python nel [Servizio app in Linux](app-service-linux-intro.md), il servizio di hosting Web con scalabilità elevata e applicazione automatica di patch. Si userà l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) locale in un computer Mac, Linux o Windows. L'app Web configurata usa un livello di servizio app Gratuito, affinché l'esecuzione delle procedure in questo articolo non comporti l'addebito di costi.

Se si preferisce distribuire le app tramite un IDE, vedere [Distribuire app Python nel Servizio app da Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (è supportato anche Python 3.6)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Interfaccia della riga di comando di Azure</a>

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra del terminale eseguire il comando seguente per clonare l'applicazione di esempio nel computer locale. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Passare quindi alla cartella:

```terminal
cd python-docs-hello-world
```

Il repository contiene un file *application.py*, che indica al Servizio app che il codice contiene un'app Flask. Per altre informazioni, vedere [Processo di avvio di contenitore e personalizzazioni](how-to-configure-python.md).

## <a name="run-the-sample"></a>Eseguire l'esempio

In una finestra del terminale usare i comandi seguenti (a seconda del sistema operativo in uso) per installare le dipendenze necessarie e avviare il server di sviluppo predefinito. 

# <a name="bashtabbash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py
flask run
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Aprire un Web browser e passare all'app di esempio all'indirizzo `http://localhost:5000/`. L'app visualizza il messaggio **Hello World!** .

![Eseguire un'app Python di esempio in locale](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

Nella finestra del terminale premere **CTRL**+**C** per uscire dal server Web.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

L'interfaccia della riga di comando di Azure offre molti comandi pratici che è possibile usare da un terminale locale per effettuare il provisioning e gestire le risorse di Azure dalla riga di comando. È possibile utilizzare i comandi per completare le stesse attività eseguite tramite il portale di Azure in un browser. È anche possibile usare i comandi dell'interfaccia della riga di comando negli script per automatizzare i processi di gestione.

Per eseguire i comandi di Azure nell'interfaccia della riga di comando di Azure, è prima necessario eseguire l'accesso usando il comando `az login`. Questo comando apre un browser per raccogliere le credenziali.

```terminal
az login
```

## <a name="deploy-the-sample"></a>Distribuire l'esempio

Il comando [`az webapp up`](/cli/azure/webapp#az-webapp-up) crea l'app Web nel Servizio app e distribuisce il codice.

Nella cartella *python-docs-hello-world* che contiene il codice di esempio eseguire il comando `az webapp up` seguente. Sostituire `<app-name>` con un nome di app univoco globale (*i caratteri validi sono `a-z`, `0-9` e `-`* ). Sostituire anche `<location-name>` con un'area di Azure, ad esempio **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia** e così via. Per recuperare un elenco di aree consentite per l'account Azure, è possibile eseguire il comando [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations).


```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Passare all'app

Passare all'applicazione distribuita nel Web browser all'URL `http://<app-name>.azurewebsites.net`.

Il codice di esempio Python esegue un contenitore Linux nel Servizio app usando un'immagine predefinita.

![Eseguire un'app Python di esempio in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Congratulazioni** La distribuzione dell'app Python nel Servizio app di Azure in Linux è stata completata.

## <a name="redeploy-updates"></a>Ridistribuire gli aggiornamenti

Nell'editor di codice preferito aprire *application.py* e modificare l'istruzione `return` sull'ultima riga in modo che corrisponda al codice seguente. L'istruzione `print` è inclusa qui per generare l'output di registrazione da usare nella sezione successiva. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Salvare le modifiche e uscire dall'editor. 

Ridistribuire l'app con il comando `az webapp up` seguente, usando lo stesso comando impiegato per distribuire l'app la prima volta, sostituendo `<app-name>` e `<location-name>` con gli stessi nomi usati in precedenza. 

```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Al termine della distribuzione, tornare alla finestra del browser aperta per `http://<app-name>.azurewebsites.net` e aggiornare la pagina per visualizzare il messaggio modificato:

![Eseguire un'app Python di esempio aggiornata in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code offre estensioni potenti per Python e il Servizio app di Azure, al fine di semplificare il processo di distribuzione delle app Web Python nel Servizio app. Per altre informazioni, vedere [Distribuire app Python nel Servizio app di Azure in Linux da Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Eseguire lo streaming dei log

È possibile accedere ai log della console generati dall'interno dell'app e del contenitore in cui è in esecuzione. I log includono tutti gli output generati tramite le istruzioni `print`.

In primo luogo, attivare la registrazione del contenitore eseguendo il comando seguente in un terminale, sostituendo `<app-name>` con il nome dell'app e `<resource-group-name>` con il nome del gruppo di risorse visualizzato nell'output del comando `az webapp up` usato (ad esempio "appsvc_rg_Linux_centralus"):

```terminal
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Dopo aver attivato la registrazione del contenitore, eseguire il comando seguente per visualizzare il flusso di registrazione:

```terminal
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Aggiornare l'app nel browser per generare i log della console, che includeranno righe simili al testo seguente. Se l'output non viene visualizzato immediatamente, riprovare dopo 30 secondi.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

È anche possibile esaminare i file di log nel browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Per interrompere lo streaming dei log in qualsiasi momento, digitare `Ctrl`+`C`.

## <a name="manage-the-azure-app"></a>Gestire l'app Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> per gestire l'app creata. Cercare e selezionare **Servizi app**.

![Passare a Servizi app nel portale di Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selezionare il nome dell'app Azure.

![Nel portale di Azure passare all'app Python in Servizi app](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Verrà visualizzata la pagina Panoramica dell'app. Qui è possibile eseguire attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione dell'app.

![Gestire l'app Python nella pagina Panoramica del portale di Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Nel menu del Servizio app sono disponibili varie pagine per la configurazione dell'app.

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Il gruppo di risorse ha un nome simile a "appsvc_rg_Linux_CentralUS", a seconda della località scelta. Se si usa uno SKU del Servizio app diverso dal livello F1 gratuito, per queste risorse verranno addebitati costi su base continuativa.

Se non si prevede di usare queste risorse in futuro, eliminare il gruppo di risorse con il comando seguente, sostituendo `<resource-group-name>` con il gruppo di risorse riportato nell'output del comando `az webapp up`, ad esempio "appsvc_rg_Linux_centralus". L'esecuzione del comando può richiedere un minuto.

```terminal
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Web Python (Django) con PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurare un'app Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire un'app Python in un contenitore personalizzato](tutorial-custom-docker-image.md)
