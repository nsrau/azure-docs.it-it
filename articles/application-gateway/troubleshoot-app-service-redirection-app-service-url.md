---
title: Risoluzione dei problemi relativi a applicazione Azure gateway con il servizio app-reindirizzamento all'URL del servizio app
description: Questo articolo fornisce informazioni su come risolvere il problema di reindirizzamento quando applicazione Azure gateway viene usato con app Azure servizio
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: ef2bbf8804e96a3e25f053d189c6d85bfa845b0b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833181"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Risolvere i problemi del servizio app nel gateway applicazione

Informazioni su come diagnosticare e risolvere i problemi che possono verificarsi quando app Azure servizio viene usato come destinazione back-end con applicazione Azure gateway.

## <a name="overview"></a>Panoramica

In questo articolo si apprenderà come risolvere i problemi seguenti:

> [!div class="checklist"]
> * L'URL del servizio app viene esposto nel browser quando è presente un reindirizzamento.
> * Il dominio del cookie ARRAffinity del servizio app è impostato sul nome host del servizio app, example.azurewebsites.net, anziché sull'host originale.

Quando un'applicazione back-end invia una risposta di reindirizzamento, potrebbe essere necessario reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. Questa operazione può essere eseguita quando un servizio app è ospitato dietro un gateway applicazione e richiede che il client esegua un reindirizzamento al percorso relativo. Un esempio è un Reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2. 

Quando il servizio app invia una risposta di reindirizzamento, USA lo stesso nome host nell'intestazione Location della risposta come quello nella richiesta ricevuta dal gateway applicazione. Ad esempio, il client effettua la richiesta direttamente a contoso.azurewebsites.net/path2 anziché passare attraverso il contoso.com/path2 del gateway applicazione. Non si vuole ignorare il gateway applicazione.

Questo problema può verificarsi per i motivi principali seguenti:

- È stato configurato il reindirizzamento nel servizio app. Il reindirizzamento può essere semplice quanto aggiungere una barra finale alla richiesta.
- Si dispone di Azure Active Directory autenticazione, che causa il reindirizzamento.

Inoltre, quando si usano i servizi app dietro un gateway applicazione, il nome di dominio associato al gateway applicazione (example.com) è diverso dal nome di dominio del servizio app (ad indicare, example.azurewebsites.net). Il valore di dominio per il cookie ARRAffinity impostato dal servizio app contiene il nome di dominio example.azurewebsites.net, che non è auspicabile. Il nome host originale, example.com, deve essere il valore del nome di dominio nel cookie.

## <a name="sample-configuration"></a>Configurazione di esempio

- Listener HTTP: di base o multisito
- Pool di indirizzi back-end: servizio app
- Impostazioni HTTP: **selezionare il nome host dall'indirizzo back-end** abilitato
- Probe: **selezionare il nome host da impostazioni http** abilitate

## <a name="cause"></a>Causa

Il servizio app è un servizio multi-tenant, quindi usa l'intestazione host nella richiesta per instradare la richiesta all'endpoint corretto. Il nome di dominio predefinito dei servizi app, *. azurewebsites.net (Say, contoso.azurewebsites.net), è diverso dal nome di dominio del gateway applicazione (ad indicare, contoso.com). 

La richiesta originale del client ha il nome di dominio del gateway applicazione, contoso.com, come nome host. È necessario configurare il gateway applicazione per modificare il nome host nella richiesta originale con il nome host del servizio app quando instrada la richiesta al back-end del servizio app. Usare l'opzione **Seleziona nome host dall'indirizzo back-end** nella configurazione dell'impostazione http del gateway applicazione. Usare l'opzione **Seleziona nome host da impostazioni http back-end** nella configurazione del probe di integrità.



