---
title: Operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sulle reti virtuali di Azure | Microsoft Docs
description: Informazioni sulle operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sulle reti virtuali di Azure.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''

ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick

---
# Rete virtuale - Continuità aziendale
## Panoramica
Una rete virtuale è una rappresentazione logica della propria rete personalizzata nel cloud. Consente di definire il proprio spazio di indirizzi IP privato e segmentare la rete in subnet. Le reti virtuali vengono usate come limite di attendibilità per ospitare le risorse di calcolo quali macchine virtuali di Azure e servizi cloud (ruoli Web/di lavoro). Una rete virtuale consente la comunicazione IP privata diretta tra le risorse ospitate al suo interno. Può anche essere collegata a una rete locale tramite una delle opzioni ibride, ad esempio un gateway VPN o ExpressRoute.

Una rete virtuale viene creata nell'ambito di un'area. È possibile creare reti virtuali con lo stesso spazio di indirizzi in due aree diverse (ad esempio Stati Uniti orientali e Stati Uniti occidentali, ma non connetterle tra loro direttamente).

## Continuità aziendale
Le applicazioni potrebbero essere soggette a interruzioni di varia natura. Una determinata area potrebbe essere interessata da un'interruzione totale a causa di una calamità naturale o un'emergenza parziale dovuta a un errore di più dispositivi/servizi. L'impatto sul servizio di rete virtuale è diverso in ognuna di queste situazioni.

**D: Cosa fare in caso di interruzione di un'intera area? Ad esempio, nel caso di un'interruzione totale dovuta a una calamità naturale? Cosa accade alle reti virtuali ospitate nell'area?**

R: La rete virtuale e le risorse nell'area interessata restano inaccessibili durante il periodo di interruzione del servizio.

![Diagramma della rete virtuale semplice](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**D: In che modo è possibile ricreare la stessa rete virtuale in un'area diversa?**

R: La rete virtuale è una risorsa piuttosto semplice. Per creare una rete virtuale con lo stesso spazio di indirizzi in un'area diversa, è possibile richiamare le API di Azure. Per ricreare lo stesso ambiente presente nell'area interessata, è necessario eseguire chiamate API per ridistribuire i servizi cloud (ruoli Web/di lavoro) e le macchine virtuali. È inoltre necessario creare un gateway VPN e connettersi alla rete locale se si dispone di connettività locale (ad esempio in una distribuzione ibrida).

Le istruzioni per la creazione di una rete virtuale sono disponibili [qui](virtual-networks-create-vnet-arm-pportal.md).

**D: È possibile ricreare in anticipo una replica di una rete virtuale di una determinata area in un'altra area?**

R: Sì, è possibile creare in anticipo due reti virtuali usando lo stesso spazio di indirizzi IP privato e le stesse risorse in due aree diverse. Se il cliente stava ospitando servizi con connessione Internet nella rete virtuale, potrebbe aver impostato Gestione traffico in modo da distribuire a livello geografico il traffico all'area attiva. Tuttavia, un cliente non può connettere due reti virtuali con lo stesso spazio di indirizzi a una rete locale perché ciò comporterebbe problemi di routing. Al momento di un'emergenza e della perdita di una rete virtuale in un'area, il cliente può connettere l'altra rete virtuale disponibile con lo stesso spazio di indirizzi alla rete locale.

Le istruzioni per la creazione di una rete virtuale sono disponibili [qui](virtual-networks-create-vnet-arm-pportal.md).

<!---HONumber=AcomDC_0601_2016-->