---
title: Uso di PowerShell per Gestione traffico in Azure
description: Uso di PowerShell per Gestione traffico con Azure Resource Manager
services: traffic-manager
documentationcenter: na
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: allensu
ms.openlocfilehash: a2065ba51b74d7f55464a22df0f55cac4c6defcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071036"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Uso di PowerShell per Gestione traffico

Azure Resource Manager è l'interfaccia di gestione preferita dei servizi in Azure. I profili di Gestione traffico di Azure possono ora essere gestiti usando le API e gli strumenti basati su Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Modello di risorsa

Gestione traffico di Azure viene configurato utilizzando una serie di impostazioni denominate "profilo di Gestione traffico". Questo profilo include le impostazioni DNS, di routing del traffico, di monitoraggio dell'endpoint e un elenco degli endpoint di servizio a cui viene indirizzato il traffico.

Ogni profilo di Gestione traffico è rappresentato da una risorsa di tipo "TrafficManagerProfiles". A livello di API REST, l'URI per ogni profilo è analogo al seguente:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Configurazione di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In queste istruzioni viene usato Microsoft Azure PowerShell. L'articolo seguente illustra come installare e configurare Azure PowerShell.

* [Come installare e configurare Azure PowerShell](/powershell/azure/overview)

Negli esempi inclusi in questo articolo si presuppone che esista un gruppo di risorse. È possibile creare un gruppo di risorse usando il comando seguente:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager richiede che tutti i gruppi di risorse specifichino un percorso, che viene usato come percorso predefinito per le risorse create in questo gruppo di risorse. Tuttavia, dal momento che tutte le risorse del profilo di Gestione traffico sono globali (non locali), la scelta del percorso relativo al gruppo di risorse non ha alcun impatto sul servizio Gestione traffico di Azure.

## <a name="create-a-traffic-manager-profile"></a>Creazione di un profilo di Gestione traffico

Per creare un profilo di Gestione traffico, usare il cmdlet `New-AzTrafficManagerProfile`:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

La tabella seguente descrive i parametri:

| Parametro | Descrizione |
| --- | --- |
| Name |Il nome della risorsa per la risorsa del profilo di Gestione traffico. I profili dello stesso gruppo di risorse devono disporre di nomi univoci. Tale nome è diverso rispetto a quello DNS utilizzato per le query DNS. |
| ResourceGroupName |Il nome del gruppo di risorse che include la risorsa del profilo. |
| TrafficRoutingMethod |Indica il metodo di routing del traffico usato per determinare quale endpoint viene restituito in risposta alle query DNS. I valori possibili sono "Performance", "'Weighted" e "Priority". |
| RelativeDnsName |Indica la parte hostname del nome DNS fornito da questo profilo di Gestione traffico. Questo valore viene combinato con il nome del dominio DNS usato da Gestione traffico di Azure per formare il nome di dominio completo del profilo. Ad esempio, l'impostazione del valore di "contoso" diventa "contoso.trafficmanager.net". |
| TTL |Specifica la durata (TTL) DNS in secondi. In questo modo, i resolver locali e i client DNS sono informati sulla durata della memorizzazione nella cache delle risposte DNS per il profilo di Gestione traffico. |
| MonitorProtocol |Indica il protocollo da usare per monitorare lo stato di integrità dell'endpoint. I valori possibili sono "HTTP" e "HTTPS". |
| MonitorPort |Indica la porta TCP da usare per monitorare lo stato di integrità dell'endpoint. |
| MonitorPath |Indica il percorso relativo al nome di dominio dell'endpoint usato per verificare l'integrità dell'endpoint. |

Il cmdlet consente di creare un profilo di Gestione traffico in Azure e restituisce un oggetto di profilo corrispondente a PowerShell. A questo punto, il profilo non contiene endpoint. Per altre informazioni sull'aggiunta di endpoint a un profilo di Gestione traffico, vedere Aggiunta di endpoint a Gestione traffico.

## <a name="get-a-traffic-manager-profile"></a>Visualizzazione di un profilo di Gestione traffico

Per recuperare un oggetto profilo di Gestione traffico esistente, usare il cmdlet `Get-AzTrafficManagerProfle`:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Questo cmdlet restituisce un oggetto profilo di Gestione traffico.

