---
title: "Introduzione alla funzionalità di risoluzione dei problemi di connessione di Azure Network Watcher | Microsoft Docs"
description: "Questa pagina fornisce una panoramica della funzionalità di risoluzione dei problemi di connessione di Network Watcher"
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: f8825af71620722065c03a28c93e113876c5aa71
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Introduzione alla funzionalità di risoluzione dei problemi di connessione in Azure Network Watcher

La funzionalità di risoluzione dei problemi di connessione di Network Watcher consente di controllare una connessione TCP diretta da una macchina virtuale (VM) a un'altra, il nome di dominio completo (FQDN), l'URI o l'indirizzo IPv4. Gli scenari di rete sono complessi e vengono implementati mediante l'uso di gruppi di sicurezza di rete, firewall, route definite dall'utente e risorse fornite da Azure. La risoluzione dei problemi di connettività nella configurazioni complesse è un compito impegnativo. Network Watcher consente di ridurre il tempo necessario per individuare e rilevare i problemi di connettività. I risultati restituiti possono offrire informazioni dettagliate sulla causa del problema di connettività, ovvero se esso sia riconducibile a un problema della piattaforma o della configurazione utente. È possibile controllare la connettività con [PowerShell](network-watcher-connectivity-powershell.md), l'[interfaccia della riga di comando di Azure](network-watcher-connectivity-cli.md) e l'[API REST](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Per la risoluzione dei problemi di connessione è richiesta un'estensione macchina virtuale `AzureNetworkWatcherExtension`. Per installare l'estensione in una VM Windows, vedere [Estensione macchina virtuale agente Azure Network Watcher per Windows](../virtual-machines/windows/extensions-nwa.md) e per una VM Linux VM vedere [Estensione macchina virtuale Azure Network Watcher Agent per Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="response"></a>Risposta

La tabella seguente illustra le proprietà restituite al termine dell'esecuzione della funzionalità di risoluzione dei problemi di connessione.

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|ConnectionStatus     | Lo stato del controllo della connettività. I risultati possibili sono **Reachable** e **Unreachable**.        |
|AvgLatencyInMs     | Latenza media durante il controllo della connettività in millisecondi. Visualizzata solo se lo stato del controllo è reachable.        |
|MinLatencyInMs     | Latenza minima durante il controllo della connettività in millisecondi. Visualizzata solo se lo stato del controllo è reachable.        |
|MaxLatencyInMs     | Latenza massima durante il controllo della connettività in millisecondi. Visualizzata solo se lo stato del controllo è reachable.        |
|ProbesSent     | Numero di probe inviati durante il controllo. Il valore massimo è 100.        |
|ProbesFailed     | Numero di probe non riusciti durante il controllo. Il valore massimo è 100.        |
|Hops     | Hop per percorso hop da origine a destinazione.        |
|Hops[].Type     | Tipo di risorsa. I valori possibili sono **Source**, **VirtualAppliance**, **VnetLocal** e **Internet**.        |
|Hops[].Id | Identificatore univoco dell'hop.|
|Hops[].Address | Indirizzo IP dell'hop.|
|Hops[].ResourceId | ID risorsa dell'hop se l'hop è una risorsa di Azure. Se si tratta di una risorsa Internet, l'ID risorsa è **Internet**. |
|Hops[].NextHopIds | Identificatore univoco dell'hop successivo.|
|Hops[].Issues | Raccolta di problemi riscontrati durante il controllo in corrispondenza dell'hop. Se non sono stati riscontrati problemi, il valore è vuoto.|
|Hops[].Issues[].Origin | Hop corrente in cui si è verificato il problema. I valori possibili sono:<br/> **Inbound**: il problema è nel collegamento dall'hop precedente a quello corrente<br/>**Outbound**: il problema è nel collegamento dall'hop corrente a quello successivo<br/>**Local**: il problema è nell'hop corrente.|
|Hops[].Issues[].Severity | Gravità del problema rilevato. I valori possibili sono **Error** e **Warning**. |
|Hops[].Issues[].Type |Tipo di problema rilevato. I valori possibili sono: <br/>**CPU**<br/>**Memoria**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |Dettagli relativi al problema rilevato.|
|Hops[].Issues[].Context[].key |Chiave della coppia chiave-valore restituita.|
|Hops[].Issues[].Context[].value |Valore della coppia chiave-valore restituito.|

Di seguito è riportato un esempio di un problema rilevato in un hop.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Tipi di errore

La funzionalità di risoluzione dei problemi di connessione restituisce i tipi di errore relativi alla connessione. La tabella seguente contiene un elenco dei tipi di errore attualmente restituiti.

|type  |DESCRIZIONE  |
|---------|---------|
|CPU     | Utilizzo elevato della CPU.       |
|Memoria     | Uso intensivo della memoria.       |
|GuestFirewall     | Traffico bloccato a causa della configurazione del firewall di una macchina virtuale.        |
|DNSResolution     | Risoluzione DNS non riuscita per l'indirizzo di destinazione.        |
|NetworkSecurityRule    | Traffico bloccato da una regola NSG. La regola viene restituita.        |
|UserDefinedRoute|Traffico ignorato a causa di una route definita dall'utente o di sistema. |

### <a name="next-steps"></a>Passaggi successivi

Informazioni su come risolvere i problemi relativi alle connessioni usando il [portale di Azure](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), l'[interfaccia della riga di comando di Azure](network-watcher-connectivity-cli.md) o l'[API REST](network-watcher-connectivity-rest.md).