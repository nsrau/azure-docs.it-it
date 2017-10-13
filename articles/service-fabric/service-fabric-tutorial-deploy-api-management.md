---
title: Integrare Azure Service Fabric con Gestione API | Microsoft Docs
description: Informazioni su come iniziare a usare subito Gestione API di Azure con Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/13/2017
ms.author: ryanwi
ms.openlocfilehash: 705212675fc0a869a4374f621d5f2d7e035294dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Distribuire Gestione API e Service Fabric
Questa è la seconda di una serie di esercitazioni. Questa esercitazione mostra come impostare il servizio [Gestione API di Azure](../api-management/api-management-key-concepts.md) con Service Fabric per indirizzare il traffico ai servizi back-end di Service Fabric.  Al termine, la Gestione API sarà stata distribuita alla rete virtuale e sarà stata configurata un'operazione API per l'invio del traffico ai servizi senza stato di back-end. Per ulteriori informazioni sugli scenari di Gestione API di Azure con Service Fabric, vedere l'articolo [Panoramica](service-fabric-api-management-overview.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Distribuire Gestione API
> * Configurare Gestione API
> * Creare un'operazione per le API
> * Configurare un criterio di back-end
> * Aggiungere l'API a un prodotto

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare [un cluster di Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o un [cluster di Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) protetto in Azure usando un modello
> * Distribuire Gestione API e Service Fabric

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installare il [modulo Azure PowerShell 4.1 o versioni successive](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) o [la versione 2.0 dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
- Creare [un cluster di Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o un [cluster di Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) protetto in Azure

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Accedere ad Azure e selezionare la sottoscrizione
Questa esercitazione usa [Azure PowerShell][azure-powershell]. Quando si avvia una nuova sessione di PowerShell, accedere al proprio account Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.
 
Accedere al proprio account Azure e selezionare la sottoscrizione:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-api-management"></a>Distribuire Gestione API
Le applicazioni cloud necessitano in genere di un gateway front-end per garantire un singolo punto di ingresso per utenti, dispositivi o altre applicazioni. In Service Fabric un gateway può essere qualsiasi servizio senza stato, ad esempio un'applicazione ASP.NET Core, o un altro servizio progettati per l'ingresso del traffico, ad esempio Hub eventi, Hub IoT o Gestione API di Azure. In questa esercitazione viene illustrata un'introduzione all'uso di Gestione API di Azure come gateway per le applicazioni di Service Fabric. Gestione API si integra direttamente in Service Fabric, consentendo di pubblicare API con un ampio set di regole di routing nei servizi Service Fabric back-end. 

Ora che si dispone di un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o di un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) protetto in Azure, distribuire Gestione API per la rete virtuale nella subnet e il gruppo NSG designato per Gestione API. Per questa esercitazione, il modello di Resource Manager di Gestione API è preconfigurato per l'uso dei nomi di rete virtuale, subnet e gruppo NSG impostati [nell'esercitazione per cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o [nell'esercitazione per cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md). 

Scaricare il modello e il file di parametri di Resource Manager seguenti:
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

Specificare i parametri vuoti nel file `apim.parameters.json` per la distribuzione.

Per distribuire il modello e i file dei parametri di Gestione risorse per Gestione API, usare il seguente script:

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>Configurare Gestione API

Dopo aver distribuito Gestione API e il cluster di Service Fabric, è possibile configurare le impostazioni di sicurezza e un back-end di Service Fabric in Gestione API. Questo consente di creare un criterio del servizio back-end che invia il traffico al cluster di Service Fabric.

### <a name="configure-api-management-security"></a>Configurare la sicurezza di Gestione API

Per configurare il back-end di Service Fabric, è innanzitutto necessario configurare le impostazioni di sicurezza di Gestione API. A questo scopo, passare al servizio Gestione API nel portale di Azure.

#### <a name="enable-the-api-management-rest-api"></a>Abilitare l'API REST di Gestione API

L'API REST di Gestione API rappresenta al momento l'unico modo per configurare un servizio back-end. È innanzitutto necessario abilitare l'API REST di Gestione API e proteggerla.

 1. Nel servizio Gestione API selezionare **Gestione API** in **Sicurezza**.
 2. Selezionare la casella di controllo **Abilita API REST di Gestione API**.
 3. Prendere nota dell'**URL dell'API di gestione** che verrà usato successivamente per configurare il back-end di Service Fabric.
 4. Generare un **token di accesso** selezionando una data di scadenza e una chiave, quindi fare clic sul pulsante **Genera** nella parte inferiore della pagina.
 5. Copiare il **token di accesso** e salvarlo.  Il token di accesso verrà usato nei passaggi successivi. Tenere presente che questo token è diverso dalla chiave primaria e dalla chiave secondaria.

#### <a name="upload-a-service-fabric-client-certificate"></a>Caricare un certificato client di Service Fabric

Gestione API deve eseguire l'autenticazione con il cluster di Service Fabric per individuare il servizio mediante un certificato client che dispone di accesso al cluster. Per semplicità, in questa esercitazione viene usato lo stesso certificato specificato durante la creazione del [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) o del [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor) che per impostazione predefinita può essere usato per accedere al cluster.

 1. Nel servizio Gestione API selezionare **Certificati client** in **Sicurezza**.
 2. Fare clic sul pulsante **+ Aggiungi**.
 2. Selezionare il file di chiave privata (con estensione pfx) del certificato del cluster specificato durante la creazione del cluster di Service Fabric, assegnargli un nome e immettere la password della chiave privata.

> [!NOTE]
> In questa esercitazione viene usato lo stesso certificato per l'autenticazione client e la sicurezza del cluster da nodo a nodo. È possibile usare un certificato client separato se ne è stato configurato uno per l'accesso al cluster di Service Fabric.

### <a name="configure-the-backend"></a>Configurare il back-end

Dopo aver configurato la sicurezza di Gestione API, è possibile configurare il back-end di Service Fabric. Per i back-end di Service Fabric, il cluster di Service Fabric è il back-end, anziché uno specifico servizio di Service Fabric. Questo consente di eseguire il routing di un singolo criterio in più servizi del cluster.

Per eseguire questo passaggio è necessario specificare il token di accesso generato prima e l'identificazione personale per il certificato cluster caricato precedentemente in Gestione API.

> [!NOTE]
> Se nel passaggio precedente è stato usato un certificato client separato per Gestione API, in questo passaggio sarà necessario specificare l'identificazione personale per il certificato client oltre all'identificazione personale per il certificato cluster.

Inviare la richiesta HTTP PUT all'URL API di Gestione API annotato in precedenza durante l'abilitazione dell'API REST di Gestione API per configurare il servizio back-end di Service Fabric. Al completamento del comando, dovrebbe essere visualizzata la risposta `HTTP 201 Created`. Per ulteriori informazioni su ciascun campo, vedere la [documentazione di riferimento dell'API di back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) di Gestione API.

Comando e URL HTTP:
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

Intestazioni della richiesta:
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

Corpo della richiesta:
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

Il parametro **url** è il nome completo di un servizio nel cluster al quale, per impostazione predefinita, vengono indirizzate tutte le richieste se non viene specificato alcun servizio in un criterio di back-end. È possibile usare un nome di servizio fittizio, ad esempio "fabric:/fake/service", non se non si desidera un servizio di fallback.

Per ulteriori informazioni su ciascun campo, vedere la [documentazione di riferimento dell'API di back-end](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) di Gestione API.

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Distribuire un servizio back-end di Service Fabric

Dopo aver configurato Service Fabric come back-end di Gestione API, è possibile creare criteri di back-end per le API che inviano il traffico nei propri servizi Service Fabric. A questo scopo, è innanzitutto necessario un servizio in esecuzione in Service Fabric per accettare le richieste.

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>Creare un servizio Service Fabric con un endpoint HTTP

Per questa esercitazione verrà creato un Reliable Service senza stato di base per ASP.NET Core usando il modello di progetto API Web predefinito. Questo permetterà di generare un endpoint HTTP per il servizio, che verrà esposto tramite Gestione API di Azure:

```
/api/values
```

Partire dall'[impostazione dell'ambiente per lo sviluppo di ASP.NET Core](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core).

Dopo aver impostato l'ambiente di sviluppo, avviare Visual Studio come Amministratore e creare un servizio ASP.NET Core:

 1. In Visual Studio selezionare File -> Nuovo progetto.
 2. Selezionare il modello Applicazione di Service Fabric nel Cloud e denominarlo **"ApiApplication"**.
 3. Selezionare il modello di servizio ASP.NET Core e denominare il progetto **"WebApiService"**.
 4. Selezionare il modello di progetto Web API ASP.NET Core 1.1.
 5. Una volta creato il progetto, aprire `PackageRoot\ServiceManifest.xml` e rimuovere l'attributo `Port` dalla configurazione delle risorse endpoint:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Questo consente a Service Fabric di specificare in modo dinamico una porta dell'intervallo di porte dell'applicazione, che è stata aperta nel gruppo NSG del modello di Resource Manager del cluster, permettendo il flusso del traffico in tale porta da Gestione API.
 
 6. Premere F5 in Visual Studio per verificare che l'API Web sia disponibile in locale. 

    Aprire Service Fabric Explorer ed eseguire il drill-down fino a un'istanza specifica del servizio ASP.NET Core per visualizzare l'indirizzo di base di ascolto del servizio. Aggiungere `/api/values` all'indirizzo di base e aprirlo in un browser. Questa operazione chiama il metodo Get su ValuesController nel modello API Web e restituisce la risposta predefinita trasmessa dal modello, ovvero una matrice JSON contenente due stringhe:

    ```json
    ["value1", "value2"]`
    ```

    Questo è l'endpoint che verrà esposto tramite Gestione API in Azure.

 7. Infine, distribuire l'applicazione nel cluster in Azure. [Usando Visual Studio](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box), fare clic con il pulsante destro del mouse sul progetto Applicazione e scegliere **Pubblica**. Specificare l'endpoint del cluster, ad esempio `mycluster.westus.cloudapp.azure.com:19000`, per distribuire l'applicazione nel cluster di Service Fabric in Azure.

In tale cluster dovrebbe essere ora in esecuzione un servizio ASP.NET Core senza stato denominato `fabric:/ApiApplication/WebApiService`.

## <a name="create-an-api-operation"></a>Creare un'operazione per le API

A questo punto è possibile creare in Gestione API un'operazione usata dai client esterni per comunicare con il servizio ASP.NET Core senza stato in esecuzione nel cluster di Service Fabric.

1. Accedere al portale di Azure e passare alla distribuzione del servizio Gestione API.
2. Nel pannello del servizio Gestione API selezionare **API**.
3. Aggiungere una nuova API facendo clic su **+API** e quindi sulla casella **API vuota** e completare la finestra di dialogo:

    - **URL del servizio Web**: per i back-end di Service Fabric, questo valore URL non viene usato. È possibile inserire un valore qualsiasi nel campo. Per questa esercitazione usare "http://servicefabric".
    - **Nome visualizzato**: inserire il nome dell'API. Per questa esercitazione usare "App di Service Fabric".
    - **Nome**: inserire "service-fabric-app".
    - **Schema URL**: selezionare **HTTP**, **HTTPS** o **Entrambi**. Per questa esercitazione usare **Entrambi**.
    - **Suffisso URL API**: inserire un suffisso per l'API. Per questa esercitazione usare "myapp".
 
4. Selezionare **App di Service Fabric** nell'elenco di API e fare clic su **+ Aggiungi operazione** per aggiungere un'operazione API front-end. Compilare i valori come indicato di seguito.
    
    - **URL**: selezionare **GET** e specificare un percorso URL per l'API. Per questa esercitazione usare "/api/values".  Per impostazione predefinita, il percorso URL specificato qui è il percorso URL inviato al servizio Service Fabric di back-end. Se si specifica lo stesso percorso URL usato dal servizio, in questo caso "/api/values", non è necessario apportare ulteriori modifiche. È anche possibile specificare un percorso URL diverso da quello usato dal servizio Service Fabric di back-end. In questo caso, sarà necessario immettere successivamente anche un'indicazione di riscrittura del percorso nel criterio dell'operazione.
    - **Nome visualizzato**: specificare un nome per l'API. Per questa esercitazione usare "Valori".

5. Fare clic su **Salva**.

## <a name="configure-a-backend-policy"></a>Configurare un criterio di back-end

Il criterio di back-end collega tutti gli elementi e consente di configurare il servizio Service Fabric di back-end a cui vengono indirizzate le richieste. È possibile applicare questo criterio a qualsiasi operazione API. La [configurazione back-end per Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) fornisce i controlli di indirizzamento delle richieste seguenti: 
 - Selezione dell'istanza del servizio mediante la specifica di un nome di istanza del servizio Service Fabric, hardcoded (ad esempio, `"fabric:/myapp/myservice"`) o generato dalla richiesta HTTP (ad esempio, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Risoluzione della partizione mediante la generazione di una chiave di partizione che usa uno schema di partizionamento di Service Fabric.
 - Selezione della replica per i servizi con stato.
 - Condizioni per i nuovi tentativi di risoluzione della posizione di un servizio e il nuovo invio di una richiesta.

Per questa esercitazione, creare un criterio di back-end che consenta di indirizzare le richieste direttamente al servizio ASP.NET Core senza stato distribuito in precedenza:

 1. Selezionare e modificare i **criteri in ingresso** per l'operazione Valori facendo clic sull'icona di modifica, quindi scegliere **Visualizzazione Codice**.
 2. Nell'editor del codice dei criteri aggiungere un criterio `set-backend-service` nei criteri in ingresso, come illustrato di seguito, e fare clic sul pulsante **Salva**:
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
           sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
      </inbound>
      <backend>
        <base/>
      </backend>
      <outbound>
        <base/>
      </outbound>
    </policies>
    ```

Per un set completo di attributi di criteri di back-end di Service Fabric, vedere la [documentazione back-end di Gestione API](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService).

### <a name="add-the-api-to-a-product"></a>Aggiungere l'API a un prodotto 

Prima di chiamare l'API, è innanzitutto necessario aggiungerla a un prodotto per il quale sia possibile concedere l'accesso agli utenti. 

 1. Nel servizio Gestione API selezionare **Prodotti**.
 2. Per impostazione predefinita, Gestione API include due prodotti: Starter e Illimitato. Selezionare il prodotto Illimitato.
 3. Selezionare **+Aggiungi API**.
 4. Selezionare l'API `Service Fabric App` creata nei passaggi precedenti e fare clic sul pulsante **Seleziona**.

### <a name="test-it"></a>Eseguirne il test

È ora possibile provare a inviare una richiesta al servizio back-end in Service Fabric tramite Gestione API direttamente dal portale di Azure.

 1. Nel servizio Gestione API selezionare **API**.
 2. Selezionare l'API di **App di Service Fabric** creata nei passaggi precedenti, fare clic sulla scheda **Test** e quindi selezionare l'operazione **Valori**.
 3. Fare clic sul pulsante **Invia** per inviare una richiesta di test al servizio back-end.  La risposta HTTP visualizzata sarà simile a questa:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Un cluster è costituito da altre risorse di Azure oltre alla risorsa cluster stessa. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

Accedere ad Azure e selezionare l'ID della sottoscrizione da usare per rimuovere il cluster.  È possibile trovare l'ID della sottoscrizione accedendo al [portale di Azure](http://portal.azure.com). Eliminare il gruppo di risorse e tutte le risorse del cluster con il [cmdlet Remove-AzureRmResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Distribuire Gestione API
> * Configurare Gestione API
> * Creare un'operazione per le API
> * Configurare un criterio di back-end
> * Aggiungere l'API a un prodotto


[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json