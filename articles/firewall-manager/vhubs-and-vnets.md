---
title: Quali sono le opzioni dell'architettura di Azure Firewall Manager?
description: Confrontare e confrontare usando la rete virtuale hub o le architetture di hub virtuale protetto con Gestione firewall di Azure.Compare and contrast using hub virtual network or secured virtual hub architectures with Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444576"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Quali sono le opzioni dell'architettura di Azure Firewall Manager?

Gestione firewall di Azure può fornire la gestione della sicurezza per due tipi di architettura di rete:Azure Firewall Manager can provide security management for two network architecture types:

- **Hub virtuale protetto**

   Un [hub di rete WAN virtuale di Azure](../virtual-wan/virtual-wan-about.md#resources) è una risorsa gestita da Microsoft che consente di creare facilmente architetture hub-spoke. Quando i criteri di sicurezza e di routing sono associati a un hub di questo tipo, viene definito *[hub virtuale protetto.](secured-virtual-hub.md)* 
- **Rete virtuale hub**

   È una rete virtuale di Azure standard creata e gestita dall'utente. Quando a un hub di questo tipo sono associati criteri di sicurezza, l'hub è definito *rete virtuale hub*. Attualmente sono supportati solo criteri firewall di Azure. È possibile eseguire il peering di reti virtuali spoke contenenti i servizi e i server del carico di lavoro, nonché gestire firewall in reti virtuali autonome non connesse tramite peering a spoke.

## <a name="comparison"></a>Confronto

Nella tabella seguente vengono confrontate queste due opzioni di architettura e è possibile decidere quale è adatto ai requisiti di sicurezza dell'organizzazione:


|  |**Rete virtuale hub**|**Hub virtuale protetto**  |
|---------|---------|---------|
|**Risorsa sottostante**     |Rete virtuale|Hub WAN virtuale|
|**Hub & Spoke**     |Utilizza il peering della rete virtuale|Automatizzato usando la connessione di rete virtuale hubAutomated using hub virtual network connection|
|**Connettività locale**     |Gateway VPN fino a 10 Gbps e 30 connessioni S2S; ExpressRoute|Gateway VPN più scalabile fino a 20 Gbps e 1000 connessioni S2S; Percorso espresso|
|**Connettività automatizzata delle filiali tramite SDWAN**      |Non supportate|Supportato|
|**Hub per area**     |Più reti virtuali per areaMultiple Virtual Networks per region|Singolo hub virtuale per area. Più hub possibili con più WAN virtuali|
|**Firewall di Azure: più indirizzi IP pubblici**      |Fornito dal cliente|Generato automaticamente. Per essere disponibile da GA.|
|**Aree di disponibilità del firewall di AzureAzure Firewall Availability zones**     |Supportato|Non disponibile nell'anteprima. Da essere disponibile da GA|
|**Sicurezza Internet avanzata con partner di terze parti Security as a Service**     |Connettività VPN stabilita e gestita dal cliente al servizio partner prescelto|Automatizzato tramite il flusso dei partner di sicurezza affidabili e l'esperienza di gestione dei partner|
|**Gestione centralizzata dei percorsi per instradare il traffico verso l'hub**     |Route definita dall'utente gestita dal cliente|Supportato con BGP|
|**Web Application Firewall nel gateway applicazione** |Supportato nella rete virtualeSupported in Virtual Network|Attualmente supportato nella rete spoke|
|**Appliance virtuale di rete**|Supportato nella rete virtualeSupported in Virtual Network|Attualmente supportato nella rete spoke|

## <a name="next-steps"></a>Passaggi successivi

- Esaminare [Panoramica della distribuzione di Anteprima di Gestione firewall di Azure](deployment-overview.md)
- Informazioni sugli [hub virtuali protetti](secured-virtual-hub.md).