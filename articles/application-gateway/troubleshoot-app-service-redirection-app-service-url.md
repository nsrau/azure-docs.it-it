---
title: Risoluzione dei problemi relativi a applicazione Azure gateway con il servizio app-reindirizzamento all'URL del servizio app
description: Questo articolo fornisce informazioni su come risolvere il problema di reindirizzamento quando applicazione Azure gateway viene usato con app Azure servizio
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347875"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Risolvere i problemi del servizio app nel gateway applicazione

Informazioni su come diagnosticare e risolvere i problemi riscontrati quando il server app viene usato come destinazione back-end con il gateway applicazione

## <a name="overview"></a>Panoramica

In questo articolo si apprenderà come risolvere i problemi seguenti:

> [!div class="checklist"]
> * L'URL del servizio app verrà esposto nel browser quando è presente un reindirizzamento
> * Il dominio del cookie ARRAffinity del servizio app è impostato sul nome host del servizio app (example.azurewebsites.net) anziché sull'host originale

Quando un'applicazione back-end invia una risposta di reindirizzamento, potrebbe essere necessario reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. Ad esempio, è possibile eseguire questa operazione quando un servizio app è ospitato dietro un gateway applicazione e richiede al client di eseguire un reindirizzamento al percorso relativo. Ad esempio, un Reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2. Quando il servizio app invia una risposta di reindirizzamento, USA lo stesso nome host nell'intestazione Location della risposta come quello nella richiesta ricevuta dal gateway applicazione. Quindi, il client effettuerà la richiesta direttamente a contoso.azurewebsites.net/path2 anziché passare attraverso il gateway applicazione (contoso.com/path2). Non è consigliabile ignorare il gateway applicazione.

Questo problema può verificarsi a causa dei motivi principali seguenti:

- È stato configurato il reindirizzamento nel servizio app. Il reindirizzamento può essere semplice quanto aggiungere una barra finale alla richiesta.
- Si dispone di Azure AD autenticazione che causa il reindirizzamento.

Inoltre, quando si usano i servizi app dietro il gateway applicazione, il nome di dominio associato al gateway applicazione (example.com) sarà diverso dal nome di dominio del servizio app (ad indicare example.azurewebsites.net) perché si noterà che il valore di dominio per il cookie ARRAffinity impostato dal servizio app conterrà il nome di dominio "example.azurewebsites.net", che non è auspicabile. Il nome host originale (example.com) deve essere il valore del nome di dominio nel cookie.

## <a name="sample-configuration"></a>Configurazione di esempio

- Listener HTTP: Di base o multisito
- Pool di indirizzi back-end: Servizio app
- Impostazioni HTTP: "Scegli nome host dall'indirizzo back-end" abilitato
- Sonda "Seleziona nome host da impostazioni HTTP" abilitato

## <a name="cause"></a>Causa

Poiché il servizio app è un servizio multi-tenant, usa l'intestazione host nella richiesta per instradare la richiesta all'endpoint corretto. Tuttavia, il nome di dominio predefinito dei servizi app, *. azurewebsites.net (Say contoso.azurewebsites.net), è diverso dal nome di dominio del gateway applicazione (ad indicare contoso.com). Poiché la richiesta originale del client ha il nome di dominio del gateway applicazione (contoso.com) come nome host, è necessario configurare il gateway applicazione per modificare il nome host nella richiesta originale al nome host di servizi app quando instrada la richiesta a back-end del servizio app.  A tale scopo, usare l'opzione "pick hostname from backend Address" nella configurazione dell'impostazione HTTP del gateway applicazione e l'opzione "pick hostname from backend HTTP Settings" nella configurazione del probe di integrità.



![Appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

A causa di questo, quando il servizio app esegue un reindirizzamento, usa il nome host sottoposto a override "contoso.azurewebsites.net" nell'intestazione Location, anziché il nome host originale "contoso.com", a meno che non sia configurato diversamente. È possibile controllare le intestazioni di richiesta e risposta di esempio di seguito.
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
Nell'esempio precedente, è possibile notare che l'intestazione della risposta ha un codice di stato 301 per il reindirizzamento e che l'intestazione Location contiene il nome host del servizio app anziché il nome host originale "www.contoso.com".

## <a name="solution-rewrite-the-location-header"></a>Soluzione: Riscrivere l'intestazione Location

Sarà necessario impostare il nome host nell'intestazione Location sul nome di dominio del gateway applicazione. A tale scopo, creare una [regola](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) di riscrittura con una condizione che valuti se l'intestazione Location nella risposta contiene azurewebsites.NET ed esegue un'azione per riscrivere l'intestazione location in modo da avere il nome host del gateway applicazione.  Vedere le istruzioni su [come riscrivere l'intestazione Location](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Il supporto per la riscrittura dell'intestazione HTTP è disponibile solo per lo [SKU Standard_V2 e WAF_v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) del gateway applicazione. Se si usa lo SKU V1, si consiglia vivamente di eseguire la [migrazione da V1 a V2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) per poter usare la riscrittura e altre [funzionalità avanzate](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) disponibili con lo SKU V2.

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>Soluzione alternativa: Usa il dominio personalizzato del servizio app anziché il nome di dominio predefinito

Se si usa lo SKU V1, non sarà possibile riscrivere l'intestazione Location perché questa funzionalità è disponibile solo per lo SKU V2. Per risolvere il problema del reindirizzamento, quindi, è necessario passare lo stesso nome host ricevuto dal gateway applicazione anche al servizio app, anziché eseguire una sostituzione dell'host.

Una volta eseguita questa operazione, il servizio app effettuerà il reindirizzamento (se presente) nella stessa intestazione host originale che punta al gateway applicazione e non a se stessa.

A tale scopo, è necessario essere proprietari di un dominio personalizzato e attenersi alla procedura indicata di seguito.

- Registrare il dominio nell'elenco di domini personalizzati del servizio app. A tale proposito, è necessario che nel dominio personalizzato sia presente un record CNAME che punta al nome di dominio completo del servizio app. Per altre informazioni, vedere [eseguire il mapping di un nome DNS personalizzato esistente al servizio app Azure](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![Appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Al termine, il servizio app è pronto ad accettare il nome host "www.contoso.com". Modificare ora la voce CNAME in DNS per puntare di nuovo al nome di dominio completo del gateway applicazione. Ad esempio, "appgw.eastus.cloudapp.azure.com".

- Quando si esegue una query DNS, verificare che il dominio "www.contoso.com" venga risolto nel nome di dominio completo del gateway applicazione.

- Impostare il probe personalizzato per disabilitare "pick hostname from backend HTTP Settings". Questa operazione può essere eseguita dal portale deselezionando la casella di controllo nelle impostazioni del probe e in PowerShell senza usare l'opzione-PickHostNameFromBackendHttpSettings nel comando set-AzApplicationGatewayProbeConfig. Nel campo hostname del probe immettere il nome di dominio completo del servizio app "example.azurewebsites.net", perché le richieste di probe inviate dal gateway applicazione porteranno questo nell'intestazione host.

  > [!NOTE]
  > Quando si esegue il passaggio successivo, assicurarsi che il probe personalizzato non sia associato alle impostazioni HTTP back-end perché per le impostazioni HTTP è ancora abilitato l'opzione "Seleziona nome host da indirizzo back-end".

- Impostare le impostazioni HTTP del gateway applicazione per disabilitare "Seleziona nome host dall'indirizzo back-end". Questa operazione può essere eseguita dal portale deselezionando la casella di controllo e in PowerShell senza usare l'opzione-PickHostNameFromBackendAddress nel comando set-AzApplicationGatewayBackendHttpSettings.

- Associare il probe personalizzato di nuovo alle impostazioni HTTP back-end e verificare l'integrità back-end se è integro.

- Al termine di questa operazione, il gateway applicazione deve ora trasmettere lo stesso nome host "www.contoso.com" al servizio app e il reindirizzamento si verificherà nello stesso nome host. È possibile controllare le intestazioni di richiesta e risposta di esempio di seguito.

Per implementare i passaggi descritti in precedenza usando PowerShell per un'installazione esistente, seguire lo script di PowerShell di esempio riportato di seguito. Si noti che non sono state usate le opzioni-PickHostname nella configurazione delle impostazioni di probe e HTTP.

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

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).
