---
title: Tabelle del firewall-soluzione VMware di CloudSimple-Azure
description: Informazioni sulle regole del firewall e delle tabelle del firewall del cloud privato CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d25aa9252f061cee7f4cffdca42f00d84f719a3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812655"
---
# <a name="firewall-tables-overview"></a>Panoramica delle tabelle del firewall

Una tabella del firewall elenca le regole per filtrare il traffico di rete da e verso le risorse del cloud privato. È possibile applicarli a una VLAN o a una subnet. Le regole controllano quindi il traffico di rete tra una rete di origine o un indirizzo IP e una rete di destinazione o un indirizzo IP.

## <a name="firewall-rules"></a>Regole del firewall

Nella tabella seguente vengono descritti i parametri di una regola del firewall.

| Proprietà | Dettagli |
| ---------| --------|
| **Nome** | Nome che identifica in modo univoco la regola del firewall e il suo scopo. |
| **Priorità** | Un numero compreso tra 100 e 4096, con 100 è la priorità più alta. Le regole vengono elaborate in ordine di priorità. Quando il traffico passa attraverso una regola, l'elaborazione delle regole viene arrestata. Di conseguenza, tutte le regole con priorità più bassa che hanno gli stessi attributi delle regole con priorità più alta non vengono elaborate.  Prestare attenzione per evitare regole in conflitto. |
| **Rilevamento dello stato** | Il rilevamento può essere senza stato (cloud privato, Internet o VPN) o con stato (IP pubblico).  |
| **Protocollo** | Le opzioni includono any, TCP o UDP. Se è necessario ICMP, usare Any. |
| **Direzione** | Definisce se la regola si applica al traffico in ingresso o in uscita. |
| **Azione** | Consentire o negare il tipo di traffico definito nella regola. |
| **Origine** | Un indirizzo IP, un blocco CIDR (Inter-Domain Routing) senza classe (ad esempio, 10.0.0.0/24) o any.  Specificando un intervallo, un tag di servizio o un gruppo di sicurezza delle applicazioni è possibile creare un minor numero di regole di sicurezza. |
| **Porta di origine** | Porta da cui ha origine il traffico di rete.  È possibile specificare una singola porta o un intervallo di porte, ad esempio 443 o 8000-8080. Specificando intervalli è possibile creare un minor numero di regole di sicurezza. |
| **Destinazione** | Un indirizzo IP, un blocco CIDR (Inter-Domain Routing) senza classe (ad esempio, 10.0.0.0/24) o any.  Specificando un intervallo, un tag di servizio o un gruppo di sicurezza delle applicazioni è possibile creare un minor numero di regole di sicurezza.  |
| **Porta di destinazione** | Porta a cui fluisce il traffico di rete.  È possibile specificare una singola porta o un intervallo di porte, ad esempio 443 o 8000-8080. Specificando intervalli è possibile creare un minor numero di regole di sicurezza.|

### <a name="stateless"></a>Senza stato

Una regola senza stato cerca solo i singoli pacchetti e li filtra in base alla regola.  
Per il flusso del traffico in direzione inversa potrebbero essere necessarie altre regole.  Usare regole senza stato per il traffico tra i punti seguenti:

* Subnet di cloud privati
* Subnet locale e subnet cloud privata
* Traffico Internet dai cloud privati

### <a name="stateful"></a>Con stato

 Una regola con stato è in grado di riconoscere le connessioni che lo passano. Viene creato un record di flusso per le connessioni esistenti. La comunicazione è consentita o negata in base allo stato di connessione del record di flusso.  Usare questo tipo di regola per gli indirizzi IP pubblici per filtrare il traffico da Internet.

### <a name="default-rules"></a>Regole predefinite

In ogni tabella del firewall vengono create le regole predefinite seguenti.

|Priority|Name|Rilevamento dello stato|Direction|Tipo di traffico|Protocol|Source|Porta di origine|Destination|Porta di destinazione|Azione|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|Consenti-tutti-a-Internet|Con stato|In uscita|IP pubblico o traffico Internet|Tutti|Any|Any|Any|Any|Allow|
|65001|Deny-All-da-Internet|Con stato|In ingresso|IP pubblico o traffico Internet|Tutti|Any|Any|Any|Any|Nega|
|65002|Consenti-tutti-a-Intranet|Senza stato|In uscita|Traffico interno o VPN del cloud privato|Tutti|Any|Any|Any|Any|Allow|
|65003|Consenti tutto da Intranet|Senza stato|In ingresso|Traffico interno o VPN del cloud privato|Tutti|Any|Any|Any|Any|Allow|

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le regole e le tabelle del firewall](https://docs.azure.cloudsimple.com/firewall/)