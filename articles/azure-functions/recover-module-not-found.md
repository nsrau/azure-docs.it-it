---
title: Risolvere i problemi di ModuleNotFoundError Python in funzioni di Azure
description: Informazioni su come risolvere gli errori del modulo funzioni di Azure non trovati nelle funzioni di Python.
author: Hazhzeng
ms.topic: article
ms.date: 05/12/2020
ms.author: hazeng
ms.custom: tracking-python
ms.openlocfilehash: b2582caf407b3983b32c40482fa0f0275f00fb8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84554748"
---
# <a name="troubleshoot-python-module-errors-in-azure-functions"></a>Risolvere gli errori del modulo Python in funzioni di Azure

Questo articolo consente di risolvere gli errori relativi ai moduli nell'app per le funzioni Python. Questi errori generano in genere il messaggio di errore di funzioni di Azure seguente:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Questo errore si verifica quando un'app per le funzioni Python non riesce a caricare un modulo Python. La causa principale di questo errore è rappresentata da uno dei problemi seguenti:

- [Impossibile trovare il pacchetto](#the-package-cant-be-found)
- [Il pacchetto non viene risolto con la rotellina Linux corretta](#the-package-isnt-resolved-with-proper-linux-wheel)
- [Il pacchetto non è compatibile con la versione dell'interprete Python](#the-package-is-incompatible-with-the-python-interpreter-version)
- [Il pacchetto è in conflitto con altri pacchetti](#the-package-conflicts-with-other-packages)
- [Il pacchetto supporta solo piattaforme Windows o macOS](#the-package-only-supports-windows-or-macos-platforms)

## <a name="view-project-files"></a>Visualizzare i file di progetto

Per identificare la causa effettiva del problema, è necessario ottenere i file di progetto Python eseguiti nell'app per le funzioni. Se non si dispone dei file di progetto nel computer locale, è possibile ottenerli in uno dei modi seguenti:

- Se l'app per le funzioni ha `WEBSITE_RUN_FROM_PACKAGE` un'impostazione dell'app e il relativo valore è un URL, scaricare il file copiando e incollare l'URL nel browser.
- Se l'app per le funzioni ha `WEBSITE_RUN_FROM_PACKAGE` e è impostata su `1` , passare a `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` e scaricare il file dall'URL più recente `href` .
- Se l'app per le funzioni non ha l'impostazione dell'app menzionata in precedenza, passare a `https://<app-name>.scm.azurewebsites.net/api/settings` e trovare l'URL in `SCM_RUN_FROM_PACKAGE` . Scaricare il file copiando e incollando l'URL nel browser.
- Se nessuna di queste funzioni, passare a `https://<app-name>.scm.azurewebsites.net/DebugConsole` e rivelare il contenuto sotto `/home/site/wwwroot` .

Il resto di questo articolo consente di risolvere le possibili cause di questo errore esaminando il contenuto dell'app per le funzioni, identificando la causa principale e risolvendo il problema specifico.

## <a name="diagnose-modulenotfounderror"></a>Diagnosticare ModuleNotFoundError

In questa sezione vengono illustrate in dettaglio le potenziali cause principali degli errori correlati al modulo. Una volta calcolata la causa principale probabile, è possibile passare alla mitigazione correlata.

### <a name="the-package-cant-be-found"></a>Impossibile trovare il pacchetto

Individuare `.python_packages/lib/python3.6/site-packages/<package-name>` o `.python_packages/lib/site-packages/<package-name>` . Se il percorso del file non esiste, il percorso mancante è probabilmente la causa principale.

Questo problema può essere causato dall'utilizzo di strumenti di terze parti o obsoleti durante la distribuzione.

Vedere [abilitare la compilazione remota](#enable-remote-build) o [creare dipendenze native](#build-native-dependencies) per la mitigazione.

### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>Il pacchetto non viene risolto con la rotellina Linux corretta

Passare a `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` o `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Usare l'editor di testo preferito per aprire il file della **rotellina** e selezionare la sezione **Tag:** . Se il valore del tag non contiene **Linux**, potrebbe trattarsi del problema.

Funzioni Python eseguite solo in Linux in Azure: il runtime di funzioni V2. x viene eseguito in Debian stretch e nel runtime V3. x in Debian Buster. L'artefatto dovrebbe contenere i file binari Linux corretti. L'uso di `--build local` flag negli strumenti di base, in strumenti di terze parti o obsoleti può causare l'uso di file binari meno recenti.

Vedere [abilitare la compilazione remota](#enable-remote-build) o [creare dipendenze native](#build-native-dependencies) per la mitigazione.

### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>Il pacchetto non è compatibile con la versione dell'interprete Python

Passare a `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` o `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Utilizzando un editor di testo, aprire il file di metadati e controllare la sezione **classificatori:** . Se la sezione non contiene `Python :: 3` , `Python :: 3.6` , `Python :: 3.7` o `Python :: 3.8` , significa che la versione del pacchetto è troppo vecchia o molto probabilmente il pacchetto è già fuori dalla manutenzione.

È possibile controllare la versione di Python dell'app per le funzioni dalla [portale di Azure](https://portal.azure.com). Passare all'app per le funzioni, scegliere **Esplora inventario risorse**e selezionare **Vai**.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Aprire il Esplora inventario risorse per l'app per le funzioni nel portale":::

Al termine del caricamento di Esplora risorse, cercare **LinuxFxVersion**, che mostra la versione di Python.

Vedere [aggiornare il pacchetto alla versione più recente](#update-your-package-to-the-latest-version) o [sostituire il pacchetto con gli equivalenti](#replace-the-package-with-equivalents) per la mitigazione.

### <a name="the-package-conflicts-with-other-packages"></a>Il pacchetto è in conflitto con altri pacchetti

Se è stato verificato che il pacchetto è stato risolto correttamente con le ruote Linux corrette, potrebbe verificarsi un conflitto con altri pacchetti. In alcuni pacchetti i documenti PyPi possono chiarire i moduli incompatibili. Ad esempio [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) , in è presente un'istruzione come segue:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

È possibile trovare la documentazione per la versione del pacchetto in `https://pypi.org/project/<package-name>/<package-version>` .

Vedere [aggiornare il pacchetto alla versione più recente](#update-your-package-to-the-latest-version) o [sostituire il pacchetto con gli equivalenti](#replace-the-package-with-equivalents) per la mitigazione.

### <a name="the-package-only-supports-windows-or-macos-platforms"></a>Il pacchetto supporta solo piattaforme Windows o macOS

Aprire `requirements.txt` con un editor di testo e verificare il pacchetto in `https://pypi.org/project/<package-name>` . Alcuni pacchetti vengono eseguiti solo su piattaforme Windows o macOS. Ad esempio, PyWin32 viene eseguito solo in Windows.

`Module Not Found`È possibile che l'errore non si verifichi quando si usa Windows o MacOS per lo sviluppo locale. Tuttavia, il pacchetto non viene importato in funzioni di Azure, che usa Linux in fase di esecuzione. Questo problema può essere causato dall'uso `pip freeze` di per esportare l'ambiente virtuale in requirements.txt dal computer Windows o MacOS durante l'inizializzazione del progetto.

Per la mitigazione, vedere [sostituire il pacchetto con gli equivalenti](#replace-the-package-with-equivalents) o il [requirements.txthandcraft](#handcraft-requirementstxt) .

## <a name="mitigate-modulenotfounderror"></a>Attenuare ModuleNotFoundError

Di seguito sono riportate le possibili mitigazioni per i problemi relativi ai moduli. Usare le [diagnosi precedenti](#diagnose-modulenotfounderror) per determinare quali di queste attenuazioni provare.

### <a name="enable-remote-build"></a>Abilita compilazione remota

Assicurarsi che la compilazione remota sia abilitata. Il modo in cui si esegue questa operazione dipende dal metodo di distribuzione.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
Verificare che sia installata la versione più recente dell' [estensione funzioni di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) . Verificare che `.vscode/settings.json` esista e che contenga l'impostazione `"azureFunctions.scmDoBuildDuringDeployment": true` . In caso contrario, creare il file con l' `azureFunctions.scmDoBuildDuringDeployment` impostazione abilitata e ridistribuire il progetto.

# <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

Verificare che sia installata la versione più recente di [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) . Passare alla cartella del progetto di funzione locale e usare `func azure functionapp publish <app-name>` per la distribuzione.

# <a name="manual-publishing"></a>[Pubblicazione manuale](#tab/manual)

Se si sta pubblicando manualmente il pacchetto nell' `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` endpoint, assicurarsi che sia **SCM_DO_BUILD_DURING_DEPLOYMENT** che **ENABLE_ORYX_BUILD** siano impostati su **true**. Per altre informazioni, vedere [come usare le impostazioni dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings).

---

### <a name="build-native-dependencies"></a>Compila dipendenze Native

Assicurarsi che sia installata la versione più recente di **Docker** e [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) . Passare alla cartella del progetto di funzione locale e usare `func azure functionapp publish <app-name> --build-native-deps` per la distribuzione.

### <a name="update-your-package-to-the-latest-version"></a>Aggiornare il pacchetto alla versione più recente

Esplorare la versione più recente del pacchetto in `https://pypi.org/project/<package-name>` e controllare la sezione **classificatori:** . Il pacchetto deve essere `OS Independent` compatibile con `POSIX` o `POSIX :: Linux` nel **sistema operativo**. Inoltre, il linguaggio di programmazione deve contenere `Python :: 3` ,, `Python :: 3.6` `Python :: 3.7` o `Python :: 3.8` .

Se questi sono corretti, è possibile aggiornare il pacchetto alla versione più recente cambiando la riga `<package-name>~=<latest-version>` in requirements.txt.

### <a name="handcraft-requirementstxt"></a>requirements.txt handcraft

Alcuni sviluppatori usano `pip freeze > requirements.txt` per generare l'elenco di pacchetti Python per gli ambienti di sviluppo. Anche se questa praticità dovrebbe funzionare nella maggior parte dei casi, è possibile che si verifichino problemi negli scenari di distribuzione multipiattaforma, ad esempio lo sviluppo di funzioni in locale in Windows o macOS, ma la pubblicazione in un'app per le funzioni, che viene eseguita in Linux. In questo scenario, `pip freeze` può presentare dipendenze o dipendenze specifiche del sistema operativo per l'ambiente di sviluppo locale. Queste dipendenze possono interrompere l'app per le funzioni Python durante l'esecuzione in Linux.

La procedura consigliata consiste nel controllare l'istruzione import da ogni file con estensione PY nel codice sorgente del progetto e archiviare tali moduli solo in requirements.txt file. Ciò garantisce che la risoluzione dei pacchetti possa essere gestita correttamente su sistemi operativi diversi.

### <a name="replace-the-package-with-equivalents"></a>Sostituire il pacchetto con gli equivalenti

Prima di tutto, è opportuno esaminare la versione più recente del pacchetto in `https://pypi.org/project/<package-name>` . In genere, questo pacchetto ha una pagina di GitHub personalizzata, passare alla sezione **problemi** su GitHub e cercare se il problema è stato risolto. In tal caso, aggiornare il pacchetto alla versione più recente.

In alcuni casi, il pacchetto potrebbe essere stato integrato nella [libreria standard Python](https://docs.python.org/3/library/) (ad esempio pathlib). In tal caso, poiché viene fornita una certa distribuzione Python in funzioni di Azure (Python 3,6, Python 3,7 e Python 3,8), il pacchetto nel requirements.txt deve essere rimosso.

Tuttavia, se si è verificato un problema che non è stato risolto e si sta per scadere. Si consiglia di effettuare alcune ricerche e trovare un pacchetto simile per il progetto. In genere, la community di Python fornirà un'ampia gamma di librerie simili che è possibile usare.

## <a name="next-steps"></a>Passaggi successivi

Se non si riesce a risolvere il problema correlato al modulo, segnalarlo al team Functions:

> [!div class="nextstepaction"]
> [Segnala un problema non risolto](https://github.com/Azure/azure-functions-python-worker/issues)
