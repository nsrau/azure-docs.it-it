---
title: Panoramica della distribuzione dell'anteprima di Gestione firewall di Azure
description: Informazioni sui passaggi di distribuzione generali necessari per l'anteprima di Gestione firewall di Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443126"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Panoramica della distribuzione dell'anteprima di Gestione firewall di Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Per distribuire l'anteprima di Gestione firewall di Azure è possibile procedere in più modi, ma è consigliabile eseguire il processo generale descritto di seguito.

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

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Proteggere la rete cloud con l'anteprima di Gestione firewall di Azure usando il portale di Azure](secure-cloud-network.md)