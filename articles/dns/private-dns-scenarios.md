---
title: Scenari per le zone private di DNS di Azure
description: Panoramica degli scenari comuni per l'uso di Zone private di DNS di Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 03/15/2018
ms.author: victorh
ms.openlocfilehash: d84da36ad6b1ef3e2a507a0944aac583861d5ccb
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162168"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scenari di Zone private di DNS di Azure
Il servizio Zone private di DNS di Azure consente la risoluzione dei nomi all'interno di una rete virtuale e tra reti virtuali. In questo articolo vengono esaminati alcuni scenari comuni che è possibile attuare con questa funzionalità. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenario: Risoluzione dei nomi in una singola rete virtuale
In questo scenario si ha una rete virtuale in Azure che contiene diverse risorse di Azure, tra cui macchine virtuali (VM). Si vuole risolvere le risorse all'interno della rete virtuale tramite un nome di dominio specifico (zona DNS) ed è necessario che la risoluzione dei nomi sia privata e non accessibile da Internet. Per le macchine virtuali all'interno della rete virtuale, è inoltre necessario che Azure esegua la registrazione automatica nella zona DNS. 

Questo scenario è illustrato di seguito. La rete virtuale denominata "A" contiene due macchine virtuali (VNETA-VM1 e VNETA-VM2). A ognuna di esse sono associati indirizzi IP privati. Una volta creata una zona privata denominata contoso.com e collegata la rete virtuale come rete virtuale di registrazione, DNS di Azure crea automaticamente due record A nella zona, come illustrato. A questo punto, le query DNS da VNETA-VM1 per la risoluzione di VNETA-VM2.contoso.com riceveranno una risposta DNS che contiene l'IP privato di VNETA-VM2. Una query DNS inversa (PTR) per l'IP privato di VNETA-VM1 (10.0.0.1) eseguita da VNETA-VM2 riceverà inoltre una risposta DNS contenente il nome di VNETA-VM1, come previsto. 

![Risoluzione in una singola rete virtuale](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenario: Risoluzione dei nomi tra reti virtuali

Questo scenario rappresenta il caso più comune in cui è necessario associare una zona privata a più reti virtuali. Tale scenario si adatta ad architetture come il modello hub-spoke, in cui è presente una rete virtuale hub centrale a cui sono connesse diverse altre reti virtuali spoke. La rete virtuale hub centrale può essere collegata come rete virtuale di registrazione a una zona privata e le reti virtuali spoke possono essere collegate come reti virtuali di risoluzione. 

La figura seguente mostra una versione semplificata di questo scenario in cui sono presenti solo due reti virtuali, A e B. A è designata come rete virtuale di registrazione e B è designata come rete virtuale di risoluzione. La finalità per entrambe le reti virtuali è quella di condividere una zona comune contoso.com. Quando la zona viene creata e le reti virtuali di risoluzione e di registrazione vengono collegate alla zona, Azure registra automaticamente i record DNS per le macchine virtuali (VNETA-VM1 e VNETA-VM2) dalla rete virtuale A. È anche possibile aggiungere manualmente i record DNS nella zona per le macchine virtuali nella rete virtuale di risoluzione B. Con questa configurazione, si può osservare il comportamento seguente per le query DNS dirette e inverse:
* Una query DNS da VNETB-VM1 nella rete virtuale di risoluzione B, per VNETA-VM1.contoso.com, riceverà una risposta DNS che contiene l'IP privato di VNETA-VM1.
* Una query DNS inversa (PTR) da VNETB-VM2 nella rete virtuale di risoluzione B, per 10.1.0.1, riceverà una risposta DNS che contiene il nome di dominio completo (FQDN) di VNETB-VM1.contoso.com. Il motivo è che le query DNS inverse hanno come ambito la stessa rete virtuale. 
* Una query DNS inversa (PTR) da VNETB-VM3 nella rete virtuale di risoluzione B, per 10.0.0.1, riceverà NXDOMAIN. Il motivo è che le query DNS inverse hanno come ambito solo la stessa rete virtuale. 


![Risoluzione in più reti virtuali](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenario: Funzionalità split-horizon

Questo scenario prevede un caso d'uso in cui si vuole ottenere un comportamento di risoluzione DNS diverso a seconda di dove si trova il client (all'interno di Azure o in Internet) per la stessa zona DNS. Si potrebbe ad esempio avere una versione pubblica e una privata dell'applicazione, con caratteristiche o comportamenti diversi, ma si vuole usare lo stesso nome di dominio per entrambe le versioni. È possibile realizzare questo scenario con DNS di Azure creando una zona DNS pubblica e una zona privata, con lo stesso nome.

La figura seguente illustra questo scenario. Si ha una rete virtuale A con due macchine virtuali (VNETA-VM1 e VNETA-VM2) che hanno entrambe indirizzi IP privati e indirizzi IP pubblici allocati. Si crea una zona DNS pubblica denominata contoso.com e si registrano gli indirizzi IP pubblici per queste macchine virtuali come record DNS all'interno della zona. Si crea anche una zona DNS privata denominata contoso.com specificando A come rete virtuale di registrazione. Azure registra automaticamente le macchine virtuali come record A nella zona privata, che puntano ai relativi indirizzi IP privati.

A questo punto, quando un client Internet esegue una query DNS per cercare VNETA-VM1.contoso.com, Azure restituisce il record IP pubblico dalla zona pubblica. Se la stessa query DNS viene eseguita da un'altra macchina virtuale (ad esempio, VNETA-VM2) nella stessa rete virtuale A, Azure restituisce il record IP privato dalla zona privata. 

![Risoluzione split-horizon](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle zone DNS private, vedere [Using Azure DNS for private domains](private-dns-overview.md) (Uso di Azure DNS per domini privati).

Informazioni su come [creare una zona DNS privata](./private-dns-getstarted-powershell.md) in DNS di Azure.

Per informazioni sui record e le zone DNS visitare la pagina [Panoramica delle zone e dei record DNS](dns-zones-records.md).

Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.

