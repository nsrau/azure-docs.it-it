---
title: Avvio rapido di Azure Service Fabric con Gestione API | Microsoft Docs
description: Questa guida illustra le procedure per iniziare a usare subito Gestione API di Azure con Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2160e2e65de5c65df8a13248bad4f626def86e49
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---

# <a name="service-fabric-with-azure-api-management-quick-start"></a>Avvio rapido di Service Fabric con Gestione API

Questa guida mostra come impostare il servizio Gestione API di Azure con Service Fabric e configurare la prima operazione API per l'invio di traffico ai servizi back-end di Service Fabric. Per ulteriori informazioni sugli scenari di Gestione API di Azure con Service Fabric, vedere l'articolo [Panoramica](service-fabric-api-management-overview.md). 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>Distribuire Gestione API e Service Fabric in Azure

Il primo passaggio consiste nel distribuire un cluster di Gestione API e Service Fabric in Azure tramite una rete virtuale condivisa. In questo modo Gestione API può comunicare direttamente con Service Fabric in modo da poter eseguire l'individuazione del servizio, la risoluzione della partizione del servizio e inoltrare il traffico direttamente a un servizio back-end in Service Fabric.

### <a name="topology"></a>Topologia

Le informazioni contenute in questa guida consentono di distribuire in Azure la topologia mostrata di seguito, dove Gestione API e Service Fabric si trovano in subnet della stessa rete virtuale:

 ![Sottotitolo immagine][sf-apim-topology-overview]

Per iniziare rapidamente, vengono forniti modelli di Resource Manager per ogni passaggio di distribuzione:

 - Topologia di rete:
    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]
 - Cluster di Service Fabric:
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]
 - Gestione API:
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>Accedere ad Azure e selezionare la sottoscrizione

Questa guida usa [Azure PowerShell][azure-powershell]. Quando si avvia una nuova sessione di PowerShell, accedere al proprio account Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.
 
Accedere all'account Azure:

```powershell
PS > Login-AzureRmAccount
```

Selezionare la propria sottoscrizione:

```powershell
PS > Get-AzureRmSubscription
PS > Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un nuovo gruppo di risorse per la propria distribuzione. Assegnargli un nome e una posizione.

```powershell
PS > New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>Distribuire la topologia di rete

Il primo passaggio consiste nella configurazione della topologia di rete nella quale verrà distribuito il cluster di Gestione API e Service Fabric. Il modello di Resource Manager [network.json][network-arm] viene configurato per creare una rete virtuale (VNET) con due subnet e due gruppi di sicurezza di rete (NSG). 

Il file di parametri [network.parameters.json][network-parameters-arm] contiene i nomi delle subnet e i gruppi di sicurezza di rete in cui verranno distribuiti Gestione API e Service Fabric. In questa guida non è necessario modificare i valori dei parametri. I modelli di Resource Manager di Gestione API e Service Fabric usano questi valori, pertanto se vengono modificati qui, sarà necessario modificarli anche negli altri modelli di Resource Manager. 

 1. Scaricare il modello e il file di parametri di Resource Manager seguenti:

    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]

 2. Per distribuire il modello e il file di parametri di Resource Manager per la configurazione di rete, usare il comando PowerShell seguente:

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>Distribuire il cluster di Service Fabric

Una volta terminata la distribuzione delle risorse di rete, il passaggio successivo consiste nel distribuire un cluster Service Fabric per la rete virtuale nella subnet e nel gruppo NSG designato per tale cluster. Per questa esercitazione, il modello di Resource Manager di Service Fabric è preconfigurato per l'uso dei nomi di rete virtuale, subnet e gruppo NSG impostati nel passaggio precedente. 

Il modello di Resource Manager del cluster di Service Fabric è configurato per la creazione di un cluster protetto mediante un'architettura di sicurezza basata sui certificati. Il certificato viene usato per proteggere la comunicazione da nodo a nodo per il cluster e per gestire l'accesso utente al cluster di Service Fabric. Gestione API usa questo certificato per accedere al Service Fabric Naming Service per l'individuazione del servizio.

Per questo passaggio è necessario disporre di un certificato nel Key Vault per la sicurezza del cluster. Per ulteriori informazioni sull'impostazione di un cluster protetto con Key Vault, vedere [questa guida per la creazione di un cluster di Service Fabric mediante Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

