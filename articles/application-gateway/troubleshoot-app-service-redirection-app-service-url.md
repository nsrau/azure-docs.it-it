---
title: Risolvere i problemi relativi al reindirizzamento all'URL del servizio appTroubleshoot redirection to App Service URL
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce informazioni su come risolvere il problema di reindirizzamento quando viene usato il gateway applicazione di Azure con il servizio app di AzureThis article provides information on how to troubleshoot the redirection issue when Azure Application Gateway is used with Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293532"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Risolvere i problemi del servizio app nel gateway applicazioneTroubleshoot App Service issues in Application Gateway

Informazioni su come diagnosticare e risolvere i problemi che possono verificarsi quando il servizio app di Azure viene usato come destinazione back-end con il gateway applicazione di Azure.Learn how to diagnose and resolve issues you might encounter when Azure App Service is used as a back-end target with Azure Application Gateway.

## <a name="overview"></a>Panoramica

In questo articolo verrà illustrato come risolvere i problemi seguenti:In this article, you'll learn how to troubleshoot the following issues:

> [!div class="checklist"]
> * L'URL del servizio app viene esposto nel browser quando è presente un reindirizzamento.
> * Il dominio del cookie ARRAffinity del servizio app è impostato sul nome host del servizio app, example.azurewebsites.net, anziché sull'host originale.

Quando un'applicazione back-end invia una risposta di reindirizzamento, è possibile reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. È possibile eseguire questa operazione quando un servizio app è ospitato dietro un gateway applicazione e richiede al client di eseguire un reindirizzamento al relativo percorso. Un esempio è un reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2. 

Quando il servizio app invia una risposta di reindirizzamento, usa lo stesso nome host nell'intestazione del percorso della risposta di quella nella richiesta che riceve dal gateway applicazione. Ad esempio, il client effettua la richiesta direttamente per contoso.azurewebsites.net/path2 anziché passare attraverso il gateway applicazione contoso.com/path2. Non si desidera ignorare il gateway applicazione.

Questo problema può verificarsi per i seguenti motivi principali:

- Il reindirizzamento è stato configurato nel servizio app. Il reindirizzamento può essere semplice come aggiungere una barra finale alla richiesta.
- Si dispone dell'autenticazione di Azure Active Directory, che causa il reindirizzamento.

Inoltre, quando si usano i servizi app dietro un gateway applicazione, il nome di dominio associato al gateway applicazione (example.com) è diverso dal nome di dominio del servizio app (ad esempio, example.azurewebsites.net). Il valore di dominio per il cookie ARRAffinity impostato dal servizio app contiene il nome di dominio example.azurewebsites.net, che non è auspicabile. Il nome host originale, example.com, deve essere il valore del nome di dominio nel cookie.

## <a name="sample-configuration"></a>Configurazione di esempio

- Listener HTTP: Basic o multisito
- Pool di indirizzi back-end: servizio app
- Impostazioni HTTP: **selezionare Il nome host dall'indirizzo back-end** abilitato
- Probe: **selezionare il nome host dalle impostazioni HTTP** abilitate

## <a name="cause"></a>Causa

Il servizio app è un servizio multi-tenant, pertanto usa l'intestazione host nella richiesta per instradare la richiesta all'endpoint corretto. Il nome di dominio predefinito di App Services, azurewebsites.net ovvero contoso.azurewebsites.net, è diverso dal nome di dominio del gateway applicazione (ad esempio, contoso.com). 

La richiesta originale dal client ha il nome di dominio del gateway applicazione, contoso.com, come nome host. È necessario configurare il gateway applicazione per modificare il nome host nella richiesta originale con il nome host del servizio app quando instrada la richiesta al back-end del servizio app. Utilizzare l'opzione **Seleziona nome host dall'indirizzo back-end** nella configurazione delle impostazioni HTTP del gateway applicazione. Utilizzare l'opzione **Seleziona nome host da impostazioni HTTP back-end** nella configurazione del probe di integrità.