![Nome host modifiche del gateway applicazione](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Quando il servizio app esegue un reindirizzamento, usa il nome host sottoposto a override contoso.azurewebsites.net nell'intestazione Location anziché il nome host originale contoso.com, a meno che non sia configurato diversamente. Controllare le intestazioni di richiesta e risposta di esempio seguenti.
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
Nell'esempio precedente, si noti che l'intestazione della risposta ha un codice di stato 301 per il reindirizzamento. L'intestazione Location ha il nome host del servizio app anziché il nome host originale `www.contoso.com`.

## <a name="solution-rewrite-the-location-header"></a>Soluzione: riscrivere l'intestazione Location

Impostare il nome host nell'intestazione Location sul nome di dominio del gateway applicazione. A tale scopo, creare una [regola di riscrittura](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) con una condizione che valuti se l'intestazione Location nella risposta contiene azurewebsites.NET. Deve anche eseguire un'azione per riscrivere l'intestazione location in modo da avere il nome host del gateway applicazione. Per ulteriori informazioni, vedere le istruzioni su [come riscrivere l'intestazione Location](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Il supporto per la riscrittura dell'intestazione HTTP è disponibile solo per le [Standard_v2 e WAF_V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) del gateway applicazione. Se si usa lo SKU V1, è consigliabile [eseguire la migrazione da V1 a V2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2). Si desidera utilizzare riscrittura e altre [funzionalità avanzate](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) disponibili con lo SKU V2.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Soluzione alternativa: usare un nome di dominio personalizzato

Se si usa lo SKU V1, non è possibile riscrivere l'intestazione Location. Questa funzionalità è disponibile solo per lo SKU V2. Per risolvere il problema di reindirizzamento, passare lo stesso nome host che il gateway applicazione riceve anche al servizio app, anziché eseguire una sostituzione dell'host.

Il servizio app ora esegue il reindirizzamento, se presente, sulla stessa intestazione host originale che punta al gateway applicazione e non a se stessa.

È necessario essere proprietari di un dominio personalizzato e seguire questa procedura:

- Registrare il dominio nell'elenco di domini personalizzati del servizio app. È necessario avere un record CNAME nel dominio personalizzato che punti al nome di dominio completo del servizio app. Per altre informazioni, vedere [eseguire il mapping di un nome DNS personalizzato esistente al servizio app Azure](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

    ![Elenco di domini personalizzati del servizio app](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Il servizio app è pronto per accettare il nome host `www.contoso.com`. Modificare la voce CNAME in DNS per puntare di nuovo al nome di dominio completo del gateway applicazione, ad esempio `appgw.eastus.cloudapp.azure.com`.

- Verificare che il dominio `www.contoso.com` venga risolto nel nome di dominio completo del gateway applicazione quando si esegue una query DNS.

- Impostare il probe personalizzato per disabilitare **pick hostname dalle impostazioni http back-end**. Nella portale di Azure deselezionare la casella di controllo nelle impostazioni del probe. In PowerShell non usare l'opzione **-PickHostNameFromBackendHttpSettings** nel comando **set-AzApplicationGatewayProbeConfig** . Nel campo nome host del probe immettere il nome di dominio completo del servizio app, example.azurewebsites.net. Le richieste di probe inviate dal gateway applicazione contengono questo FQDN nell'intestazione host.

  > [!NOTE]
  > Per il passaggio successivo, assicurarsi che il probe personalizzato non sia associato alle impostazioni HTTP del back-end. Le impostazioni HTTP hanno ancora l'opzione **Seleziona nome host da indirizzo back-end** abilitata a questo punto.

- Impostare le impostazioni HTTP del gateway applicazione per disabilitare **pick hostname dall'indirizzo back-end**. Nella portale di Azure deselezionare la casella di controllo. In PowerShell non usare l'opzione **-PickHostNameFromBackendAddress** nel comando **set-AzApplicationGatewayBackendHttpSettings** .

- Associare nuovamente il probe personalizzato alle impostazioni HTTP back-end e verificare che il back-end sia integro.

- Il gateway applicazione deve ora trasmettere lo stesso nome host, `www.contoso.com`, al servizio app. Il reindirizzamento si verifica sullo stesso nome host. Controllare le intestazioni di richiesta e risposta di esempio seguenti.

Per implementare i passaggi precedenti usando PowerShell per un'installazione esistente, usare lo script di PowerShell di esempio seguente. Si noti che non sono state usate le opzioni **-PickHostname** nella configurazione delle impostazioni di probe e http.

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

Se la procedura precedente non risolve il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).
