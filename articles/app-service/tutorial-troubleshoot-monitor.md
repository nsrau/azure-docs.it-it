---
title: 'Esercitazione: Risolvere i problemi con Monitoraggio di Azure'
description: Informazioni su come Monitoraggio di Azure e Log Analytics consentono di monitorare un'app Web del servizio app. Monitoraggio di Azure massimizza la disponibilità tramite la distribuzione di una soluzione completa per il monitoraggio degli ambienti.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: 106427a6b26386e6ff881862f836e9108a27aa96
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082330"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Esercitazione: Risolvere i problemi di un'app del servizio app con Monitoraggio di Azure

> [!NOTE]
> L'integrazione di Monitoraggio di Azure con il Servizio app è in [anteprima](https://aka.ms/appsvcblog-azmon).
>

Questa esercitazione illustra come risolvere i problemi di un'app del [servizio app](overview.md) con [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview). L'app di esempio include il codice destinato a esaurire la memoria e a causare errori HTTP 500, in modo da poter diagnosticare e risolvere il problema con Monitoraggio di Azure. Al termine, si avrà un'app di esempio in esecuzione nel Servizio app in Linux integrata con [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview).

[Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) ottimizza la disponibilità e le prestazioni delle applicazioni e dei servizi in uso offrendo una soluzione completa per raccogliere e analizzare la telemetria e intervenire di conseguenza dal cloud e dagli ambienti locali.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un'app Web con Monitoraggio di Azure
> * Inviare i log della console a Log Analytics
> * Usare le query su log per identificare e risolvere i problemi relativi agli errori delle app Web

I passaggi illustrati in questa esercitazione possono essere eseguiti in macOS, Linux e Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

