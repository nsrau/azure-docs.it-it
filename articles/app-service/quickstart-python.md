---
title: "Avvio rapido: Creare un'app Python"
description: Introduzione all'uso del Servizio app di Azure distribuendo la prima app Python in un contenitore Linux nel servizio app.
ms.topic: quickstart
ms.date: 06/30/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
ms.openlocfilehash: 3bfe927b2c9dc16de8712f6c1793e850df92c201
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078453"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Avvio rapido: Creare un'app Python nel Servizio app di Azure in Linux

In questa esercitazione dell'avvio rapido si distribuirà una semplice app Web Python nel [Servizio app in Linux](overview.md#app-service-on-linux), il servizio di hosting Web con scalabilità elevata e applicazione automatica di patch. Si userà l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) locale in un computer Mac, Linux o Windows. L'app Web configurata usa un livello di servizio app Gratuito, affinché l'esecuzione delle procedure in questo articolo non comporti l'addebito di costi.

Se si preferisce distribuire le app tramite un IDE, vedere [Distribuire app Python nel Servizio app da Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01).

## <a name="set-up-your-initial-environment"></a>Configurare l'ambiente iniziale

Per eseguire le procedure descritte è necessario:

1. Avere a disposizione un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installare <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 o versioni successive</a>.
1. Installare l'<a href="/cli/azure/install-azure-cli" target="_blank">interfaccia della riga di comando di Azure</a> 2.0.80 o versione successiva, con cui si eseguono i comandi in qualsiasi shell per il provisioning e la configurazione delle risorse di Azure.

Aprire una finestra terminale e verificare che la versione di Python sia 3.6 o successiva:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Verificare anche che la versione dell'interfaccia della riga di comando di Azure sia 2.0.80 o successiva:

```azurecli
az --version
```

Quindi accedere ad Azure tramite l'interfaccia della riga di comando:

```azurecli
az login
```

Questo comando apre un browser per raccogliere le credenziali. Al termine dell'esecuzione del comando, viene visualizzato un output JSON contenente informazioni sulle sottoscrizioni.

Dopo aver eseguito l'accesso, è possibile eseguire i comandi di Azure con l'interfaccia della riga di comando di Azure per usare le risorse nella sottoscrizione.

