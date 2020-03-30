---
title: Logica di elaborazione delle regole del Firewall di Azure
description: Informazioni sulla logica di elaborazione delle regole del Firewall di Azure
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518203"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logica di elaborazione delle regole del Firewall di Azure

Firewall di Azure include regole NAT, regole di rete e regole di applicazione. Le regole vengono elaborate in base al tipo.

## <a name="network-rules-and-applications-rules"></a>Regole di rete e di applicazione

Le regole di rete vengono applicate per prime, seguite da quelle di applicazione. L'elaborazione delle regole non avviene a ciclo continuo. Se le regole di rete trovano una corrispondenza, le regole di applicazione non vengono elaborate.  Se non esiste alcuna corrispondenza con una regola di rete e se il protocollo dei pacchetti è HTTP/HTTPS, il pacchetto viene quindi valutato dalle regole di applicazione. Se ancora non viene trovata una corrispondenza, il pacchetto viene valutato in base alla raccolta regole dell'infrastruttura. Se non è ancora presente alcuna corrispondenza, il pacchetto viene rifiutato per impostazione predefinita.

## <a name="nat-rules"></a>Regole NAT

La connettività in ingresso può essere abilitata configurando DNAT (Destination Network Address Translation) come descritto in [Esercitazione: Filtrare il traffico in ingresso con DNAT di Firewall di Azure tramite il portale di Azure](../firewall/tutorial-firewall-dnat.md). Le regole DNAT vengono applicate per prime. Se viene trovata una corrispondenza, viene aggiunta in modo implicito una regola di rete corrispondente per consentire il traffico convertito. È possibile sostituire questo comportamento aggiungendo in modo esplicito una raccolta regole di rete con regole di negazione corrispondenti al traffico convertito. Per queste connessioni non vengono applicate regole di applicazione.

## <a name="inherited-rules"></a>Regole ereditate

Le raccolte di regole di rete ereditate da un criterio padre vengono sempre classificate in ordine di priorità al di sopra delle raccolte di regole di rete definite come parte dei nuovi criteri. La stessa logica si applica anche alle raccolte di regole dell'applicazione. Le raccolte di regole di rete, tuttavia, vengono sempre elaborate prima delle raccolte di regole dell'applicazione indipendentemente dall'ereditarietà.

Per impostazione predefinita, i criteri ereditano la modalità di intelligence delle minacce dei criteri padre. È possibile eseguire l'override impostando la modalità Di Intelligence per le minacce su un valore diverso nella pagina delle impostazioni dei criteri. È possibile eseguire l'override solo con un valore più rigoroso. Ad esempio, se il criterio padre è impostato su *Solo avvisi*, è possibile configurare questo criterio locale su Invia avviso *e nega*, ma non è possibile disattivarlo.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sull'anteprima di Azure Firewall ManagerLearn more about Azure Firewall Manager Preview](overview.md)