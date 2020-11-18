---
title: "Avvio rapido: Creare un'app Python"
description: Introduzione all'uso del Servizio app di Azure distribuendo la prima app Python in un contenitore Linux nel servizio app.
ms.topic: quickstart
ms.date: 11/10/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
zone_pivot_groups: python-frameworks-01
adobe-target: true
ms.openlocfilehash: b32977ac1c8cfe0c461bcd1628c08a0ca215ba93
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506193"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Avvio rapido: Creare un'app Python nel Servizio app di Azure in Linux

In questa esercitazione dell'avvio rapido si distribuirà una semplice app Web Python nel [Servizio app in Linux](overview.md#app-service-on-linux), il servizio di hosting Web con scalabilità elevata e applicazione automatica di patch. Si usa l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) locale in computer Mac, Linux o Windows per distribuire un esempio con il framework Flask o Django. L'app Web configurata usa un livello di servizio app Gratuito, affinché l'esecuzione delle procedure in questo articolo non comporti l'addebito di costi.

> [!TIP]
> Se invece si preferisce usare Visual Studio Code, seguire la **[guida di avvio rapido sul servizio app in Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)** .

## <a name="set-up-your-initial-environment"></a>Configurare l'ambiente iniziale

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

Problemi? [Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clone-the-sample"></a>Clonare l'esempio

Clonare il repository di esempi usando il comando seguente, quindi passare alla relativa cartella. [Installare Git](https://git-scm.com/downloads) se non si è già presente.

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Quindi passare a tale cartella:

```terminal
cd python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```

Quindi passare a tale cartella:

```terminal
cd python-docs-hello-django
```
::: zone-end

L'esempio contiene codice specifico del framework che il servizio app di Azure riconosce all'avvio dell'app. Per altre informazioni, vedere [Processo di avvio del contenitore](configure-language-python.md#container-startup-process).

Problemi? [Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="run-the-sample"></a>Eseguire l'esempio

::: zone pivot="python-framework-flask"
1. Assicurarsi di trovarsi nella cartella *python-docs-hello-world*. 

1. Creare un ambiente virtuale e installare le dipendenze:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Se si verifica l'errore "[Errno 2] Impossibile trovare il file o la directory: 'requirements.txt'", assicurarsi di trovarsi nella cartella *python-docs-hello-world*.

1. Eseguire il server di sviluppo.

    ```terminal  
    flask run
    ```
    
    Per impostazione predefinita, il server presuppone che il modulo di ingresso dell'app si trovi in *app.py*, come usato nell'esempio. Se si usa un nome di modulo diverso, impostare la variabile di ambiente `FLASK_APP` su tale nome.

1. Aprire un Web browser e passare all'app di esempio all'indirizzo `http://localhost:5000/`. L'app visualizza il messaggio **Hello World!** .

    ![Eseguire un'app Python di esempio in locale](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Nella finestra del terminale premere **CTRL**+**C** per uscire dal server di sviluppo.
::: zone-end

::: zone pivot="python-framework-django"
1. Assicurarsi di trovarsi nella cartella *python-docs-hello-django*. 

1. Creare un ambiente virtuale e installare le dipendenze:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Se si verifica l'errore "[Errno 2] Impossibile trovare il file o la directory: 'requirements.txt'.", assicurarsi di trovarsi nella cartella *python-docs-hello-django*.
    
1. Eseguire il server di sviluppo.

    ```terminal
    python manage.py runserver
    ```

1. Aprire un Web browser e passare all'app di esempio all'indirizzo `http://localhost:8000/`. L'app visualizza il messaggio **Hello World!** .

    ![Eseguire un'app Python di esempio in locale](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Nella finestra del terminale premere **CTRL**+**C** per uscire dal server di sviluppo.
::: zone-end

Problemi? [Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="deploy-the-sample"></a>Distribuire l'esempio

Distribuire il codice nella cartella locale (*python-docs-hello-world*) usando il comando `az webapp up`:

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Se il comando `az` non viene riconosciuto, verificare di aver installato l'interfaccia della riga di comando di Azure come descritto in [Configurare l'ambiente iniziale](#set-up-your-initial-environment).
- Se il comando `webapp` non viene riconosciuto, assicurarsi che la versione dell'interfaccia della riga di comando di Azure sia 2.0.80 o successiva. Se non lo è, [installare l'ultima versione](/cli/azure/install-azure-cli).
- Sostituire `<app_name>` con un nome univoco nell'ambito di Azure (*i caratteri validi sono `a-z`, `0-9` e `-`* ). Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.
- Con l'argomento `--sku F1` l'app Web viene creata nel piano tariffario Gratuito. Omettere questo argomento per usare un livello Premium più rapido, che però comporta un costo orario.
- Facoltativamente, è possibile includere l'argomento `--location <location-name>`, dove `<location_name>` è un'area di Azure disponibile. Per recuperare un elenco di aree consentite per l'account Azure, è possibile eseguire il comando [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations).
- Se viene visualizzato un messaggio di errore analogo a "Non è stato possibile rilevare automaticamente lo stack di runtime dell'app", assicurarsi che il comando venga eseguito nella cartella *python-docs-hello-world* (Flask) o *python-docs-hello-django* (Django) che contiene il file *requirements.txt*. Vedere l'articolo sulla [risoluzione dei problemi di rilevamento automatico con az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub).

Il completamento del comando può richiedere alcuni minuti. Durante l'esecuzione, vengono visualizzati messaggi sulla creazione del gruppo di risorse, sul piano di servizio app e l'app di hosting, la configurazione della registrazione e quindi la distribuzione dello ZIP. Viene quindi visualizzato il messaggio che indica che è possibile avviare l'app all'indirizzo http://&lt;nome-app&gt;.azurewebsites.net, ovvero l'URL dell'app in Azure.

![Output di esempio del comando az webapp up](./media/quickstart-python/az-webapp-up-output.png)

Problemi? Per prima cosa, vedere la [guida alla risoluzione dei problemi](configure-language-python.md#troubleshooting). Se i problemi persistono, [segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Passare all'app

Passare all'applicazione distribuita nel Web browser all'URL `http://<app-name>.azurewebsites.net`. L'avvio dell'app potrebbe richiedere uno o due minuti, quindi se viene visualizzata una pagina dell'app predefinita, attendere un minuto e aggiornare il browser.

Il codice di esempio Python esegue un contenitore Linux nel Servizio app usando un'immagine predefinita.

![Eseguire un'app Python di esempio in Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Congratulazioni** La distribuzione dell'app Python nel Servizio app di Azure è stata completata.

Problemi? Per prima cosa, vedere la [guida alla risoluzione dei problemi](configure-language-python.md#troubleshooting). Se i problemi persistono, [segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="redeploy-updates"></a>Ridistribuire gli aggiornamenti

In questa sezione apportare una piccola modifica al codice e quindi ridistribuirlo in Azure. La modifica del codice include un'istruzione `print` per generare l'output di registrazione da usare nella sezione successiva.

::: zone pivot="python-framework-flask"
Aprire *app.py* in un editor e aggiornare la funzione `hello` in modo che corrisponda al codice seguente. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Aprire *hello/views.py* in un editor e aggiornare la funzione `hello` in modo che corrisponda al codice seguente.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Salvare le modifiche, quindi ridistribuire l'app usando di nuovo il comando `az webapp up`:

```azurecli
az webapp up
```

Questo comando usa i valori memorizzati nella cache in locale nel file *.azure/config*, inclusi il nome dell'app, il gruppo di risorse e il piano di servizio app.

Al termine della distribuzione, tornare alla finestra del browser aperta su `http://<app-name>.azurewebsites.net`. Aggiornare la pagina, che dovrebbe visualizzare il messaggio modificato:

![Eseguire un'app Python di esempio aggiornata in Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

Problemi? Per prima cosa, vedere la [guida alla risoluzione dei problemi](configure-language-python.md#troubleshooting). Se i problemi persistono, [segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

> [!TIP]
> Visual Studio Code offre estensioni potenti per Python e il Servizio app di Azure, al fine di semplificare il processo di distribuzione delle app Web Python nel Servizio app. Per altre informazioni, vedere [Distribuire app Python nel Servizio app di Azure in Linux da Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Eseguire lo streaming dei log

È possibile accedere ai log della console generati dall'interno dell'app e del contenitore in cui è in esecuzione. I log includono tutti gli output generati tramite le istruzioni `print`.

Per trasmettere in streaming i log, eseguire il comando [az webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail):

```azurecli
az webapp log tail
```

È anche possibile includere il parametro `--logs` con il comando `az webapp up` per aprire automaticamente il flusso di log durante la distribuzione.

Aggiornare l'app nel browser per generare i log della console, che includeranno messaggi che descrivono le richieste HTTP per l'app. Se non viene visualizzato immediatamente un output, riprovare dopo 30 secondi.

È anche possibile esaminare i file di log nel browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Per interrompere lo streaming di log in qualsiasi momento, premere **CTRL**+**C** nel terminale.

Problemi? [Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="manage-the-azure-app"></a>Gestire l'app Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> per gestire l'app creata. Cercare e selezionare **Servizi app**.

![Passare a Servizi app nel portale di Azure](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Selezionare il nome dell'app di Azure.

![Nel portale di Azure passare all'app Python in Servizi app](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Selezionando l'app viene aperta la pagina **Panoramica**, in cui è possibile eseguire attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione.

![Gestire l'app Python nella pagina Panoramica del portale di Azure](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Nel menu del Servizio app sono disponibili varie pagine per la configurazione dell'app.

Problemi? Per prima cosa, vedere la [guida alla risoluzione dei problemi](configure-language-python.md#troubleshooting). Se i problemi persistono, [segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Il gruppo di risorse ha un nome simile a "appsvc_rg_Linux_CentralUS", a seconda della località scelta. Se si usa uno SKU del servizio app diverso dal livello F1 gratuito, per queste risorse verranno addebitati costi su base continuativa. Vedere [Prezzi di Servizio app](https://azure.microsoft.com/pricing/details/app-service/linux/).

Se non si prevede di usare queste risorse in futuro, eliminare i gruppi di risorse eseguendo questo comando:

```azurecli
az group delete --no-wait
```

Il comando usa il nome del gruppo di risorse memorizzato nella cache nel file *.azure/config*.

Con l'argomento `--no-wait`, il comando restituisce il risultato prima del completamento dell'operazione.

Problemi? [Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Web Python (Django) con PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurare un'app Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Aggiungere l'accesso utente a un'app Web Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire un'app Python in un contenitore personalizzato](tutorial-custom-container.md)
