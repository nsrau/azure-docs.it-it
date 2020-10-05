---
title: Rete e connettività della soluzione Azure VMware
description: Descrizione delle funzionalità di rete e connettività della soluzione Azure VMware.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91574458"
---
<!-- Used in introduction.md and concepts-networking.md -->

La soluzione Azure VMware offre un ambiente cloud privato accessibile da risorse o ambienti locali e basati su Azure. La connettività viene fornita da servizi come Azure ExpressRoute e da connessioni VPN. Per abilitare questi servizi sono necessari specifici intervalli di indirizzi di rete e porte del firewall.

Quando si distribuisce un cloud privato, vengono create reti private per la gestione, il provisioning e vMotion. Usare queste reti private per accedere a vCenter e NSX-T Manager e per la distribuzione o la migrazione tramite vMotion delle macchine virtuali.  Per connettere i cloud privati agli ambienti locali viene usato il servizio Copertura globale di ExpressRoute. Per stabilire la connessione è necessaria una rete virtuale con un circuito ExpressRoute nella sottoscrizione.



>[!NOTE]
>Viene effettuato il provisioning dell'accesso a Internet e ai servizi di Azure per l'utilizzo delle macchine virtuali nelle reti di produzione durante la distribuzione di un cloud privato.  Per impostazione predefinita, l'accesso a Internet è disabilitato per i nuovi cloud privati e può essere abilitato o disabilitato in qualsiasi momento.