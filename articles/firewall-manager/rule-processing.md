---
title: Logica di elaborazione della regola di gestione firewall di Azure
description: Informazioni sulla logica di elaborazione delle regole del Firewall di Azure
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 9184bf7baa85420e067edb4c0aafccb7e6711225
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512181"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logica di elaborazione delle regole del Firewall di Azure

Firewall di Azure include regole NAT, regole di rete e regole di applicazione. Le regole vengono elaborate in base al tipo.

## <a name="network-rules-and-applications-rules"></a>Regole di rete e di applicazione

Le regole di rete vengono applicate per prime, seguite da quelle di applicazione. L'elaborazione delle regole non avviene a ciclo continuo. Quindi, se viene trovata una corrispondenza nelle regole di rete, le regole dell'applicazione non vengono elaborate.  Se nessuna regola di rete corrisponde e se il protocollo del pacchetto è HTTP/HTTPS, il pacchetto viene valutato dalle regole dell'applicazione. Se ancora non viene trovata una corrispondenza, il pacchetto viene valutato in base alla raccolta regole dell'infrastruttura. Se non è ancora presente alcuna corrispondenza, il pacchetto viene negato per impostazione predefinita.

## <a name="nat-rules"></a>Regole NAT

La connettività in ingresso può essere abilitata configurando DNAT (Destination Network Address Translation) come descritto in [Esercitazione: Filtrare il traffico in ingresso con DNAT di Firewall di Azure tramite il portale di Azure](../firewall/tutorial-firewall-dnat.md). Le regole DNAT vengono applicate per prime. Se viene trovata una corrispondenza, viene aggiunta in modo implicito una regola di rete corrispondente per consentire il traffico convertito. È possibile sostituire questo comportamento aggiungendo in modo esplicito una raccolta regole di rete con regole di negazione corrispondenti al traffico convertito. Per queste connessioni non vengono applicate regole di applicazione.

## <a name="inherited-rules"></a>Regole ereditate

Le raccolte di regole di rete ereditate da un criterio padre sono sempre classificate in ordine di priorità sopra le raccolte di regole di rete definite come parte del nuovo criterio. La stessa logica si applica anche alle raccolte di regole dell'applicazione. Le raccolte di regole di rete, tuttavia, vengono sempre elaborate prima delle raccolte di regole dell'applicazione indipendentemente dall'ereditarietà.

Per impostazione predefinita, i criteri ereditano la modalità di intelligence per le minacce dei criteri padre. È possibile eseguire l'override impostando la modalità di intelligence per le minacce su un valore diverso nella pagina Impostazioni criteri. La sostituzione è consentita solo con un valore più restrittivo. Se, ad esempio, il criterio padre è impostato su *avviso solo*, è possibile configurare questo criterio locale per l' *avviso e il rifiuto*, ma non è possibile disattivarlo.

## <a name="next-steps"></a>Passaggi successivi

- [Scopri di più su Azure Firewall Manager](overview.md)