## <a name="update-a-traffic-manager-profile"></a>Aggiornare un profilo di Gestione traffico

La modifica dei profili di Gestione traffico prevede un processo in 3 passaggi:

1. Recuperare il profilo usando `Get-AzTrafficManagerProfile` oppure usare il profilo restituito da `New-AzTrafficManagerProfile`.
2. Modificare il profilo. È possibile aggiungere e rimuovere endpoint o modificare i parametri dell'endpoint o del profilo. Queste modifiche sono operazioni offline. Si sta modificando solo l'oggetto locale in memoria che rappresenta il profilo.
3. Confermare le modifiche usando il cmdlet `Set-AzTrafficManagerProfile`.

Tutte le proprietà del profilo possono essere modificate, ad eccezione del valore RelativeDnsName del profilo. Per modificare RelativeDnsName, è necessario eliminare il profilo e creare un nuovo profilo con un nuovo nome.

L'esempio seguente illustra come cambiare la durata (TTL) del profilo:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Sono disponibili tre tipi di endpoint di Gestione traffico:

1. I servizi **Endpoint di Azure** sono ospitati hosting in Azure
2. I servizi **Endpoint esterni** sono ospitati all'esterno di Azure
3. Gli **endpoint annidati** vengono usati per creare gerarchie annidate dei profili di Gestione traffico. Gli endpoint annidati consentono configurazioni avanzate del routing del traffico per applicazioni complesse.

In tutti e tre i casi è possibile aggiungere gli endpoint in due modi:

1. Usando il processo in 3 passaggi descritto in precedenza. Il vantaggio di questo metodo consiste nel fatto che è possibile apportare diverse modifiche agli endpoint in un singolo aggiornamento.
2. Mediante il cmdlet New-AzTrafficManagerEndpoint. Questo cmdlet aggiunge un endpoint a un profilo di Gestione traffico esistente in una singola operazione.

## <a name="adding-azure-endpoints"></a>Aggiunta di endpoint di Azure

Gli endpoint di Azure fanno riferimento ai servizi ospitati in Azure. Sono attualmente supportati due tipi di endpoint di Azure:

1. Servizio app di Azure
2. Risorse di tipo publicIpAddress di Azure, che possono essere associate a un servizio di bilanciamento del carico o a una scheda di interfaccia di rete di una macchina virtuale. La risorsa di tipo PublicIpAddress deve avere un nome DNS assegnato da usare in Gestione traffico.

In ogni caso:

* Il servizio viene specificato usando il parametro "targetResourceId" di `Add-AzTrafficManagerEndpointConfig` o `New-AzTrafficManagerEndpoint`.
* TargetResourceId usa in modo implicito "Target" ed "EndpointLocation".
* "Weight" è facoltativo ed è possibile scegliere se specificarlo. I pesi vengono usati solo se il profilo è configurato per l'uso del metodo di routing del traffico "Weighted". In caso contrario, vengono ignorati. Il valore deve essere un numero compreso tra 1 e 1000. il cui valore predefinito è 1.
* "Priority" è facoltativo ed è possibile scegliere se specificarlo. Le priorità vengono usate solo se il profilo è configurato per l'uso del metodo di routing del traffico "Priority". In caso contrario, vengono ignorate. I valori validi sono compresi tra 1 e 1000 con i valori più bassi che indicano una priorità più alta. Se si specifica questo valore per un endpoint, sarà necessario specificarlo per tutti gli endpoint. Se questo valore viene omesso, verranno applicati i valori predefiniti a partire da "1" nell'ordine in cui sono elencati gli endpoint.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Esempio 1: Aggiunta degli endpoint di servizio app usando `Add-AzTrafficManagerEndpointConfig`

In questo esempio viene creato un profilo di Gestione traffico e vengono aggiunti due endpoint di servizio app usando il cmdlet `Add-AzTrafficManagerEndpointConfig`.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Esempio 2 Aggiunta di un endpoint publicIpAddress usando `New-AzTrafficManagerEndpoint`