> [!NOTE]
> È possibile aggiungere l'autenticazione di Azure Active Directory oltre al certificato usato per l'accesso al cluster. Azure Active Directory è il modo consigliato per gestire l'accesso utente al cluster di Service Fabric, ma non è necessario per completare questa esercitazione. In entrambi i casi, è richiesto un certificato per la sicurezza del cluster da nodo a nodo e per l'autenticazione di Gestione API di Azure, che attualmente non supporta l'autenticazione con Azure Active Directory per un back-end di Service Fabric.

 1. Scaricare il modello e il file di parametri di Resource Manager seguenti:
 
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]

 2. Compilare i parametri vuoti nel file `cluster.parameters.json` per la distribuzione, incluse le [informazioni sul Key Vault](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) per il certificato del cluster.

 3. Per distribuire il modello e i file di parametri di Resource Manager per creare il cluster di Service Fabric, usare il comando PowerShell seguente:

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>Distribuire Gestione API

Infine, distribuire Gestione API per la rete virtuale nella subnet e il gruppo NSG designato per Gestione API. Non è necessario attendere il completamento della distribuzione del cluster di Service Fabric per poter distribuire Gestione API. 

Per questa esercitazione, il modello di Resource Manager di Gestione API è preconfigurato per l'uso dei nomi di rete virtuale, subnet e gruppo NSG impostati nel passaggio precedente. 

 1. Scaricare il modello e il file di parametri di Resource Manager seguenti:
 
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

 2. Specificare i parametri vuoti nel file `apim.parameters.json` per la distribuzione.

 3. Per distribuire il modello e il file di parametri di Resource Manager per Gestione API, usare il comando PowerShell seguente:

    ```powershell
    PS > New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>Configurare Gestione API

Dopo aver distribuito Gestione API e il cluster di Service Fabric, è possibile configurare un back-end di Service Fabric in Gestione API. Questo consente di creare un criterio del servizio back-end che invia il traffico al cluster di Service Fabric.

### <a name="api-management-security"></a>Sicurezza di Gestione API

Per configurare il back-end di Service Fabric, è innanzitutto necessario configurare le impostazioni di sicurezza di Gestione API. A questo scopo, passare al servizio Gestione API nel portale di Azure.

#### <a name="enable-the-api-management-rest-api"></a>Abilitare l'API REST di Gestione API

L'API REST di Gestione API rappresenta al momento l'unico modo per configurare un servizio back-end. È innanzitutto necessario abilitare l'API REST di Gestione API e proteggerla.

 1. Nel servizio Gestione API selezionare **Gestione API - ANTEPRIMA** in **Sicurezza**.
 2. Selezionare la casella di controllo **Abilita API REST di Gestione API**.
 3. Prendere nota dell'URL dell'API di gestione, ovvero l'URL che verrà usato successivamente per configurare il back-end di Service Fabric.
 4. Generare un **token di accesso** selezionando una data di scadenza e una chiave, quindi fare clic sul pulsante **Genera** nella parte inferiore della pagina.
 5. Copiare il **token di accesso** e salvarlo per usarlo nei passaggi successivi. Tenere presente che questo token è diverso dalla chiave primaria e dalla chiave secondaria.

#### <a name="upload-a-service-fabric-client-certificate"></a>Caricare un certificato client di Service Fabric

Gestione API deve eseguire l'autenticazione con il cluster di Service Fabric per individuare il servizio mediante un certificato client che dispone di accesso al cluster. Per semplicità, in questa esercitazione viene usato lo stesso certificato specificato durante la creazione del cluster di Service Fabric, che per impostazione predefinita può essere usato per accedere al cluster.

 1. Nel servizio Gestione API selezionare **Certificati client - ANTEPRIMA** in **Sicurezza**.
 2. Fare clic sul pulsante **+ Aggiungi**.
 2. Selezionare il file di chiave privata (con estensione pfx) del certificato del cluster specificato durante la creazione del cluster di Service Fabric, assegnargli un nome e immettere la password della chiave privata.

> [!NOTE]
> In questa esercitazione viene usato lo stesso certificato per l'autenticazione client e la sicurezza del cluster da nodo a nodo. È possibile usare un certificato client separato se ne è stato configurato uno per l'accesso al cluster di Service Fabric.

### <a name="configure-the-backend"></a>Configurare il back-end

Dopo aver configurato la sicurezza di Gestione API, è possibile configurare il back-end di Service Fabric. Per i back-end di Service Fabric, il cluster di Service Fabric è il back-end, anziché uno specifico servizio di Service Fabric. Questo consente di eseguire il routing di un singolo criterio in più servizi del cluster.

Per eseguire questo passaggio è necessario specificare il token di accesso generato prima e l'identificazione personale per il certificato cluster caricato in Gestione API nel passaggio precedente.

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

#### <a name="example"></a>Esempio

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
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
 2. Nel pannello del servizio Gestione API selezionare **API - Anteprima**.
 3. Aggiungere una nuova API facendo sulla casella **API vuota** e completare la finestra di dialogo:

     - **URL del servizio Web**: per i back-end di Service Fabric, questo valore URL non viene usato. È possibile inserire un valore qualsiasi nel campo. Per questa esercitazione, usare `http://servicefabric`.
     - **Nome**: inserire il nome dell'API. Per questa esercitazione, usare `Service Fabric App`.
     - **Schema URL**: selezionare HTTP, HTTPS o entrambi i valori. Per questa esercitazione, usare `both`.
     - **Suffisso URL API**: inserire un suffisso per l'API. Per questa esercitazione, usare `myapp`.
 
 4. Una volta creata l'API, fare clic su **+ Aggiungi operazione** per aggiungere un'operazione API front-end. Compilare i valori come indicato di seguito.
    
     - **URL**: selezionare `GET` e specificare un percorso URL per l'API. Per questa esercitazione, usare `/api/values`.
     
       Per impostazione predefinita, il percorso URL specificato qui è il percorso URL inviato al servizio Service Fabric di back-end. Se si specifica lo stesso percorso URL usato dal servizio, in questo caso `/api/values`, non è necessario apportare ulteriori modifiche. È anche possibile specificare un percorso URL diverso da quello usato dal servizio Service Fabric di back-end. In questo caso, sarà necessario immettere successivamente anche un'indicazione di riscrittura del percorso nel criterio dell'operazione.
     - **Nome visualizzato**: specificare un nome per l'API. Per questa esercitazione, usare `Values`.

