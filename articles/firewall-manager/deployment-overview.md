---
title: Panoramica della distribuzione di Gestione firewall di Azure
description: Informazioni sui passaggi di distribuzione generali necessari per Gestione firewall di Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079099"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Panoramica della distribuzione di Gestione firewall di Azure

Per distribuire Gestione firewall di Azure è possibile procedere in più modi, ma è consigliabile attenersi al processo generale descritto di seguito.

## <a name="general-deployment-process"></a>Processo generale di distribuzione

### <a name="hub-virtual-networks"></a>Reti virtuali hub

1.  Creare criteri firewall

    - Creare nuovi criteri
<br>*or*<br>
    - Ottenere criteri di base e personalizzare criteri locali
<br>*or*<br>
    - Importare regole da un'istanza esistente di Firewall di Azure. Assicurarsi di rimuovere le regole NAT dai criteri da applicare in più firewall
1. Creare un'architettura hub and spoke
   - Creare una rete virtuale hub con Gestione firewall di Azure e connettervi tramite peering le reti virtuali spoke usando il peering di reti virtuali
<br>*or*<br>
    - Creare una rete virtuale e aggiungere connessioni di rete virtuale, quindi connettervi tramite peering le reti virtuali spoke usando il peering di reti virtuali

3. Selezionare i provider di sicurezza e associare i criteri firewall. Attualmente, l'unico provider supportato è Firewall di Azure.

   - Eseguire questa operazione durante la creazione di una rete virtuale hub.
<br>*or*<br>
    - Convertire una rete virtuale esistente in rete virtuale hub. È anche possibile convertire più reti virtuali.

4. Configurare route definite dall'utente per indirizzare il traffico al firewall della rete virtuale hub.


### <a name="secured-virtual-hubs"></a>Hub virtuali protetti

1. Creare un'architettura hub and spoke

   - Creare un hub virtuale protetto con Gestione firewall di Azure e aggiungere connessioni di rete virtuale.<br>*or*<br>
   - Creare un hub di rete WAN virtuale e aggiungere connessioni di rete virtuale.
2. Selezionare provider di sicurezza

   - Operazione eseguita durante la creazione di un hub virtuale protetto.<br>*or*<br>
   - Convertire un hub di rete WAN virtuale esistente in un hub virtuale protetto.
3. Creare un criterio firewall e associarlo all'hub

   - Applicabile solo se si usa Firewall di Azure.
   - I criteri di sicurezza come servizio (SECaaS) di terze parti vengono configurati tramite l'esperienza di gestione dei partner.
4. Configurare le impostazioni della route per instradare il traffico all'hub protetto

   - È possibile instradare facilmente il traffico all'hub protetto per filtrare e accedere senza route definite dall'utente alle reti virtuali spoke usando la pagina delle impostazioni della route dell'hub virtuale protetto.

> [!NOTE]
> - Non è possibile disporre di più di un hub per ogni WAN virtuale per area. Per ottenere questo risultato, è tuttavia possibile aggiungere più WAN virtuali nell'area.
> - Non è possibile avere spazi IP sovrapposti per gli hub in una WAN virtuale.
> - Le connessioni di rete virtuale dell'hub devono trovarsi nella stessa area dell'hub.
>
> Per altri problemi noti, vedere [Informazioni su Gestione firewall di Azure](overview.md#known-issues)

## <a name="convert-virtual-networks"></a>Convertire reti virtuali

Le informazioni seguenti si applicano se si converte una rete virtuale esistente in una rete virtuale hub:

- Se nella rete virtuale è presente un'istanza di Firewall di Azure, è necessario selezionare un criterio firewall da associare al firewall esistente. Lo stato di provisioning del firewall verrà aggiornato mentre il criterio firewall sostituisce le regole del firewall. Durante lo stato di provisioning il firewall continua l'elaborazione del traffico e non ha tempi di inattività. Per importare regole esistenti in un criterio firewall, è possibile usare Gestione firewall o Azure PowerShell.
- Se alla rete virtuale non è associata un'istanza di Firewall di Azure, viene distribuito un firewall e il criterio del firewall viene associato al nuovo firewall.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Proteggere la rete cloud con Gestione firewall di Azure usando il portale di Azure](secure-cloud-network.md)