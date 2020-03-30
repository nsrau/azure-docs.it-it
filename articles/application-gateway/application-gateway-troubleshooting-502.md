---
title: Risolvere gli errori del gateway non valido - Gateway applicazione di AzureTroubleshoot Bad Gateway errors - Azure Application Gateway
description: "Informazioni su come risolvere l'errore del server Gateway applicazione: 502 - Il server Web ha ricevuto una risposta non valida mentre funge da gateway o server proxy."
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130480"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Risoluzione degli errori del gateway non valido nel gateway applicazione

Informazioni su come risolvere gli errori del gateway non valido (502) ricevuti quando si usa il gateway applicazione di Azure.Learn how to troubleshoot bad gateway (502) errors received when using Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

Dopo aver configurato un gateway applicazione, uno degli errori che possono essere visualizzati è "Errore del server: 502 - Il server Web ha ricevuto una risposta non valida mentre funge da gateway o server proxy". Questo errore può verificarsi per i seguenti motivi principali:

* NSG, UDR o DNS personalizzato blocca l'accesso ai membri del pool back-end.
* Le macchine virtuali back-end o le istanze del set di scalabilità delle macchine virtuali non rispondono al probe di integrità predefinito.
* Configurazione non valida o inappropriata dei probe di integrità personalizzati.
* Il [pool back-end](#empty-backendaddresspool)del gateway applicazione di Azure non è configurato o è vuoto.
* Nessuna delle macchine virtuali o delle istanze nel [set di scalabilità di macchine virtuali è integra](#unhealthy-instances-in-backendaddresspool).
* [Timeout della richiesta o problemi](#request-time-out) di connettività con le richieste degli utenti.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema di gruppo di sicurezza di rete, route definita dall'utente o DNS personalizzato

### <a name="cause"></a>Causa

Se l'accesso al back-end è bloccato a causa di un gruppo di sicurezza di rete, un UDR o un DNS personalizzato, le istanze del gateway applicazione non possono raggiungere il pool back-end. Ciò causa errori di probe, con conseguente errori 502.

L'UDR/NSG potrebbe essere presente nella subnet del gateway applicazione o nella subnet in cui vengono distribuite le macchine virtuali dell'applicazione.

Analogamente, la presenza di un DNS personalizzato nella rete virtuale potrebbe anche causare problemi. Un FQDN utilizzato per i membri del pool back-end potrebbe non essere risolto correttamente dal server DNS configurato dall'utente per la rete virtuale.

### <a name="solution"></a>Soluzione

Convalidare la configurazione di gruppo di sicurezza di rete, route definita dall'utente o DNS personalizzato tramite i passaggi seguenti:

* Controllare i gruppi di sicurezza di rete associati alla subnet del gateway applicazione. Assicurarsi che la comunicazione con il back-end non sia bloccata.
* Controllare UDR associato alla subnet del gateway applicazione. Assicurarsi che l'UDR non disorienta il traffico dalla subnet back-end. Ad esempio, verificare il routing alle appliance virtuali di rete o le route predefinite annunciate alla subnet del gateway applicazione tramite ExpressRoute/VPN.

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
Se presente, verificare che il server DNS sia in grado di risolvere correttamente l'FQDN del membro del pool back-end.

## <a name="problems-with-default-health-probe"></a>Problemi con il probe di integrità predefinito

### <a name="cause"></a>Causa

Gli errori 502 possono anche essere indicatori frequenti che il probe di integrità predefinito non può raggiungere le macchine virtuali back-end.

Quando viene eseguito il provisioning di un'istanza del gateway applicazione, viene configurato automaticamente un probe di integrità predefinito per ogni BackendAddressPool usando le proprietà di BackendHttpSetting. Per impostare il probe non è necessaria alcuna azione da parte dell'utente. In particolare, quando viene configurata una regola di bilanciamento del carico, viene effettuata un'associazione tra un BackendHttpSetting e un BackendAddressPool.Specifically, when a load-balancing rule is configured, an association is made between a BackendHttpSetting and a BackendAddressPool. Un probe predefinito viene configurato per ognuna di queste associazioni e il gateway applicazione avvia una connessione periodica di controllo dell'integrità a ogni istanza di BackendAddressPool sulla porta specificata nell'elemento BackendHttpSetting. 

Nella tabella seguente sono elencati i valori associati al probe di integrità predefinito:

| Proprietà probe | valore | Descrizione |
| --- | --- | --- |
| URL probe |`http://127.0.0.1/` |Percorso URL |
| Interval |30 |Intervallo di probe in secondi |
| Timeout |30 |Timeout di probe in secondi |
| Soglia non integra |3 |Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

### <a name="solution"></a>Soluzione

* Verificare che sia stato configurato un sito predefinito e che sia in ascolto su 127.0.0.1.
* Se BackendHttpSetting specifica una porta diversa da 80, il sito predefinito deve essere configurato per ascoltare tale porta.
* La chiamata a `http://127.0.0.1:port` deve restituire un codice risultato HTTP di 200. Questo deve essere restituito entro il periodo di timeout di 30 secondi.
* Verificare che la porta configurata sia aperta e che non siano presenti regole del firewall o gruppi di sicurezza di rete di Azure che bloccano il traffico in ingresso o in uscita sulla porta configurata.
* Se le macchine virtuali classiche di Azure o il servizio cloud viene usato con un nome di dominio completo o un indirizzo IP pubblico, verificare che sia aperto [l'endpoint](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) corrispondente.
* Se la macchina virtuale è configurata tramite Azure Resource Manager e si trova all'esterno della rete virtuale in cui è distribuito il gateway applicazione, è necessario configurare un gruppo di sicurezza di [rete](../virtual-network/security-overview.md) per consentire l'accesso sulla porta desiderata.

## <a name="problems-with-custom-health-probe"></a>Problemi con il probe di integrità personalizzato

### <a name="cause"></a>Causa

Il probe di integrità personalizzato consente una maggiore flessibilità per la ricerca di comportamenti predefiniti del probe. Quando si utilizzano probe personalizzati, è possibile configurare l'intervallo di probe, l'URL, il percorso da testare e il numero di risposte non riuscite da accettare prima di contrassegnare l'istanza del pool back-end come non integra.

Vengono aggiunte le seguenti proprietà aggiuntive:

| Proprietà probe | Descrizione |
| --- | --- |
| Nome |Nome del probe. Questo nome viene usato per fare riferimento al probe nelle impostazioni HTTP back-end |
| Protocollo |Protocollo usato per inviare il probe. Il probe usa il protocollo definito nelle impostazioni HTTP del back-end. |
| Host |Nome host per inviare il probe. Applicabile solo quando multisito è configurato nel gateway applicazione. Questo nome è diverso dal nome host della macchina virtuale. |
| Path |Percorso relativo del probe. Il percorso valido inizia da "/". Il probe viene inviato a \<protocollo\>://\<host\>:\<porta\>\<percorso\> |
| Interval |Intervallo di probe in secondi. Si tratta dell'intervallo di tempo tra due probe consecutivi. |
| Timeout |Timeout del probe in secondi. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non riuscito. |
| Soglia non integra |Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

### <a name="solution"></a>Soluzione

Verificare che il probe di integrità personalizzato sia configurato correttamente in base alla tabella precedente. In aggiunta alle procedure di risoluzione dei problemi precedenti, verificare anche quanto segue:

* Verificare che il probe sia specificato correttamente secondo le istruzioni della [guida](application-gateway-create-probe-ps.md).
* Se il gateway applicazione è configurato per un singolo sito, `127.0.0.1`per impostazione predefinita il nome host deve essere specificato come , a meno che non sia configurato diversamente nel probe personalizzato.
* Verificare che la chiamata a http://\<host\>:\<porta\>\<percorso\> restituisca un codice risultato HTTP di 200.
* Assicurarsi che Interval, Timeout e UnhealtyThreshold rientrino negli intervalli accettabili.
* Se si usa un probe HTTPS, assicurarsi che il server back-end non richieda SNI configurando un certificato di fallback nello stesso server back-end.

## <a name="request-time-out"></a>Timeout della richiesta

### <a name="cause"></a>Causa

Quando viene ricevuta una richiesta utente, il gateway applicazione applica le regole configurate alla richiesta e la indirizza a un'istanza del pool back-end. Attende quindi la risposta dall'istanza back-end per un intervallo di tempo configurabile. Per impostazione predefinita, questo intervallo è **20** secondi. Se il gateway applicazione non riceve una risposta dall'applicazione back-end in questo intervallo, la richiesta dell'utente riceve un errore 502.

### <a name="solution"></a>Soluzione

Il gateway applicazione consente di configurare questa impostazione tramite BackendHttpSetting, che può essere applicato a pool diversi. Pool back-end diversi possono avere backendHttpSetting diversi e una richiesta diversa è configurata.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vuoto

### <a name="cause"></a>Causa

Se nel gateway applicazione non sono configurate macchine virtuali o set di scalabilità di macchine virtuali nel pool di indirizzi back-end, non è possibile instradare alcuna richiesta del cliente e inviare un errore di gateway non valido.

### <a name="solution"></a>Soluzione

Verificare che il pool di indirizzi back-end non sia vuoto. A tale scopo è possibile usare PowerShell, l'interfaccia della riga di comando o il portale.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

L'output del cmdlet precedente dovrebbe contenere un pool di indirizzi back-end non vuoto. Nell'esempio seguente vengono restituiti due pool configurati con un nome di dominio completo o un indirizzo IP per le macchine virtuali back-end. Lo stato del provisioning di BackendAddressPool deve essere "Succeeded".

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

Se tutte le istanze di BackendAddressPool non sono integre, il gateway applicazione non dispone di alcun back-end a cui instradare la richiesta dell'utente. Ciò può verificarsi anche quando le istanze back-end sono integre ma non è distribuita l'applicazione necessaria.

### <a name="solution"></a>Soluzione

Verificare che le istanze siano integre e che l'applicazione sia configurata correttamente. Verificare se le istanze back-end possono rispondere a un ping da un'altra macchina virtuale nella stessa rete virtuale. Se configurato con un punto finale pubblico, verificare che una richiesta del browser all'applicazione Web sia utilizzabile.

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un ticket di [supporto.](https://azure.microsoft.com/support/options/)