## <a name="configure-a-backend-policy"></a>Configurare un criterio di back-end

Il criterio di back-end collega tutti gli elementi e consente di configurare il servizio Service Fabric di back-end a cui vengono indirizzate le richieste. È possibile applicare questo criterio a qualsiasi operazione API. La [configurazione back-end per Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) fornisce i controlli di indirizzamento delle richieste seguenti: 
 - Selezione dell'istanza del servizio mediante la specifica di un nome di istanza del servizio Service Fabric, hardcoded (ad esempio, `"fabric:/myapp/myservice"`) o generato dalla richiesta HTTP (ad esempio, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Risoluzione della partizione mediante la generazione di una chiave di partizione che usa uno schema di partizionamento di Service Fabric.
 - Selezione della replica per i servizi con stato.
 - Condizioni per i nuovi tentativi di risoluzione della posizione di un servizio e il nuovo invio di una richiesta.

Per questa esercitazione, creare un criterio di back-end che consenta di indirizzare le richieste direttamente al servizio ASP.NET Core senza stato distribuito in precedenza:

 1. Selezionare e modificare i **criteri in ingresso** per l'operazione `Values` facendo clic sull'icona di modifica, quindi scegliere **Visualizzazione Codice**.
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

### <a name="add-the-api-to-a-product"></a>Aggiungere l'API a un prodotto. 

Prima di chiamare l'API, è innanzitutto necessario aggiungerla a un prodotto per il quale sia possibile concedere l'accesso agli utenti. 

 1. Nel servizio Gestione API selezionare **Prodotti - ANTEPRIMA**.
 2. Per impostazione predefinita, Gestione API include due prodotti: Starter e Illimitato. Selezionare il prodotto Illimitato.
 3. Selezionare API.
 4. Fare clic sul pulsante **+ Aggiungi**.
 5. Selezionare l'API `Service Fabric App` creata nei passaggi precedenti e fare clic sul pulsante **Seleziona**.

### <a name="test-it"></a>Eseguirne il test

È ora possibile provare a inviare una richiesta al servizio back-end in Service Fabric tramite Gestione API direttamente dal portale di Azure.

 1. Nel servizio Gestione API selezionare **API - ANTEPRIMA**.
 2. Selezionare l'API `Service Fabric App` creata nei passaggi precedenti e fare clic sul pulsante **Test**.
 3. Fare clic sul pulsante **Invia** per inviare una richiesta di test al servizio back-end.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dovrebbe essere disponibile un'installazione di base con i servizi Service Fabric e Gestione API.

In questa esercitazione viene usata l'autenticazione utente basata su certificato per il cluster di Service Fabric per consentire una procedura di configurazione rapida. È consigliabile eseguire un'autenticazione utente più avanzata per il cluster di Service Fabric basata su [Azure Active Directory](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication). 

Successivamente, [creare e configurare impostazioni di prodotto avanzate in Gestione API di Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules) per preparare l'applicazione al traffico reale.

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png

