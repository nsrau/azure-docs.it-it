---
title: Firewall tabelle - soluzione VMware CloudSimple - Azure
description: Informazioni sulle tabelle di firewall CloudSimple cloud privato e le regole del firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577346"
---
# <a name="firewall-tables-overview"></a>Panoramica delle tabelle di firewall

Una tabella di firewall sono elencate le regole per filtrare il traffico di rete da e verso le risorse di cloud privato. È possibile applicarli a una VLAN o subnet. Quindi, le regole di controllano il traffico di rete tra una rete di origine o indirizzo IP e una rete di destinazione o indirizzo IP.

## <a name="firewall-rules"></a>Regole del firewall

Nella tabella seguente vengono descritti i parametri in una regola del firewall.

| Proprietà | Dettagli |
| ---------| --------|
| **Nome** | Un nome che identifica in modo univoco la regola del firewall e del relativo scopo. |
| **Priorità** | Un numero compreso tra 100 e 4096, con la priorità più alta di 100. Le regole vengono elaborate in ordine di priorità. Quando arriva il traffico tra corrispondenza a una regola, l'elaborazione delle regole si arresta. Di conseguenza, non vengono elaborate le regole con priorità inferiore che hanno gli stessi attributi regole con priorità più alta.  Prestare attenzione a evitare le regole in conflitto. |
| **Rilevamento dello stato** | Rilevamento può essere senza stato (Cloud privato, Internet o VPN) o con stato (indirizzo IP pubblico).  |
| **Protocollo** | Le opzioni sono Any, TCP o UDP. Se è necessario ICMP, usare uno qualsiasi. |
| **Direzione** | Definisce se la regola si applica al traffico in ingresso o in uscita. |
| **Azione** | Consente o Nega per il tipo di traffico definito nella regola. |
| **Origine** | Un indirizzo IP, blocco classless interdomain routing (CIDR) (ad esempio, 10.0.0.0/24) o uno qualsiasi.  Specifica un intervallo, un tag di servizio o gruppo di sicurezza dell'applicazione consente di creare regole di sicurezza di un numero inferiore. |
| **Porta di origine** | Porta di rete che il traffico ha origine.  È possibile specificare una porta singola o un intervallo di porte, ad esempio 443 o 8000-8080. Specificando intervalli è possibile creare un minor numero di regole di sicurezza. |
| **Destinazione** | Un indirizzo IP, blocco classless interdomain routing (CIDR) (ad esempio, 10.0.0.0/24) o uno qualsiasi.  Specifica un intervallo, un tag di servizio o gruppo di sicurezza dell'applicazione consente di creare regole di sicurezza di un numero inferiore.  |
| **Porta di destinazione** | Porta a cui il traffico di rete viene trasmesso.  È possibile specificare una porta singola o un intervallo di porte, ad esempio 443 o 8000-8080. Specificando intervalli è possibile creare un minor numero di regole di sicurezza.|

### <a name="stateless"></a>Senza stato

Una regola senza stata esamina solo singoli pacchetti e filtra in base alla regola.  
Le regole aggiuntive potrebbero essere necessari per il flusso del traffico in direzione inversa.  Usare le regole senza state per il traffico tra i punti seguenti:

* Subnet dei cloud privati
* Subnet locale e una subnet del Cloud privato
* Traffico Internet dai cloud privato

### <a name="stateful"></a>Con stato

 Una regola con stato è a conoscenza delle connessioni che vi passano attraverso. Viene creato un record di flusso per le connessioni esistenti. La comunicazione è consentita o negata in base allo stato di connessione del record di flusso.  Usare questo tipo di regola per gli indirizzi IP pubblici per filtrare il traffico da Internet.

### <a name="default-rules"></a>Regole predefinite

Le regole predefinite seguenti vengono create in ogni tabella di firewall.

|Priorità|NOME|Rilevamento dello stato|Direzione|Tipo di traffico|Protocol|`Source`|Porta di origine|Destination|Porta di destinazione|Azione|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Con stato|In uscita|Traffico IP o a internet pubblico|Tutti|Qualsiasi|Qualsiasi|Qualsiasi|Qualsiasi|CONSENTI|
|65001|Deny-all-da-internet|Con stato|In ingresso|Traffico IP o a internet pubblico|Tutti|Qualsiasi|Qualsiasi|Qualsiasi|Qualsiasi|Nega|
|65002|allow-all-to-intranet|Senza stato|In uscita|Cloud privata interna o il traffico VPN|Tutti|Qualsiasi|Qualsiasi|Qualsiasi|Qualsiasi|CONSENTI|
|65003|allow-all-from-intranet|Senza stato|In ingresso|Cloud privata interna o il traffico VPN|Tutti|Qualsiasi|Qualsiasi|Qualsiasi|Qualsiasi|CONSENTI|

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le regole e le tabelle di firewall](https://docs.azure.cloudsimple.com/firewall/)