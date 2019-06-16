---
title: Risolvere gli errori di Gateway non valido nel Gateway applicazione Azure (502)
description: Informazioni su come risolvere gli errori 502 del gateway applicazione
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: amsriva
ms.openlocfilehash: 2a1c7e480e896da6852949c9d765d17290e4e9ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64697154"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Risoluzione degli errori del gateway non valido nel gateway applicazione

Informazioni su come risolvere gli errori di gateway non valido (502) ricevuti durante l'uso di Gateway applicazione di Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

Dopo aver configurato un gateway applicazione, uno degli errori che potrebbe essere visualizzato è "Errore del Server: 502 - Il server Web, in funzione come gateway o proxy, ha ricevuto una risposta non valida". Questo errore può verificarsi per i motivi principali seguenti:

* Sicurezza di rete, route definite dall'utente o DNS personalizzato sta bloccando l'accesso ai membri del pool back-end.
* Le macchine virtuali o istanze del set di scalabilità di macchine virtuali di back-end non rispondono al probe di integrità predefinito.
* Configurazione non valida o inappropriata dei probe di integrità personalizzati.
* Azure Application Gateway [pool back-end non è configurato o è vuoto](#empty-backendaddresspool).
* Nessuna delle macchine virtuali o delle istanze nel [set di scalabilità di macchine virtuali è integra](#unhealthy-instances-in-backendaddresspool).
* [Problemi di timeout della richiesta o di connettività](#request-time-out) con le richieste degli utenti.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema di gruppo di sicurezza di rete, route definita dall'utente o DNS personalizzato

### <a name="cause"></a>Causa

Se l'accesso al back-end è bloccato a causa di un NSG, UDR o DNS personalizzato, le istanze del gateway applicazione non riesce a raggiungere il pool back-end. Ciò causa errori di probe, causando 502 errori.

il NSG/UDR possono essere presente nella subnet del gateway applicazione o la subnet in cui vengono distribuite macchine virtuali dell'applicazione.

Analogamente, la presenza di un DNS personalizzato nella rete virtuale potrebbe causare problemi. Un nome di dominio completo usato per i membri del pool back-end non vengano risolti correttamente dal server DNS configurato dall'utente per la rete virtuale.

### <a name="solution"></a>Soluzione

Convalidare la configurazione di gruppo di sicurezza di rete, route definita dall'utente o DNS personalizzato tramite i passaggi seguenti:

* Controllare che gli Nsg associato con la subnet del gateway applicazione. Assicurarsi che la comunicazione al back-end non è bloccata.
* Controllare che routing definito dall'utente associati con la subnet del gateway applicazione. Assicurarsi che la route definita dall'utente non è indirizzare il traffico dalla subnet back-end. Controllare ad esempio, per il routing per le route predefinite venga annunciate alla subnet del gateway applicazione tramite ExpressRoute/VPN o Appliance virtuali di rete.

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
Se presente, assicurarsi che il server DNS può risolvere nome di dominio completo del membro del pool back-end in modo corretto.

## <a name="problems-with-default-health-probe"></a>Problemi con il probe di integrità predefinito

### <a name="cause"></a>Causa

gli 502 errori possono anche indicare frequentemente che il probe di integrità predefinito non riesce a raggiungere le macchine virtuali back-end.

Quando viene eseguito il provisioning di un'istanza del gateway applicazione, viene configurato automaticamente un probe di integrità predefinito per ogni BackendAddressPool tramite le proprietà del BackendHttpSetting. Per impostare il probe non è necessaria alcuna azione da parte dell'utente. In particolare, quando una regola di bilanciamento del carico è configurata, viene creata un'associazione tra un BackendHttpSetting e BackendAddressPool. Un probe predefinito viene configurato per ognuna di queste associazioni e il gateway applicazione avvia una connessione di controllo di integrità periodici a ogni istanza nel backendaddresspool, sulla porta specificata nell'elemento BackendHttpSetting. 

Nella tabella seguente sono elencati i valori associati al probe di integrità predefiniti:

| Proprietà probe | Value | Descrizione |
| --- | --- | --- |
| URL probe |`http://127.0.0.1/` |Percorso URL |
| Interval |30 |Intervallo di probe in secondi |
| Timeout |30 |Timeout di probe in secondi |
| Soglia non integra |3 |Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

### <a name="solution"></a>Soluzione

* Verificare che sia stato configurato un sito predefinito e che sia in ascolto su 127.0.0.1.
* Se BackendHttpSetting specifica una porta diversa da 80, il sito predefinito deve essere configurato per ascoltare tale porta.
* La chiamata a `http://127.0.0.1:port` deve restituire un codice risultato HTTP di 200. Questo deve essere restituito entro il periodo di timeout di 30 secondi.
* Assicurarsi che la porta configurata sia aperta e che non sono presenti regole del firewall o i gruppi di sicurezza rete di Azure, che bloccano il traffico in ingresso o in uscita sulla porta configurata.
* Se le VM di Azure classiche o un servizio Cloud viene usato con un nome di dominio completo o un indirizzo IP pubblico, assicurarsi che la corrispondente [endpoint](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) viene aperto.
* Se la macchina virtuale è configurata tramite Azure Resource Manager e non è compreso nella rete virtuale in cui viene distribuito il gateway applicazione, un [Network Security Group](../virtual-network/security-overview.md) deve essere configurato per consentire l'accesso alla porta desiderata.

## <a name="problems-with-custom-health-probe"></a>Problemi con il probe di integrità personalizzato

### <a name="cause"></a>Causa

Il probe di integrità personalizzato consente una maggiore flessibilità per la ricerca di comportamenti predefiniti del probe. Quando si usano i probe personalizzati, è possibile configurare l'intervallo di probe, l'URL, il percorso da testare e il numero di risposte non riuscito da accettare prima di contrassegnare l'istanza del pool back-end come non integra.

Vengono aggiunte le proprietà aggiuntive seguenti:

| Proprietà probe | Descrizione |
| --- | --- |
| Name |Nome del probe. Questo nome viene usato per fare riferimento al probe nelle impostazioni HTTP back-end |
| Protocol |Protocollo usato per inviare il probe. Il probe usa il protocollo definito nelle impostazioni HTTP del back-end. |
| Host |Nome host per inviare il probe. Applicabile solo quando sono configurato su più siti nel gateway applicazione. Questo nome è diverso dal nome host della macchina virtuale. |
| `Path` |Percorso relativo del probe. Il percorso valido inizia da "/". Il probe viene inviato a \<protocollo\>://\<host\>:\<porta\>\<percorso\> |
| Interval |Intervallo di probe in secondi. Si tratta dell'intervallo di tempo tra due probe consecutivi. |
| Timeout |Timeout del probe in secondi. Se non viene ricevuta una risposta valida entro questo periodo di timeout, il probe viene contrassegnato come non riuscita. |
| Soglia non integra |Numero di tentativi di probe. Il server back-end viene contrassegnato come inattivo dopo che il numero di errori di probe consecutivi ha raggiunto una soglia non integra. |

### <a name="solution"></a>Soluzione

Verificare che il probe di integrità personalizzato sia configurato correttamente in base alla tabella precedente. In aggiunta alle procedure di risoluzione dei problemi precedenti, verificare anche quanto segue:

* Verificare che il probe sia specificato correttamente secondo le istruzioni della [guida](application-gateway-create-probe-ps.md).
* Se il gateway applicazione è configurato per un singolo sito, per impostazione predefinita l'Host nome deve essere specificato come `127.0.0.1`, se non diversamente configurato nel probe personalizzato.
* Verificare che la chiamata a http://\<host\>:\<porta\>\<percorso\> restituisca un codice risultato HTTP di 200.
* Assicurarsi che intervallo, Timeout e UnhealtyThreshold siano compresi in intervalli accettabili.
* Se si usa un probe HTTPS, assicurarsi che il server back-end non richieda SNI configurando un certificato di fallback nello stesso server back-end.

## <a name="request-time-out"></a>Timeout della richiesta

### <a name="cause"></a>Causa

Quando viene ricevuta una richiesta dell'utente, il gateway applicazione applica le regole configurate alla richiesta e lo instrada a un'istanza del pool back-end. Attende quindi la risposta dall'istanza back-end per un intervallo di tempo configurabile. Per impostazione predefinita, questo intervallo viene **20** secondi. Se il gateway applicazione non riceve una risposta dall'applicazione back-end in questo intervallo, la richiesta dell'utente riceve un errore 502.

### <a name="solution"></a>Soluzione

Il Gateway applicazione consente di configurare questa impostazione tramite BackendHttpSetting, applicabile a pool diversi. Pool back-end diversi possono avere BackendHttpSetting differenti e un timeout delle richieste diverse configurato.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vuoto

### <a name="cause"></a>Causa

Se il gateway applicazione è presenti VM o set di scalabilità di macchine virtuali configurate nel pool di indirizzi back-end, non può instradare le richieste del cliente e invia un messaggio di errore di gateway non valido.

### <a name="solution"></a>Soluzione

Assicurarsi che il pool di indirizzi back-end non sia vuoto. A tale scopo è possibile usare PowerShell, l'interfaccia della riga di comando o il portale.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

L'output del cmdlet precedente dovrebbe contenere un pool di indirizzi back-end non vuoto. Nell'esempio seguente mostra due pool restituito che sono configurati con un nome di dominio completo o un indirizzo IP per le macchine virtuali di back-end. Lo stato del provisioning di BackendAddressPool deve essere "Succeeded".

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

Se tutte le istanze di BackendAddressPool non sono integri, quindi il gateway applicazione non è instradare la richiesta utente da qualsiasi back-end. Ciò può essere anche il caso quando le istanze back-end sono integre ma non hanno distribuita l'applicazione necessaria.

### <a name="solution"></a>Soluzione

Verificare che le istanze siano integre e che l'applicazione sia configurata correttamente. Controllare se le istanze back-end possono rispondere a un ping da un'altra VM nella stessa rete virtuale. Se configurato con un endpoint pubblico, assicurarsi che sia valida la richiesta del browser per l'applicazione web.

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire una [ticket di supporto](https://azure.microsoft.com/support/options/).

