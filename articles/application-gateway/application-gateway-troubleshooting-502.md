---
title: Risolvere gli errori del gateway non valido-gateway applicazione Azure
description: Informazioni su come risolvere gli errori 502 del gateway applicazione
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: amsriva
ms.openlocfilehash: baf1eccdd6fe910bd98e8b39ef29b7bd8e88a7d5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048151"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Risoluzione degli errori del gateway non valido nel gateway applicazione

Informazioni su come risolvere gli errori di gateway non valido (502) ricevuti quando si usa applicazione Azure gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overview

Dopo la configurazione di un gateway applicazione, uno degli errori che è possibile riscontrare è "errore del server: 502-risposta non valida ricevuta dal server Web in funzione come server proxy o gateway". Questo errore può verificarsi per i motivi principali seguenti:

* NSG, UDR o DNS personalizzato bloccano l'accesso ai membri del pool back-end.
* Le macchine virtuali back-end o le istanze del set di scalabilità di macchine virtuali non rispondono al Probe di integrità predefinito.
* Configurazione non valida o inappropriata dei probe di integrità personalizzati.
* Il [pool back-end del Gateway applicazione Azure non è configurato o è vuoto](#empty-backendaddresspool).
* Nessuna delle macchine virtuali o delle istanze nel [set di scalabilità di macchine virtuali è integra](#unhealthy-instances-in-backendaddresspool).
* [Problemi di timeout della richiesta o di connettività](#request-time-out) con le richieste degli utenti.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema di gruppo di sicurezza di rete, route definita dall'utente o DNS personalizzato

### <a name="cause"></a>Causa

Se l'accesso al back-end è bloccato a causa di un NSG, UDR o DNS personalizzato, le istanze del gateway applicazione non possono raggiungere il pool back-end. In questo modo si verificano errori di probe, con 502 errori.

NSG/UDR può essere presente nella subnet del gateway applicazione o nella subnet in cui vengono distribuite le macchine virtuali dell'applicazione.

Analogamente, anche la presenza di un DNS personalizzato nella VNet può causare problemi. Un nome di dominio completo usato per i membri del pool back-end potrebbe non essere risolto correttamente dal server DNS configurato dall'utente per VNet.

### <a name="solution"></a>Soluzione

Convalidare la configurazione di gruppo di sicurezza di rete, route definita dall'utente o DNS personalizzato tramite i passaggi seguenti:

* Controllare gruppi associato alla subnet del gateway applicazione. Assicurarsi che la comunicazione con il back-end non sia bloccata.
* Controllare UDR associato alla subnet del gateway applicazione. Verificare che il UDR non stia indirizzando il traffico dalla subnet back-end. Ad esempio, controllare il routing a appliance virtuali di rete o route predefinite annunciate alla subnet del gateway applicazione tramite ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Controllare il gruppo di sicurezza di rete e la route effettivi nella macchina virtuale back-end

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Controllare se è presente un DNS personalizzato nella rete virtuale. È possibile eseguire questo controllo esaminando i dettagli delle proprietà della rete virtuale nell'output.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Se presente, verificare che il server DNS sia in grado di risolvere correttamente il nome FQDN del membro del pool back-end.

## <a name="problems-with-default-health-probe"></a>Problemi con il probe di integrità predefinito

### <a name="cause"></a>Causa

502 gli errori possono essere anche indicatori frequenti che il probe di integrità predefinito non è in grado di raggiungere le VM back-end.

Quando viene eseguito il provisioning di un'istanza del gateway applicazione, viene configurato automaticamente un probe di integrità predefinito per ogni BackendAddressPool usando le proprietà del BackendHttpSetting. Per impostare il probe non è necessaria alcuna azione da parte dell'utente. In particolare, quando viene configurata una regola di bilanciamento del carico, viene eseguita un'associazione tra un BackendHttpSetting e un BackendAddressPool. Per ognuna di queste associazioni viene configurato un probe predefinito e il gateway applicazione avvia una connessione di controllo di integrità periodica a ogni istanza di BackendAddressPool sulla porta specificata nell'elemento BackendHttpSetting. 

Nella tabella seguente sono elencati i valori associati al Probe di integrità predefinito:

| Proprietà probe | Valore | DESCRIZIONE |
| --- | --- | --- |
| URL probe |`http://127.0.0.1/` |Percorso URL |
| Interval |30 |Intervallo di probe in secondi |
| Timeout |30 |Timeout di probe in secondi |
| Soglia non integra |3 |Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

### <a name="solution"></a>Soluzione

* Verificare che sia stato configurato un sito predefinito e che sia in ascolto su 127.0.0.1.
* Se BackendHttpSetting specifica una porta diversa da 80, il sito predefinito deve essere configurato per ascoltare tale porta.
* La chiamata a `http://127.0.0.1:port` deve restituire un codice risultato HTTP di 200. Questa operazione deve essere restituita entro il periodo di timeout di 30 secondi.
* Verificare che la porta configurata sia aperta e che non siano presenti regole firewall o gruppi di sicurezza di rete di Azure, che bloccano il traffico in ingresso o in uscita sulla porta configurata.
* Se le macchine virtuali classiche di Azure o il servizio cloud viene usato con un nome di dominio completo o un indirizzo IP pubblico, assicurarsi che venga aperto l' [endpoint](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) corrispondente.
* Se la macchina virtuale viene configurata tramite Azure Resource Manager ed è all'esterno della VNet in cui viene distribuito il gateway applicazione, è necessario configurare un [gruppo di sicurezza di rete](../virtual-network/security-overview.md) per consentire l'accesso alla porta desiderata.

## <a name="problems-with-custom-health-probe"></a>Problemi con il probe di integrità personalizzato

### <a name="cause"></a>Causa

Il probe di integrità personalizzato consente una maggiore flessibilità per la ricerca di comportamenti predefiniti del probe. Quando si usano Probe personalizzati, è possibile configurare l'intervallo di probe, l'URL, il percorso da testare e il numero di risposte non riuscite da accettare prima di contrassegnare l'istanza del pool back-end come non integro.

Vengono aggiunte le proprietà aggiuntive seguenti:

| Proprietà probe | DESCRIZIONE |
| --- | --- |
| Nome |Nome del probe. Questo nome viene usato per fare riferimento al probe nelle impostazioni HTTP back-end |
| Protocol |Protocollo usato per inviare il probe. Il probe usa il protocollo definito nelle impostazioni HTTP del back-end. |
| Host |Nome host per inviare il probe. Applicabile solo quando il multisito è configurato nel gateway applicazione. Questo nome è diverso dal nome host della macchina virtuale. |
| Path |Percorso relativo del probe. Il percorso valido inizia da "/". Il probe viene inviato a \<protocollo\>://\<host\>:\<porta\>\<percorso\> |
| Interval |Intervallo di probe in secondi. Si tratta dell'intervallo di tempo tra due probe consecutivi. |
| Timeout |Timeout del probe in secondi. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non riuscito. |
| Soglia non integra |Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

### <a name="solution"></a>Soluzione

Verificare che il probe di integrità personalizzato sia configurato correttamente in base alla tabella precedente. In aggiunta alle procedure di risoluzione dei problemi precedenti, verificare anche quanto segue:

* Verificare che il probe sia specificato correttamente secondo le istruzioni della [guida](application-gateway-create-probe-ps.md).
* Se il gateway applicazione è configurato per un singolo sito, per impostazione predefinita il nome host deve essere specificato come `127.0.0.1`, salvo diversa configurazione in Probe personalizzato.
* Verificare che la chiamata a http://\<host\>:\<porta\>\<percorso\> restituisca un codice risultato HTTP di 200.
* Verificare che Interval, timeout e UnhealtyThreshold siano compresi negli intervalli accettabili.
* Se si usa un probe HTTPS, assicurarsi che il server back-end non richieda SNI configurando un certificato di fallback nello stesso server back-end.

## <a name="request-time-out"></a>Timeout della richiesta

### <a name="cause"></a>Causa

Quando viene ricevuta una richiesta dell'utente, il gateway applicazione applica le regole configurate alla richiesta e le instrada a un'istanza del pool back-end. Attende quindi la risposta dall'istanza back-end per un intervallo di tempo configurabile. Per impostazione predefinita, questo intervallo è di **20** secondi. Se il gateway applicazione non riceve una risposta dall'applicazione back-end in questo intervallo, la richiesta dell'utente riceve un errore 502.

### <a name="solution"></a>Soluzione

Il gateway applicazione consente di configurare questa impostazione tramite BackendHttpSetting, che può quindi essere applicata a diversi pool. I pool back-end diversi possono avere BackendHttpSetting differenti e un timeout della richiesta diverso.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vuoto

### <a name="cause"></a>Causa

Se nel pool di indirizzi back-end non è configurata alcuna VM o set di scalabilità di macchine virtuali, il gateway applicazione non può instradare le richieste dei clienti e invia un errore di gateway non valido.

### <a name="solution"></a>Soluzione

Verificare che il pool di indirizzi back-end non sia vuoto. A tale scopo è possibile usare PowerShell, l'interfaccia della riga di comando o il portale.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

L'output del cmdlet precedente dovrebbe contenere un pool di indirizzi back-end non vuoto. L'esempio seguente mostra due pool restituiti che sono configurati con un FQDN o indirizzi IP per le macchine virtuali back-end. Lo stato del provisioning di BackendAddressPool deve essere "Succeeded".

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Istanze non integre in BackendAddressPool

### <a name="cause"></a>Causa

Se tutte le istanze di BackendAddressPool sono non integre, il gateway applicazione non ha alcun back-end per il routing della richiesta utente a. Questa situazione può anche essere eseguita quando le istanze back-end sono integre ma non è stata distribuita l'applicazione richiesta.

### <a name="solution"></a>Soluzione

Verificare che le istanze siano integre e che l'applicazione sia configurata correttamente. Controllare se le istanze back-end possono rispondere a un ping da un'altra macchina virtuale nella stessa VNet. Se configurata con un endpoint pubblico, assicurarsi che una richiesta del browser all'applicazione Web sia configurabile come Service.

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).

