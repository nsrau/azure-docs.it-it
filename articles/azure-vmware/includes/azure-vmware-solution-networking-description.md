---
title: Rete e connettività della soluzione Azure VMware
description: Descrizione delle funzionalità di rete e connettività della soluzione Azure VMware.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92925034"
---
<!-- Used in introduction.md and concepts-networking.md -->

La soluzione Azure VMware offre un ambiente cloud privato accessibile da risorse o ambienti locali e basati su Azure. La connettività viene fornita da servizi come Azure ExpressRoute e da connessioni VPN. Per abilitare questi servizi sono necessari specifici intervalli di indirizzi di rete e porte del firewall.

Quando si distribuisce un cloud privato, vengono create reti private per la gestione, il provisioning e vMotion. Usare queste reti private per accedere a vCenter e NSX-T Manager e per la distribuzione o la migrazione tramite vMotion delle macchine virtuali.  Per connettere i cloud privati agli ambienti locali viene usato il servizio Copertura globale di ExpressRoute. Per stabilire la connessione è necessaria una rete virtuale con un circuito ExpressRoute nella sottoscrizione.

Le risorse, ad esempio server Web e macchine virtuali, sono accessibili in Internet tramite la funzionalità degli indirizzi IP pubblici della rete WAN virtuale di Azure.  Per impostazione predefinita, l'accesso a Internet è disabilitato per i nuovi cloud privati. Per altre informazioni, vedere [Come usare la funzionalità degli indirizzi IP pubblici nella soluzione Azure VMware](../public-ip-usage.md).