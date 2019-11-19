---
title: Logica di elaborazione delle regole del Firewall di Azure
description: Firewall di Azure include regole NAT, regole di rete e regole di applicazione. Le regole vengono elaborate in base al tipo.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166785"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logica di elaborazione delle regole del Firewall di Azure
Firewall di Azure include regole NAT, regole di rete e regole di applicazione. Le regole vengono elaborate in base al tipo.


## <a name="network-rules-and-applications-rules"></a>Regole di rete e di applicazione 
Le regole di rete vengono applicate per prime, seguite da quelle di applicazione. L'elaborazione delle regole non avviene a ciclo continuo. Se le regole di rete trovano una corrispondenza, le regole di applicazione non vengono elaborate.  Se non esiste alcuna corrispondenza con una regola di rete e se il protocollo dei pacchetti è HTTP/HTTPS, il pacchetto viene quindi valutato dalle regole di applicazione. Se ancora non viene trovata una corrispondenza, il pacchetto viene valutato in base alla raccolta regole dell'infrastruttura. Se non è ancora presente alcuna corrispondenza, il pacchetto viene rifiutato per impostazione predefinita.

## <a name="nat-rules"></a>Regole NAT
La connettività in ingresso può essere abilitata configurando DNAT (Destination Network Address Translation) come descritto in [Esercitazione: Filtrare il traffico in ingresso con DNAT di Firewall di Azure tramite il portale di Azure](tutorial-firewall-dnat.md). Le regole DNAT vengono applicate per prime. Se viene trovata una corrispondenza, viene aggiunta in modo implicito una regola di rete corrispondente per consentire il traffico convertito. È possibile sostituire questo comportamento aggiungendo in modo esplicito una raccolta regole di rete con regole di negazione corrispondenti al traffico convertito. Per queste connessioni non vengono applicate regole di applicazione.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare Firewall di Azure](tutorial-firewall-deploy-portal.md).