[Problemi? Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clone-the-sample"></a>Clonare l'esempio

Clonare il repository di esempio usando il comando seguente. [Installare Git](https://git-scm.com/downloads) se non si è già presente.

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Passare quindi alla cartella:

```terminal
cd python-docs-hello-world
```

Il codice di esempio contiene un file *application.py*, che indica al servizio app che il codice contiene un'app Flask. Per altre informazioni, vedere [Processo di avvio del contenitore](configure-language-python.md#container-startup-process).

[Problemi? Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="run-the-sample"></a>Eseguire l'esempio

# <a name="bash"></a>[Bash](#tab/bash)

Creare innanzitutto un ambiente virtuale e installare le dipendenze:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Impostare quindi la variabile di ambiente `FLASK_APP` sul modulo entry dell'app ed eseguire il server di sviluppo Flask:

```
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Creare innanzitutto un ambiente virtuale e installare le dipendenze:

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Impostare quindi la variabile di ambiente `FLASK_APP` sul modulo entry dell'app ed eseguire il server di sviluppo Flask:

```powershell
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

Creare innanzitutto un ambiente virtuale e installare le dipendenze:

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Impostare quindi la variabile di ambiente `FLASK_APP` sul modulo entry dell'app ed eseguire il server di sviluppo Flask:

```cmd
SET FLASK_APP=application.py
flask run
```

---

Aprire un Web browser e passare all'app di esempio all'indirizzo `http://localhost:5000/`. L'app visualizza il messaggio **Hello World!** .

![Eseguire un'app Python di esempio in locale](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Nella finestra terminale premere **CTRL**+**C** per uscire dal server di sviluppo Flask.

[Problemi? Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="deploy-the-sample"></a>Distribuire l'esempio

Distribuire il codice nella cartella locale (*python-docs-hello-world*) usando il comando `az webapp up`:

```azurecli
az webapp up --sku F1 -n <app-name>
```

- Se il comando `az` non è riconosciuto, verificare che sia installata l'interfaccia della riga di comando di Azure come descritto in [Configurare l'ambiente iniziale](#set-up-your-initial-environment).
- Sostituire `<app_name>` con un nome univoco nell'ambito di Azure (*i caratteri validi sono `a-z`, `0-9` e `-`* ). Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.
- Con l'argomento `--sku F1` l'app Web viene creata nel piano tariffario Gratuito. Omettere questo argomento per usare un livello Premium più rapido, che però comporta un costo orario.
- È possibile facoltativamente includere l'argomento `-l <location-name>` in cui `<location_name>` è un'area di Azure, come **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia** e così via. Per recuperare un elenco di aree consentite per l'account Azure, è possibile eseguire il comando [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations).
- Se viene visualizzato un messaggio di errore analogo a "Non è stato possibile rilevare automaticamente lo stack di runtime dell'app", assicurarsi che il comando venga eseguito nella cartella *python-docs-hello-world* che contiene il file *requirements.txt*. Vedere l'articolo sulla [risoluzione dei problemi di rilevamento automatico con az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub).

Il completamento del comando può richiedere alcuni minuti. Durante l'esecuzione, vengono visualizzati messaggi sulla creazione del gruppo di risorse, sul piano di servizio app e l'app di hosting, la configurazione della registrazione e quindi la distribuzione dello ZIP. Viene quindi visualizzato il messaggio che indica che è possibile avviare l'app all'indirizzo http://&lt;nome-app&gt;.azurewebsites.net, ovvero l'URL dell'app in Azure.

![Output di esempio del comando az webapp up](./media/quickstart-python/az-webapp-up-output.png)

[Problemi? Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

[!INCLUDE [AZ Webapp Up Note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Passare all'app

Passare all'applicazione distribuita nel Web browser all'URL `http://<app-name>.azurewebsites.net`.

Il codice di esempio Python esegue un contenitore Linux nel Servizio app usando un'immagine predefinita.

![Eseguire un'app Python di esempio in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Congratulazioni** La distribuzione dell'app Python nel Servizio app di Azure è stata completata.

[Problemi? Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="redeploy-updates"></a>Ridistribuire gli aggiornamenti

Nell'editor di codice preferito aprire *application.py* e aggiornare la funzione `hello` come indicato di seguito. Con questa modifica viene aggiunta un'istruzione `print` per generare l'output di registrazione da usare nella sezione successiva. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Salvare le modifiche e uscire dall'editor. 

Ridistribuire l'app usando di nuovo il comando `az webapp up`:

```azurecli
az webapp up
```

Questo comando usa i valori memorizzati nella cache in locale nel file *.azure/config*, inclusi il nome dell'app, il gruppo di risorse e il piano di servizio app.

Al termine della distribuzione, tornare alla finestra del browser aperta su `http://<app-name>.azurewebsites.net`. Aggiornare la pagina, che dovrebbe visualizzare il messaggio modificato:

![Eseguire un'app Python di esempio aggiornata in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Problemi? Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

> [!TIP]
> Visual Studio Code offre estensioni potenti per Python e il Servizio app di Azure, al fine di semplificare il processo di distribuzione delle app Web Python nel Servizio app. Per altre informazioni, vedere [Distribuire app Python nel Servizio app di Azure in Linux da Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Eseguire lo streaming dei log

È possibile accedere ai log della console generati dall'interno dell'app e del contenitore in cui è in esecuzione. I log includono tutti gli output generati tramite le istruzioni `print`.

Per eseguire lo streaming dei log, usare il comando seguente:

```azurecli
az webapp log tail
```

Aggiornare l'app nel browser per generare i log della console, che includeranno messaggi che descrivono le richieste HTTP per l'app. Se non viene visualizzato immediatamente un output, riprovare dopo 30 secondi.

È anche possibile esaminare i file di log nel browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Per interrompere lo streaming dei log in qualsiasi momento, premere **CTRL**+**C**.

[Problemi? Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="manage-the-azure-app"></a>Gestire l'app Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> per gestire l'app creata. Cercare e selezionare **Servizi app**.

![Passare a Servizi app nel portale di Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selezionare il nome dell'app di Azure.

![Nel portale di Azure passare all'app Python in Servizi app](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Selezionando l'app viene aperta la pagina **Panoramica**, in cui è possibile eseguire attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione.

![Gestire l'app Python nella pagina Panoramica del portale di Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Nel menu del Servizio app sono disponibili varie pagine per la configurazione dell'app.

[Problemi? Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Il gruppo di risorse ha un nome simile a "appsvc_rg_Linux_CentralUS", a seconda della località scelta. Se si usa uno SKU del servizio app diverso dal livello F1 gratuito, per queste risorse verranno addebitati costi su base continuativa. Vedere [Prezzi di Servizio app](https://azure.microsoft.com/pricing/details/app-service/linux/).

Se non si prevede di usare queste risorse in futuro, eliminare i gruppi di risorse eseguendo questo comando:

```azurecli
az group delete
```

Il comando usa il nome del gruppo di risorse memorizzato nella cache nel file *.azure/config*.

L'esecuzione del comando può richiedere un minuto.

[Problemi? Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Web Python (Django) con PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Aggiungere l'accesso utente a un'app Web Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Configurare un'app Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire un'app Python in un contenitore personalizzato](tutorial-custom-container.md)