![Il gateway applicazione modifica il nome host](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Quando il servizio app esegue un reindirizzamento, usa il nome host sottoposto a override contoso.azurewebsites.net nell'intestazione del percorso anziché il nome host originale contoso.com, a meno che non sia configurato diversamente. Controllare le intestazioni di richiesta e risposta di esempio seguenti.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
Nell'esempio precedente, si noti che l'intestazione della risposta ha un codice di stato 301 per il reindirizzamento. L'intestazione del percorso ha il nome host `www.contoso.com`del servizio app anziché il nome host originale.

## <a name="solution-rewrite-the-location-header"></a>Soluzione: riscrivere l'intestazione della posizione

Impostare il nome host nell'intestazione del percorso sul nome di dominio del gateway applicazione. A tale scopo, creare una regola di [riscrittura](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) con una condizione che valuta se l'intestazione del percorso nella risposta contiene azurewebsites.net. Deve inoltre eseguire un'azione per riscrivere l'intestazione della posizione in modo che abbia il nome host del gateway applicazione. Per ulteriori informazioni, vedere le istruzioni su [come riscrivere l'intestazione di posizione](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Il supporto per la riscrittura dell'intestazione HTTP è disponibile solo per il [Standard_v2 e WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) del gateway applicazione. Se si utilizza lo SKU v1, è consigliabile [eseguire la migrazione dalla versione v1 alla v2.](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) Si desidera utilizzare la riscrittura e altre [funzionalità avanzate](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) disponibili con lo SKU v2.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Soluzione alternativa: usare un nome di dominio personalizzatoAlternate solution: Use a custom domain name

Se si utilizza lo SKU v1, non è possibile riscrivere l'intestazione del percorso. Questa funzionalità è disponibile solo per sKU v2. Per risolvere il problema di reindirizzamento, passare lo stesso nome host che il gateway applicazione riceve anche al servizio app, anziché eseguire un override dell'host.

Il servizio app esegue ora il reindirizzamento (se presente) nella stessa intestazione host originale che punta al gateway applicazione e non al proprio.

È necessario possedere un dominio personalizzato e seguire questa procedura:You must own a custom domain and follow this process:

- Registrare il dominio nell'elenco di domini personalizzato del servizio app. È necessario disporre di un CNAME nel dominio personalizzato che punta al nome di dominio completo del servizio app. Per altre informazioni, vedere Eseguire il mapping di un nome DNS personalizzato esistente al servizio app di Azure.For more information, see [Map an existing custom DNS name to Azure App Service.](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)

    ![Elenco di domini personalizzati del servizio app](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Il servizio app è pronto `www.contoso.com`per accettare il nome host. Modificare la voce CNAME nel DNS in modo che punti al `appgw.eastus.cloudapp.azure.com`nome di dominio completo del gateway applicazione, ad esempio .

- Assicurarsi che `www.contoso.com` il dominio venga risolto nel nome di dominio completo del gateway applicazione quando si esegue una query DNS.

- Impostare il probe personalizzato per disabilitare **Seleziona nome host dalle impostazioni HTTP back-end**. Nel portale di Azure deselezionare la casella di controllo nelle impostazioni del probe. In PowerShell non usare l'opzione **-PickHostNameFromBackendHttpSettings** nel comando **Set-AzApplicationGatewayProbeConfig.** Nel campo del nome host del probe immettere il nome di dominio completo del servizio app example.azurewebsites.net. Le richieste probe inviate dal gateway applicazione contengono questo FQDN nell'intestazione host.

  > [!NOTE]
  > Per il passaggio successivo, assicurarsi che il probe personalizzato non sia associato alle impostazioni HTTP back-end. Le impostazioni HTTP hanno ancora l'opzione **Seleziona nome host dall'indirizzo back-end** abilitato a questo punto.

- Impostare le impostazioni HTTP del gateway applicazione per disabilitare **Seleziona nome host dall'indirizzo back-end**. Nel portale di Azure deselezionare la casella di controllo. In PowerShell non usare l'opzione **-PickHostNameFromBackendAddress** nel comando **Set-AzApplicationGatewayBackendHttpSettings.**

- Associare il probe personalizzato alle impostazioni HTTP back-end e verificare che il back-end sia integro.

- Il gateway applicazione dovrebbe ora inoltrare lo stesso nome host, `www.contoso.com`, al servizio app. Il reindirizzamento avviene sullo stesso nome host. Controllare le intestazioni di richiesta e risposta di esempio seguenti.

Per implementare i passaggi precedenti usando PowerShell per un'installazione esistente, usare lo script di PowerShell di esempio seguente. Si noti come non sono stati utilizzati le opzioni **-PickHostname** nella configurazione delle impostazioni probe e HTTP.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un ticket di [supporto.](https://azure.microsoft.com/support/options/)
