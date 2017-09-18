---
title: "Creare un'app con iperscalabilità in Azure | Microsoft Docs"
description: Informazioni su come usare i diversi servizi di Azure per ottimizzare le prestazioni di un'applicazione ASP.NET in Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: erikre
editor: 
ms.assetid: a4d49ac7-0f97-4997-84c5-cdb9c4465757
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2017
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 17d046f4f5291233cc5dc29c96e2b68d767d5750
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---
# <a name="build-a-hyper-scale-web-app-in-azure"></a>Creare un'app Web con iperscalabilità in Azure

Questa esercitazione illustra come scalare orizzontalmente un'app Web ASP.NET in Azure per ottimizzare le richieste degli utenti.

Prima di iniziare, verificare che nel computer sia [installata l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). È necessario anche disporre di [Visual Studio](https://www.visualstudio.com/vs/) sul computer locale per eseguire l'applicazione di esempio.

## <a name="step-1---get-sample-application"></a>Passaggio 1: applicazione di esempio
In questo passaggio si imposta il progetto ASP.NET locale.

### <a name="clone-the-application-repository"></a>Clonare il repository di applicazione

Aprire il terminale della riga di comando desiderato ed eseguire `CD` in una directory di lavoro. Eseguire quindi i comandi seguenti per clonare l'applicazione di esempio. 

```powershell
git clone https://github.com/cephalin/HighScaleApp.git
```

### <a name="run-the-sample-application-in-visual-studio"></a>Eseguire l'applicazione in Visual Studio

Aprire la soluzione in Visual Studio.

```powershell
cd HighScaleApp
.\HighScaleApp.sln
```

Digitare `F5` per eseguire l'applicazione.

Questa app Web ASP.NET di esempio deriva dal modello predefinito, mantiene le sessioni degli utenti e usa la cache di output. Vedere `HighScaleApp\Controllers\HomeController.cs`. Il metodo `Index()` aggiunge una parte di dati alla sessione.

```csharp
Session.Add("visited", "true"); 
```

I metodi `About()` e `Contact()` memorizzano il relativo output nella cache.

```csharp
[OutputCache(Duration = 60)]
```

## <a name="step-2---deploy-to-azure"></a>Passaggio 2: distribuire in Azure
In questo passaggio si crea un'app Web di Azure, per poi distribuire in essa l'applicazione ASP.NET di esempio.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse   
Usare il comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) per creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) nell'area Europa occidentale. In un gruppo di risorse si inseriranno tutte le risorse che si vogliono gestire insieme, ad esempio l'app Web e il back-end del database SQL.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Per visualizzare i possibili valori utilizzabili per `---location`, usare il comando [az appservice list-locations](https://docs.microsoft.com/en-us/cli/azure/appservice#az_appservice_list_locations).

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app
Usare il comando [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#az_appservice_plan_create) per creare un [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "B1". 

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1
```

Un piano di servizio app è un'unità di scala, che può includere il numero desiderato di app da scalare verticalmente oppure orizzontalmente, oltre alla medesima infrastruttura del servizio app. A ogni piano viene inoltre assegnato un [piano tariffario](https://azure.microsoft.com/en-us/pricing/details/app-service/). I piani tariffari superiori includono un hardware migliore e più caratteristiche, ad esempio un numero maggiore di istanze di scalabilità orizzontale.

Per questa esercitazione, B1 è il livello minimo che consente la scalabilità orizzontale in tre istanze. È sempre possibile modificare il piano tariffario dell'app in un secondo momento eseguendo il comando [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update). 

### <a name="create-a-web-app"></a>Creare un'app Web
Usare il comando [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_create) per creare un'app Web con un nome univoco in `$appName`.

```azurecli
$appName = "<replace-with-a-unique-name>"
az appservice web create --name $appName --resource-group myResourceGroup --plan myAppServicePlan
```

### <a name="set-deployment-credentials"></a>Reimpostare le credenziali di distribuzione
Usare il comando [az appservice web deployment user set](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/user#az_appservice_web_deployment_user_set) per impostare le credenziali di distribuzione a livello di account per il servizio app.

```azurecli
az appservice web deployment user set --user-name <letters-numbers> --password <mininum-8-char-captital-lowercase-letters-numbers>
```

### <a name="configure-git-deployment"></a>Configurare la distribuzione Git
Usare il comando [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config_local_git) per configurare la distribuzione Git locale.

```azurecli
az appservice web source-control config-local-git --name $appName --resource-group myResourceGroup
```

Questo comando fornisce un output simile al seguente:

```json
{
  "url": "https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git"
}
```

Usare l'URL restituito per configurare Git remoto. Il comando seguente usa l'esempio di output precedente.

```powershell
git remote add azure https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-the-sample-application"></a>Distribuire l'applicazione di esempio
È ora possibile distribuire l'applicazione di esempio. Eseguire `git push`.

```powershell
git push azure master
```

Quando viene richiesta la password, usare quella specificata all'esecuzione di `az appservice web deployment user set`.

### <a name="browse-to-azure-web-app"></a>Passare all'app Web di Azure
Usare il comando [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_browse) per osservare l'app in esecuzione in Azure.

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-3---connect-to-redis"></a>Passaggio 3: connettersi a Redis
In questo passaggio si configura una cache Redis di Azure come una cache esterna con percorso condiviso nell'app Web di Azure. È possibile usare rapidamente Redis per memorizzare nella cache l'output della pagina. In più, quando si scalano orizzontalmente le app Web in un secondo momento, Redis aiuta a mantenere le sessioni degli utenti su più istanze in maniera affidabile.

### <a name="create-an-azure-redis-cache"></a>Creare una cache Redis di Azure
Usare il comando [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_create) per creare una cache Redis di Azure e salvare l'output JSON. Usare un nome univoco in `$cacheName`.

```powershell
$cacheName = "<replace-with-a-unique-cache-name>"
$redis = (az redis create --name $cacheName --resource-group myResourceGroup --location "West Europe" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

### <a name="configure-the-application-to-use-redis"></a>Configurare l'applicazione per l'uso di Redis
Formattare la stringa di connessione per la cache.

```powershell
$connstring = "$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False"
$connstring 
```

La seconda riga dovrebbe mostrare un output simile al seguente:

```
mycachename.redis.cache.windows.net:6380,password=/rQP/TLz1mrEPpmh9b/gnfns/t9vBRXqXn3i1RwBjGA=,ssl=True,abortConnect=False
```

In Visual Studio creare un file di configurazione Web nella radice del progetto denominato `redis.config` e incollarvi il codice seguente. In `value` usare la stringa di connessione dall'output di PowerShell.

```xml
<appSettings>
  <add key="RedisConnection" value="your-azure-redis-cache-connection-string"/>
</appSettings>
```

Se si osserva il file `.gitignore` nel repository Git, si noterà che è escluso dal controllo del codice sorgente. In questo modo le informazioni riservate vengono protette. 

Aprire `Web.config`. Si noti l'elemento `<appSettings file="redis.config">`, che riceve l'impostazione creata in `redis.config`. 

Individuare la sezione commentata che include `<sessionState>` e `<caching>`. Rimuovere il commento in questa sezione.

![](./media/app-service-web-tutorial-hyper-scale-app/redisproviders.png)

Questo codice cerca la stringa di connessione a Redis definita in `RedisConnection`. 

Ora l'applicazione usa Redis per gestire sessioni e memorizzazione nella cache. Digitare `F5` per eseguire l'applicazione. Se si desidera, è possibile scaricare un client di gestione di Redis per visualizzare i dati salvati nella cache.

### <a name="configure-the-connection-string-in-azure"></a>Configurare la stringa di connessione in Azure

Per far funzionare l'applicazione in Azure è necessario configurare la medesima stringa di connessione a Redis nell'app Web di Azure. Poiché `redis.config` non viene mantenuto nel controllo del codice sorgente, non viene distribuito in Azure quando si esegue la distribuzione Git.

Usare il comando [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#az_appservice_web_config_appsettings_update) per aggiungere la stringa di connessione con lo stesso nome (`RedisConnection`).

az appservice web config appsettings update --settings "RedisConnection=$connstring" --name $appName --resource-group myResourceGroup

Tenere presente che `$connstring` contiene la stringa di connessione formattata.

### <a name="redeploy-the-application-to-azure"></a>Ridistribuire l'applicazione in Azure
Usare i comandi di Git per inviare le modifiche in Azure

```bash
git add .
git commit -m "now use Redis providers"
git push azure master
```

Quando viene richiesta la password, usare quella specificata all'esecuzione di `az appservice web deployment user set`.

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure
Usare il comando [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_browse) per visualizzare le modifiche pubblicate in Azure.

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-4---scale-to-multiple-instances"></a>Passaggio 4: scalare in più istanze
Il piano di servizio app è l'unità di scala per le app Web di Azure. Per scalare orizzontalmente l'app Web, è necessario scalare il piano di servizio app.

Usare il comando [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update) per scalare orizzontalmente il piano di servizio app in tre istanze, ovvero il numero massimo consentito dal piano tariffario B1. Tenere presente che B1 è il piano tariffario scelto quando in precedenza si è creato il piano di servizio app. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --number-of-workers 3 
```

## <a name="step-5---scale-geographically"></a>Passaggio 5: scalare geograficamente
Durante la scalabilità geografica, si esegue l'app in più aree del cloud di Azure. Questa configurazione bilancia ulteriormente il carico dell'app in base all'area geografica e riduce i tempi di risposta posizionandola più vicina ai browser client.

In questo passaggio si scala l'app Web ASP.NET in una seconda area con [Gestione traffico di Azure](https://docs.microsoft.com/en-us/azure/traffic-manager/). Al termine del passaggio, si otterrà un'app Web in esecuzione in Europa occidentale (già creata) e un'app Web in esecuzione in Asia sud-orientale (non ancora creata). Entrambe le app verranno servite dallo stesso URL di Gestione traffico.

### <a name="scale-up-the-europe-app-to-standard-tier"></a>Scalare l'app Europa al livello Standard
Nel servizio app l'integrazione con Gestione traffico di Azure richiede il piano tariffario Standard. Usare il comando [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update) per far passare a S1 il piano di servizio app. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --sku S1
```
### <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico 
Usare il comando [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) per creare un profilo di Gestione traffico e aggiungerlo al gruppo di risorse. Usare un nome DNS univoco in $dnsName.

```azurecli
$dnsName = "<replace-with-unique-dns-name>"
az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name $dnsName
```

> [!NOTE]
> `--routing-method Performance` specifica che questo profilo [instrada il traffico utente all'endpoint più vicino](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="get-the-resource-id-of-the-europe-app"></a>Ottenere l'ID risorsa dell'app Europa
Usare il comando [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_show) per ottenere l'ID risorsa dell'app Web.

```azurecli
$appId = az appservice web show --name $appName --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-europe-app"></a>Aggiungere un endpoint di Gestione traffico per l'app Europa
Usare il comando [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) per aggiungere un endpoint al profilo di Gestione traffico e usare l'ID risorsa dell'app Web come destinazione.

```azurecli
az network traffic-manager endpoint create --name myWestEuropeEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appId
```

### <a name="get-the-traffic-manager-endpoint-url"></a>Ottenere l'URL dell'endpoint di Gestione traffico
Ora il profilo di Gestione traffico dispone di un endpoint che punta all'app Web esistente. Usare il comando [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_show) per ottenere l'URL. 

```azurecli
az network traffic-manager profile show --name myTrafficManagerProfile --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
```

Copiare l'output nel browser. Dovrebbe essere visualizzata nuovamente l'app Web.

### <a name="create-an-azure-redis-cache-in-asia"></a>Creare una cache Redis di Azure in Asia
Ora si vedrà come replicare l'app Web di Azure nell'area Asia sud-orientale. Per iniziare, usare il comando [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_create) per creare una seconda cache Redis di Azure in Asia sud-orientale. Questa cache deve disporre di un percorso condiviso con l'app in Asia.

```powershell
$redis = (az redis create --name $cacheName-asia --resource-group myResourceGroup --location "Southeast Asia" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

`--name $cacheName-asia` fornisce alla cache il nome della cache in Europa occidentale, con il suffisso `-asia`.

### <a name="create-an-app-service-plan-in-asia"></a>Creare un piano di servizio app in Asia
Usare il comando [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) per creare un secondo piano di servizio app nell'area Asia sud-orientale, usando il medesimo piano tariffario dell'area Europa occidentale (S1).

```azurecli
az appservice plan create --name myAppServicePlanAsia --resource-group myResourceGroup --location "Southeast Asia" --sku S1
```

### <a name="create-a-web-app-in-asia"></a>Creare un'app Web in Asia
Usare il comando [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_create) per creare una seconda app Web.

```azurecli
az appservice web create --name $appName-asia --resource-group myResourceGroup --plan myAppServicePlanAsia
```

`--name $appName-asia` fornisce all'app il nome dell'app in Europa occidentale, con il suffisso `-asia`.

### <a name="configure-the-connection-string-for-redis"></a>Configurare la stringa di connessione per Redis
Usare il comando [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#az_appservice_web_config_appsettings_update) per aggiungere la stringa di connessione per la cache in Asia sud-orientale all'app Web.

az appservice web config appsettings update --settings "RedisConnection=$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False" --name $appName-asia --resource-group myResourceGroup

### <a name="configure-git-deployment-for-the-asia-app"></a>Configurare la distribuzione Git per l'app in Asia.
Usare il comando [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#az_appservice_web_source_control_config_local_git) per configurare la distribuzione Git locale per la seconda app Web.

```azurecli
az appservice web source-control config-local-git --name $appName-asia --resource-group myResourceGroup
```

Questo comando fornisce un output simile al seguente:

```json
{
  "url": "https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git"
}
```

Usare l'URL restituito per configurare un secondo Git remoto per il repository locale. Il comando seguente usa l'esempio di output precedente.

```bash
git remote add azure-asia https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-your-sample-application"></a>Distribuire l'applicazione di esempio
Eseguire `git push` per distribuire l'applicazione di esempio nel secondo Git remoto. 

```bash
git push azure-asia master
```

Quando viene richiesta la password, usare quella specificata all'esecuzione di `az appservice web deployment user set`.

### <a name="browse-to-the-asia-app"></a>Passare all'app Asia
Usare il comando [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_browse) per accertarsi che l'app sia in esecuzione in Azure.

```azurecli
az appservice web browse --name $appName-asia --resource-group myResourceGroup
```

### <a name="get-the-resource-id-of-the-asia-app"></a>Ottenere l'ID risorsa dell'app Asia
Usare il comando [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#az_appservice_web_show) per ottenere l'ID risorsa dell'app Web in Asia sud-orientale.

```azurecli
$appIdAsia = az appservice web show --name $appName-asia --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-asia-app"></a>Aggiungere un endpoint di Gestione traffico per l'app Asia
Usare il comando [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) per aggiungere un secondo endpoint al profilo di Gestione traffico.

```azurecli
az network traffic-manager endpoint create --name myAsiaEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appIdAsia
```

### <a name="add-region-identifier-to-web-apps"></a>Aggiungere un identificatore di area alle app Web
Usare il comando [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#az_appservice_web_config_appsettings_update) per aggiungere una variabile di ambiente specifica per l'area.

```azurecli
az appservice web config appsettings update --settings "Region=West Europe" --name $appName --resource-group myResourceGroup
az appservice web config appsettings update --settings "Region=Southeast Asia" --name $appName-asia --resource-group myResourceGroup
```

Il codice dell'applicazione usa già questa impostazione. Vedere `HighScaleApp\Views\Home\Index.cshtml`.

### <a name="complete"></a>Operazione completata

A questo punto, provare ad accedere all'URL del profilo di Gestione traffico da browser in aree geografiche diverse. I browser client situati in Europa dovrebbero mostrare "ASP.NET Europa occidentale", mentre quelli in Asia dovrebbero mostrare "ASP.NET Asia sud-orientale".

## <a name="more-resources"></a>Altre risorse