- [Sottoscrizione di Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Creare le risorse di Azure

Prima di tutto, si eseguono diversi comandi localmente per configurare un'app di esempio da usare con questa esercitazione. I comandi clonano un'app di esempio, creano le risorse di Azure, creano un utente della distribuzione e distribuiscono l'app ad Azure. Verrà richiesta la password specificata in precedenza nel corso della creazione dell'utente della distribuzione. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>Configurare Monitoraggio di Azure (anteprima)

### <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics

Dopo aver distribuito l'app di esempio al Servizio app di Azure, è possibile configurare la funzionalità di monitoraggio per la risoluzione dei problemi dell'app quando si verificano. Monitoraggio di Azure archivia i dati di log in un'area di lavoro Log Analytics. Un'area di lavoro è un contenitore che include informazioni sui dati e sulla configurazione.

In questo passaggio viene creata un'area di lavoro Log Analytics per configurare Monitoraggio di Azure con l'app.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Per Log Analytics in Monitoraggio di Azure si paga per l'inserimento e la conservazione dei dati.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Creare un'impostazione di diagnostica

È possibile usare le impostazioni di diagnostica per raccogliere le metriche per determinati servizi di Azure nei log di Monitoraggio di Azure per l'analisi con altri dati di monitoraggio usando le query su log. Per questa esercitazione si abilitano il server Web e i log di output standard e/o degli errori. Vedere i [tipi di log supportati](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types) per un elenco completo dei tipi di log e delle descrizioni.

Eseguire i comandi seguenti per creare le impostazioni di diagnostica per AppServiceConsoleLogs (output standard/errori) e AppServiceHTTPLogs (log del server Web). Sostituire _\<app-name>_ e _\<workspace-name>_ con i propri valori. 

> [!NOTE]
> I primi due comandi, `resourceID` e `workspaceID`, sono variabili da usare nel comando `az monitor diagnostic-settings create`. Vedere [Creare le impostazioni di diagnostica usando l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli) per altre informazioni su questo comando.
>

```bash
resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'
```

## <a name="troubleshoot-the-app"></a>Risolvere i problemi dell'app

Passare a `http://<app-name>.azurewebsites.net`.

L'app di esempio, ImageConverter, converte le immagini incluse da `JPG` a `PNG`. Per questa esercitazione è stato deliberatamente inserito nel codice un bug. Se si seleziona un numero sufficiente di immagini, l'app genera un errore HTTP 500 durante la conversione delle immagini. Si supponga che questo scenario non sia stato preso in considerazione durante la fase di sviluppo. Per risolvere l'errore, viene usato Monitoraggio di Azure.

### <a name="verify-the-app-is-works"></a>Verificare il funzionamento dell'app

Per convertire le immagini, fare clic su `Tools` e selezionare `Convert to PNG`.

![Fare clic su 'Tools' e selezionare 'Convert to PNG'.](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Selezionare le prime due immagini e fare clic su `convert`. La conversione verrà eseguita correttamente.

![Selezionare le prime due immagini](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Interrompere l'app

Dopo avere verificato l'app convertendo correttamente due immagini, si proverà a convertire le prime cinque immagini.

![Convertire le prime cinque immagini](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Questa azione ha esito negativo e genera un errore `HTTP 500` che non è stato testato durante lo sviluppo.

![La conversione restituirà un errore HTTP 500](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Usare una query su log per visualizzare i log di Monitoraggio di Azure

Verranno ora visualizzati i log disponibili nell'area di lavoro Log Analytics. 

Fare clic su questo [collegamento dell'area di lavoro Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) per accedere all'area di lavoro nel portale di Azure.

Nel portale di Azure selezionare l'area di lavoro Log Analytics.

### <a name="log-queries"></a>Query di log

Le query su log consentono di sfruttare appieno il valore dei dati raccolti nei log di Monitoraggio di Azure. Le query su log vengono usate per identificare i log sia in AppServiceHTTPLogs che in AppServiceConsoleLogs. Per altre informazioni sulle query su log, vedere [Panoramica delle query su log](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

### <a name="view-appservicehttplogs-with-log-query"></a>Visualizzare AppServiceHTTPLogs con una query su log

Dopo avere eseguito l'accesso all'app, vengono visualizzati i dati associati alle richieste HTTP disponibili in `AppServiceHTTPLogs`.

1. Fare clic su `Logs` nel riquadro di spostamento a sinistra.

![Log dell'area di lavoro Log Analytics](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Cercare `appservice` e fare doppio clic su `AppServiceHTTPLogs`.

![Tabelle dell'area di lavoro Log Analytics](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Fare clic su `Run`.

![AppServiceHTTPLogs dell'area di lavoro Log Analytics](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

La query su `AppServiceHTTPLogs` restituisce tutte le richieste nelle ultime 24 ore. La colonna `ScStatus` contiene lo stato HTTP. Per diagnosticare gli errori `HTTP 500`, limitare la colonna `ScStatus` a 500 ed eseguire la query, come illustrato di seguito:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>Visualizzare AppServiceConsoleLogs con una query su log

Dopo avere verificato gli errori HTTP 500, verranno esaminati l'output standard e/o gli errori dell'app. Questi log si trovano in 'AppServiceConsoleLogs'.

(1) Fare clic su `+` per creare una nuova query. 

(2) Fare doppio clic sulla tabella `AppServiceConsoleLogs` e fare clic su `Run`. 

Poiché la conversione di cinque immagini genera errori del server, è possibile verificare se l'app scrive gli errori filtrando `ResultDescription` per gli errori, come illustrato di seguito:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

Nella colonna `ResultDescription` verrà visualizzato l'errore seguente:

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Unire AppServiceHTTPLogs e AppServiceConsoleLogs

Dopo avere identificato sia gli errori HTTP 500 che gli errori standard, è necessario verificare se esiste una correlazione tra questi messaggi. Successivamente, si uniscono le tabelle in base al timestamp `TimeGenerated`.

> [!NOTE]
> È stata preparata una query che esegue le operazioni seguenti:
>
> - Filtra HTTPLogs per gli errori 500
> - Esegue query sui log della console
> - Unisce le tabelle in base a `TimeGenerated`
>

Eseguire la query riportata di seguito:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

Nella colonna `ResultDescription` verrà visualizzato l'errore seguente insieme agli errori del server Web:

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

Il messaggio indica che la memoria è stata esaurita nella riga 20 di `process.php`. Si è verificato ora che l'applicazione ha generato un errore durante l'errore HTTP 500. Verrà ora esaminato il codice per identificare il problema.

## <a name="identify-the-error"></a>Identificare l'errore

Nella directory locale aprire il file `process.php` e guardare la riga 20. 

```php
imagepng($imgArray[$x], $filename);
```

Il primo argomento `$imgArray[$x]` è una variabile che contiene tutti i JPG (in memoria) che necessitano di conversione. Tuttavia, `imagepng` necessita solo dell'immagine da convertire e non di tutte le immagini. Il precaricamento delle immagini non è necessario e potrebbe causare l'esaurimento della memoria, generando gli errori HTTP 500. Verrà ora aggiornato il codice per caricare le immagini su richiesta per verificare se il problema è stato risolto. Successivamente, sarà possibile migliorare il codice per risolvere il problema di memoria.

## <a name="fix-the-app"></a>Correggere l'app

### <a name="update-locally-and-redeploy-the-code"></a>Aggiornare e ridistribuire il codice in locale

Per gestire l'esaurimento della memoria, si apportano le seguenti modifiche al file `process.php`:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Eseguire il commit delle modifiche in Git e quindi effettuare il push delle modifiche al codice in Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure master
```

### <a name="browse-to-the-azure-app"></a>Passare all'app Azure

Passare a `http://<app-name>.azurewebsites.net`. 

La conversione delle immagini non genererà più gli errori HTTP 500.

![App PHP in esecuzione nel Servizio app di Azure](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

Eliminare l'impostazione di diagnostica con il comando seguente:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Configurazione di un'app Web con Monitoraggio di Azure
> * Invio di log a Log Analytics
> * Uso di query su log per identificare e risolvere i problemi relativi agli errori delle app Web

## <a name="next-steps"></a><a name="nextsteps"></a> Passaggi successivi
* [Eseguire query sui log con Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md)
* [Risoluzione dei problemi del Servizio app di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizzare i log delle app in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
