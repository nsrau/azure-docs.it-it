---
title: Soluzione Azure VMware di CloudSimple - Tabelle firewall
description: Informazioni sulle tabelle del firewall cloud privato CloudSimple e sulle regole del firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025045"
---
# <a name="firewall-tables-overview"></a>Panoramica delle tabelle del firewall

In una tabella del firewall sono elencate le regole per filtrare il traffico di rete da e verso le risorse private cloud. È possibile applicare tabelle firewall a una rete Virtuale/subnet. Le regole controllano il traffico di rete tra una rete di origine o un indirizzo IP e una rete o un indirizzo IP di destinazione.

## <a name="firewall-rules"></a>Regole del firewall

Nella tabella seguente vengono descritti i parametri di una regola del firewall.

| Proprietà | Dettagli |
| ---------| --------|
| **Nome** | Nome che identifica in modo univoco la regola del firewall e il relativo scopo. |
| **Priority** | Un numero compreso tra 100 e 4096, con 100 come priorità più alta. Le regole vengono elaborate in ordine di priorità. Quando il traffico rileva una corrispondenza della regola, l'elaborazione delle regole viene interrotta. Di conseguenza, le regole con priorità inferiore che hanno gli stessi attributi delle regole con priorità più alta non vengono elaborate.  Fare attenzione a evitare regole contrastanti. |
| **Tracciamento dello stato** | Il monitoraggio può essere senza stato (Cloud privato, Internet o VPN) o con stato (IP pubblico).  |
| **Protocollo** | Le opzioni includono Any, TCP o UDP. Se è necessario ICMP, utilizzare Any. |
| **Direzione** | Definisce se la regola si applica al traffico in ingresso o in uscita. |
| **Azione** | Consentire o negare il tipo di traffico definito nella regola. |
| **origine** | Un indirizzo IP, un blocco CIDR (Classless Inter-domain Routing) (ad esempio 10.0.0.0/24) o Any.  La specifica di un intervallo, di un tag di servizio o di un gruppo di sicurezza dell'applicazione consente di creare un numero inferiore di regole di sicurezza. |
| **Porta di origine** | Porta da cui ha origine il traffico di rete.  È possibile specificare una singola porta o un intervallo di porte, ad esempio 443 o 8000-8080. Specificando intervalli è possibile creare un minor numero di regole di sicurezza. |
| **Destinazione** | Un indirizzo IP, un blocco CIDR (Classless Inter-domain Routing) (ad esempio 10.0.0.0/24) o Any.  La specifica di un intervallo, di un tag di servizio o di un gruppo di sicurezza dell'applicazione consente di creare un numero inferiore di regole di sicurezza.  |
| **Porta di destinazione** | Porta a cui scorre il traffico di rete.  È possibile specificare una singola porta o un intervallo di porte, ad esempio 443 o 8000-8080. Specificando intervalli è possibile creare un minor numero di regole di sicurezza.|

### <a name="stateless"></a>Senza stato

Una regola senza stato esamina solo i singoli pacchetti e li filtra in base alla regola.  
Potrebbero essere necessarie regole aggiuntive per il flusso di traffico nella direzione inversa.  Utilizzare regole senza stato per il traffico tra i punti seguenti:Use stateless rules for traffic between the following points:

* Subnet di cloud privati
* Subnet locale e subnet del cloud privato
* Traffico Internet dai cloud privati

### <a name="stateful"></a>Con stato

 Una regola con stato è a conoscenza delle connessioni che la attraversano. Viene creato un record di flusso per le connessioni esistenti. La comunicazione è consentita o negata in base allo stato di connessione del record di flusso.  Utilizzare questo tipo di regola per gli indirizzi IP pubblici per filtrare il traffico proveniente da Internet.Use this rule type for public IP addresses to filter traffic from the Internet.

### <a name="default-rules"></a>Regole predefinite

Le regole predefinite seguenti vengono create in ogni tabella del firewall.

|Priorità|Nome|Tracciamento dello stato|Direction|Tipo di traffico|Protocollo|Source (Sorgente)|Porta di origine|Destination|Porta di destinazione|Azione|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|consenti tutto a internet|Con stato|In uscita|IP pubblico o traffico Internet|Tutti|Qualsiasi|Qualsiasi|Qualsiasi|Qualsiasi|Allow|
|65001|nega tutto da internet|Con stato|In ingresso|IP pubblico o traffico Internet|Tutti|Qualsiasi|Qualsiasi|Qualsiasi|Qualsiasi|Nega|
|65002|consenti tutte le reti Intranet|Senza stato|In uscita|Traffico interno o VPN del cloud privato|Tutti|Qualsiasi|Qualsiasi|Qualsiasi|Qualsiasi|Allow|
|65003|consenti all-all-from-intranet|Senza stato|In ingresso|Traffico interno o VPN del cloud privato|Tutti|Qualsiasi|Qualsiasi|Qualsiasi|Qualsiasi|Allow|

## <a name="next-steps"></a>Passaggi successivi

* [Configurare tabelle e regole del firewall](firewall.md)
