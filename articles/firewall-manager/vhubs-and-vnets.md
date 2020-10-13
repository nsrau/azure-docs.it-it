---
title: Quali sono le opzioni di architettura di Azure Firewall Manager?
description: Confrontare e contrapporre usando la rete virtuale Hub o le architetture di hub virtuale protette con gestione firewall di Azure.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: 71ff23e749139087f24da406474403167dcc1c0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563149"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Quali sono le opzioni di architettura di Azure Firewall Manager?

Azure Firewall Manager può fornire la gestione della sicurezza per due tipi di architettura di rete:

- **hub virtuale protetto**

   Un [hub di rete WAN virtuale di Azure](../virtual-wan/virtual-wan-about.md#resources) è una risorsa gestita da Microsoft che consente di creare facilmente architetture hub-spoke. Quando i criteri di sicurezza e routing sono associati a un hub di questo tipo, viene definito *[hub virtuale protetto](secured-virtual-hub.md)*. 
- **rete virtuale Hub**

   È una rete virtuale di Azure standard creata e gestita dall'utente. Quando a un hub di questo tipo sono associati criteri di sicurezza, l'hub è definito *rete virtuale hub*. Attualmente sono supportati solo criteri firewall di Azure. È possibile eseguire il peering di reti virtuali spoke contenenti i servizi e i server del carico di lavoro, nonché gestire firewall in reti virtuali autonome non connesse tramite peering a spoke.

## <a name="comparison"></a>Confronto

Nella tabella seguente vengono confrontate queste due opzioni di architettura che consentono di decidere quale sia la scelta più adatta ai requisiti di sicurezza dell'organizzazione:


|  |**Rete virtuale hub**|**Hub virtuale protetto**  |
|---------|---------|---------|
|**Risorsa sottostante**     |Rete virtuale|Hub WAN virtuale|
|**Hub & spoke**     |Usa il peering di rete virtuale|Connessione automatizzata tramite la rete virtuale dell'hub|
|**Connettività locale**     |Gateway VPN fino a 10 Gbps e 30 connessioni S2S; ExpressRoute|Gateway VPN più scalabile fino a 20 Gbps e 1000 connessioni S2S. Express Route|
|**Connettività del ramo automatizzata con SDWAN**      |Non supportato|Supportato|
|**Hub per area**     |Più reti virtuali per area|Hub virtuale singolo per area. Più hub possibili con più WAN virtuali|
|**Firewall di Azure: più indirizzi IP pubblici**      |Fornito dal cliente|Generato automaticamente|
|**zone di disponibilità del firewall di Azure**     |Supportato|Non ancora disponibile|
|**Sicurezza Internet avanzata con partner di sicurezza di terze parti come partner di servizi**     |Connettività VPN gestita e stabilita dal cliente al servizio partner preferito|Automatizzato tramite l'esperienza di gestione dei partner e del flusso del provider del partner sicurezza|
|**Gestione centralizzata delle route per instradare il traffico all'hub**     |Route definita dall'utente gestita dal cliente|Supportato tramite BGP|
|**Supporto per più provider di sicurezza**|Supportato con il tunneling forzato configurato manualmente per i firewall di terze parti|Supporto automatizzato per due provider di sicurezza: firewall di Azure per il filtraggio del traffico privato e di terze parti per il filtro Internet|
|**Web Application Firewall nel gateway applicazione** |Supportato nella rete virtuale|Attualmente supportata nella rete spoke|
|**Appliance virtuale di rete**|Supportato nella rete virtuale|Attualmente supportata nella rete spoke|
|**Supporto standard di protezione DDoS di Azure**|Sì|No|

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica della distribuzione di Gestione firewall di Azure](deployment-overview.md)
- Informazioni sugli [hub virtuali protetti](secured-virtual-hub.md).