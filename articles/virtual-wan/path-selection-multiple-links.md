---
title: Selezione percorso di Azure tra più collegamenti ISP
titleSuffix: Azure Virtual WAN
description: Scopri di più sulla selezione dei percorsi di Azure e sulla rete WAN virtuale
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: cherylmc
ms.openlocfilehash: a83a050b65f5673a86dd07ec13842f6009d2c8dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753347"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>Selezione percorso di Azure tra più collegamenti ISP

La rete WAN virtuale di Azure offre agli utenti la possibilità di includere informazioni sui collegamenti in un sito VPN, abilitando scenari in cui la soluzione per dispositivi VPN/SD-WAN può programmare criteri specifici del ramo per indirizzare il traffico tra vari collegamenti in Azure. Questa operazione è denominata **Selezione percorso di Azure**.

## <a name="architecture"></a>Architecture

Per comprendere il funzionamento della selezione dei percorsi di Azure, è possibile usare l'esempio di un sito VPN WAN virtuale e di una connessione da sito a sito.

Un sito VPN rappresenta il dispositivo SD-WAN/VPN locale con informazioni come l'indirizzo IP pubblico, il modello e il nome del dispositivo e così via. Il sito VPN locale effettivo può includere più collegamenti ISP che possono essere inclusi anche nelle informazioni sul sito VPN della rete WAN virtuale. In questo modo è possibile visualizzare le informazioni sul collegamento in Azure.

Una connessione IPsec da sito a sito in arrivo in una VPN di WAN virtuale termina nelle istanze del gateway VPN all'interno di un hub virtuale. Una connessione da sito a sito rappresenta la connettività tra il sito VPN e il gateway VPN di Azure. È costituito da una o più connessioni di collegamento. Ogni connessione di collegamento è costituita da due tunnel con ogni tunnel che termina in un'istanza univoca del gateway VPN WAN virtuale di Azure. È possibile configurare fino a quattro connessioni di collegamento nella connessione da sito a sito, che consente di avere fino a otto tunnel all'interno di una connessione da sito a sito. Azure supporta fino a 2000 tunnel che terminano all'interno di un singolo gateway VPN WAN virtuale.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="Diagramma a collegamento multiplo":::

Questa figura mostra il collegamento multiplo in un sito che si connette alla rete WAN virtuale di Azure. In questo diagramma:

* Sono disponibili due collegamenti ISP nel ramo locale (dispositivo VPN/SD-WAN). Ogni collegamento ISP corrisponde a una connessione di collegamento.

* Si presuppone che il dispositivo VPN/SD-WAN locale di gestione clienti supporti IKEv1 o IKEv2 IPsec.

* Ogni connessione WAN virtuale da sito a sito di Azure è costituita da connessioni a collegamenti all'interno di se stessa. Una connessione supporta fino a quattro connessioni di collegamento. Azure addebita una tariffa per le unità di connessione per la connessione WAN virtuale. Non sono previsti addebiti per le connessioni di collegamento.

* Ogni connessione di collegamento, a sua volta, è costituita da due tunnel IPsec che possono terminare in due istanze diverse del gateway VPN WAN virtuale. I gateway sono configurati come gateway Active-Active per la resilienza. Ogni connessione di collegamento è necessaria per avere un indirizzo IP univoco e un IP del peering BGP. Nel diagramma, il tunnel 0 può terminare con l'istanza 0 e il tunnel 1 può terminare nell'istanza 1.

* I dispositivi Branch che forniscono la selezione del percorso possono abilitare i criteri appropriati nella soluzione di gestione dei rami per indirizzare il traffico tra più collegamenti ad Azure. Ad esempio, il collegamento ISP 1 può essere usato per il traffico con priorità più elevata e il collegamento ISP 2 può essere usato come backup.

* È importante notare che la VPN dell'HUB virtuale usa ECMP (routing multipath a costo uguale) in tutti i tunnel di terminazione.

## <a name="next-steps"></a>Passaggi successivi

Vedere le [domande frequenti su Azure](virtual-wan-faq.md).