In questo esempio viene aggiunta una risorsa indirizzo IP pubblico al profilo di Gestione traffico. L'indirizzo IP pubblico deve avere un nome DNS configurato e può essere associato alla NIC di una VM o a un servizio di bilanciamento del carico.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Aggiunta di endpoint esterni

Gestione traffico usa endpoint esterni per indirizzare il traffico ai servizi ospitati all'esterno di Azure. Come con gli endpoint di Azure, gli endpoint esterni possono essere aggiunti usando `Add-AzTrafficManagerEndpointConfig` seguito da `Set-AzTrafficManagerProfile` o `New-AzTrafficManagerEndpoint`.

Quando si specificano endpoint esterni:

* Il nome di dominio dell'endpoint deve essere specificato con il parametro "Target"
* "EndpointLocation" è obbligatorio se viene usato il metodo di routing del traffico "Performance". In caso contrario, è facoltativo. Il valore deve essere un [nome di area di Azure valido](https://azure.microsoft.com/regions/).
* I parametri "Weight" e "Priority" sono facoltativi.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Esempio 1: Aggiunta di endpoint esterni usando `Add-AzTrafficManagerEndpointConfig` e `Set-AzTrafficManagerProfile`

In questo esempio viene creato un profilo di Gestione traffico, vengono aggiunti due endpoint esterni e viene eseguito il commit delle modifiche.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Esempio 2 Aggiunta di endpoint esterni usando `New-AzTrafficManagerEndpoint`

In questo esempio si aggiunge un endpoint esterno a un profilo esistente. Il profilo viene specificato usando i nomi del gruppo di risorse e del profilo.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Aggiunta di endpoint 'annidati'

Ciascun profilo di Gestione traffico specifica un solo metodo di routing del traffico. Esistono scenari che tuttavia richiedono un sistema di routing del traffico più avanzato anziché il routing fornito da un singolo profilo di Gestione traffico. È possibile annidare i profili di Gestione traffico per combinare i vantaggi offerti da più metodi di routing del traffico. I profili annidati consentono di ignorare il comportamento predefinito di Gestione traffico per supportare distribuzioni di dimensioni maggiori e più complesse. Per esempi più dettagliati, vedere [Profili annidati di Gestione traffico](traffic-manager-nested-profiles.md).

Endpoint annidati vengono configurati nel profilo padre tramite un tipo di endpoint specifico, 'NestedEndpoints'. Quando si specificano endpoint annidati:

* L'endpoint deve essere specificato usando il parametro "targetResourceId"
* "EndpointLocation" è obbligatorio se viene usato il metodo di routing del traffico "Performance". In caso contrario, è facoltativo. Il valore deve essere un [nome di area di Azure valido](https://azure.microsoft.com/regions/).
* I parametri "Weight" e "Priority" sono facoltativi, come per gli endpoint di Azure.
* Il parametro "MinChildEndpoints" è facoltativo. Il valore predefinito è "1". Se il numero di endpoint disponibili scende sotto questa soglia, il profilo padre considera il profilo figlio "degradato" con conseguente deviazione del traffico agli altri endpoint del profilo padre.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Esempio 1: Aggiunta di endpoint annidati usando `Add-AzTrafficManagerEndpointConfig` e `Set-AzTrafficManagerProfile`

In questo esempio vengono creati un nuovo profilo padre e un nuovo profilo figlio di Gestione traffico, il profilo figlio viene aggiunto come endpoint annidato nel profilo padre, quindi vengono confermate le modifiche.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

Per brevità, in questo esempio, non sono stati aggiunti altri endpoint al profilo padre o figlio.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Esempio 2 Aggiunta di endpoint annidati usando `New-AzTrafficManagerEndpoint`

In questo esempio viene aggiunto un profilo figlio esistente come endpoint annidato in un profilo padre esistente. Il profilo viene specificato usando i nomi del gruppo di risorse e del profilo.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Aggiunta di endpoint da un'altra sottoscrizione

Gestione traffico può usare endpoint da sottoscrizioni diverse. È necessario passare alla sottoscrizione con l'endpoint da aggiungere per recuperare l'input necessario in Gestione traffico. Quindi è necessario passare alle sottoscrizioni con il profilo di gestione traffico e aggiungervi l'endpoint. L'esempio seguente mostra come eseguire questa operazione con un indirizzo IP pubblico.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aggiornare un endpoint di Gestione traffico

È possibile aggiornare un endpoint di Gestione traffico esistente in due modi:

1. Ottenere il profilo di Gestione traffico mediante `Get-AzTrafficManagerProfile`, aggiornare le proprietà dell'endpoint nel profilo ed eseguire il commit delle modifiche mediante `Set-AzTrafficManagerProfile`. Il vantaggio di questo metodo consiste nella possibilità di aggiornare più endpoint in una singola operazione.
2. Ottenere l'endpoint di Gestione traffico mediante `Get-AzTrafficManagerEndpoint`, aggiornare le proprietà dell'endpoint ed eseguire il commit delle modifiche mediante `Set-AzTrafficManagerEndpoint`. Questo metodo è più semplice, perché non richiede l'indicizzazione nella matrice Endpoints nel profilo.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Esempio 1: Aggiornamento di endpoint usando `Get-AzTrafficManagerProfile` e `Set-AzTrafficManagerProfile`

In questo esempio viene modificata la priorità di due endpoint in un profilo esistente.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Esempio 2 Aggiornamento di un endpoint usando `Get-AzTrafficManagerEndpoint` e `Set-AzTrafficManagerEndpoint`

In questo esempio viene modificato il peso di un singolo endpoint in un profilo esistente.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Abilitazione e disabilitazione di endpoint e profili

Gestione traffico consente l'abilitazione e la disabilitazione dei singoli endpoint, oltre a consentire l'abilitazione e la disabilitazione di interi profili.
Queste modifiche possono essere apportate ottenendo/aggiornando/impostando l'endpoint o le risorse del profilo. Per semplificare queste operazioni comuni, le modifiche sono supportate anche tramite cmdlet dedicati.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Esempio 1: Abilitazione e disabilitazione di un profilo di Gestione traffico

Per abilitare un profilo di Gestione traffico, usare `Enable-AzTrafficManagerProfile`. Il profilo può essere specificato usando un oggetto profilo. L'oggetto profilo può essere passato tramite la pipeline o usando il parametro '-TrafficManagerProfile'. In questo esempio abbiamo specificato il profilo in base al nome del gruppo di risorse e profili.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Per disabilitare un profilo di Gestione traffico:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Il cmdlet Disable-AzTrafficManagerProfile chiede la conferma. Questo messaggio può essere soppresso mediante il parametro "-Force".

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Esempio 2 Abilitazione e disabilitazione di un endpoint di Gestione traffico

Per abilitare un endpoint di Gestione traffico, usare `Enable-AzTrafficManagerEndpoint`. È possibile specificare l'endpoint in due modi:

1. Usando un oggetto TrafficManagerEndpoint passato tramite la pipeline o usando il parametro "-TrafficManagerEndpoint"
2. Usando il nome dell'endpoint, il tipo di endpoint, il nome del profilo e il nome del gruppo di risorse:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Analogamente, per disabilitare un endpoint di Gestione traffico:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Come con `Disable-AzTrafficManagerProfile`, il cmdlet `Disable-AzTrafficManagerEndpoint` richiede una conferma. Questo messaggio può essere soppresso mediante il parametro "-Force".

## <a name="delete-a-traffic-manager-endpoint"></a>Eliminare un endpoint di Gestione traffico

Per rimuovere singoli endpoint, usare il cmdlet `Remove-AzTrafficManagerEndpoint`:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Il cmdlet richiede una conferma. Questo messaggio può essere soppresso mediante il parametro "-Force".

## <a name="delete-a-traffic-manager-profile"></a>Eliminare un profilo di Gestione traffico

Per eliminare un profilo di Gestione traffico, usare il cmdlet `Remove-AzTrafficManagerProfile`, specificando il nome del profilo e del gruppo di risorse:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Il cmdlet richiede una conferma. Questo messaggio può essere soppresso mediante il parametro "-Force".

Inoltre, il profilo da eliminare può essere specificato utilizzando un oggetto di profilo:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Questa sequenza può anche essere inoltrata tramite pipe:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Passaggi successivi

[Monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

[Considerazioni sulle prestazioni di gestione traffico](traffic-manager-performance-considerations.md)
