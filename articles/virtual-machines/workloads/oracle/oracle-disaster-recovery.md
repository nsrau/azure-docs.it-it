---
title: Panoramica di uno scenario di ripristino di emergenza di Oracle nell'ambiente Azure | Microsoft Docs
description: Scenario di ripristino di emergenza per un'istanza di Database Oracle 12c nell'ambiente Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.openlocfilehash: aede44d964994bd2ef9b46a133b1f1ee303ef309
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Ripristino di emergenza per un'istanza di Database Oracle 12c in un ambiente Azure

## <a name="assumptions"></a>Presupposti

- Conoscenza degli ambienti Azure e della progettazione di Oracle Data Guard.


## <a name="goals"></a>Obiettivi
- Progettare la topologia e la configurazione che soddisfano i requisiti di ripristino di emergenza.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenario 1: Siti primari e di ripristino di emergenza in Azure

Un cliente ha un database Oracle configurato nel sito primario. Un sito di ripristino di emergenza si trova in un'area diversa. Il cliente usa Oracle Data Guard per eseguire un ripristino rapido tra questi siti. Il sito primario ha anche un database secondario per la creazione di report e altri usi. 

### <a name="topology"></a>Topologia

Ecco un riepilogo della configurazione di Azure:

- Due siti (un sito primario e un sito di ripristino di emergenza)
- Due reti virtuali
- Due database Oracle con Data Guard (primario e standby)
- Due database Oracle con Golden Gate o Data Guard (solo sito primario)
- Due servizi dell'applicazione, uno primario e uno nel sito di ripristino di emergenza
- Un *set di disponibilità*, usato per il servizio dell'applicazione e il database nel sito primario
- Un jumpbox in ogni sito, che limita l'accesso alla rete privata e consente l'accesso solo a un amministratore
- Un jumpbox, un servizio dell'applicazione, un database e un gateway VPN in subnet separate
- Gruppo di sicurezza di rete applicato nelle subnet dell'applicazione e del database

![Schermata della pagina di topologia del ripristino di emergenza](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenario 2: Sito primario locale e sito di ripristino di emergenza in Azure

Il cliente ha una configurazione del database Oracle locale nel sito primario. Il sito di un ripristino di emergenza è in Azure. Oracle Data Guard viene usato per eseguire un ripristino rapido tra questi siti. Il sito primario ha anche un database secondario per la creazione di report e altri usi. 

Per questo tipo di configurazione esistono due approcci.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Approccio 1: Connessioni dirette tra siti locali e Azure, che richiedono porte TCP aperte sul firewall 

Le connessioni dirette non sono consigliate perché espongono le porte TCP al mondo esterno.

#### <a name="topology"></a>Topologia

Il seguente è un riepilogo della configurazione di Azure:

- Un sito di ripristino di emergenza 
- Una rete virtuale
- Un database Oracle con Data Guard (attivo)
- Un servizio dell'applicazione nel sito di ripristino di emergenza
- Un jumpbox, che limita l'accesso alla rete privata e consente l'accesso solo a un amministratore
- Un jumpbox, un servizio dell'applicazione, un database e un gateway VPN in subnet separate
- Gruppo di sicurezza di rete applicato nelle subnet dell'applicazione e del database
- Un criterio o una regola del gruppo di sicurezza di rete per consentire la porta TCP 1521 in ingresso (o una porta definita dall'utente)
- Un criterio o una regola del gruppo di sicurezza di rete per limitare l'accesso alla rete virtuale solo all'indirizzo IP o agli indirizzi locali (database o applicazione)

![Schermata della pagina di topologia del ripristino di emergenza](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Approccio 2: VPN da sito a sito
La VPN da sito a sito è un approccio migliore. Per altre informazioni sulla configurazione di una VPN, vedere [Creare una rete virtuale con una connessione VPN da sito a sito usando l'interfaccia della riga di comando](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologia

Il seguente è un riepilogo della configurazione di Azure:

- Un sito di ripristino di emergenza 
- Una rete virtuale 
- Un database Oracle con Data Guard (attivo)
- Un servizio dell'applicazione nel sito di ripristino di emergenza
- Un jumpbox, che limita l'accesso alla rete privata e consente l'accesso solo a un amministratore
- Un jumpbox, un servizio dell'applicazione, un database e un gateway VPN in subnet separate
- Gruppo di sicurezza di rete applicato nelle subnet dell'applicazione e del database
- Connessione VPN da sito a sito tra rete locale e Azure

![Schermata della pagina di topologia del ripristino di emergenza](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Informazioni aggiuntive

- [Progettare e implementare un database Oracle in Azure](oracle-design.md)
- [Configurare Oracle Data Guard](configure-oracle-dataguard.md)
- [Configurare Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Backup e ripristino di Oracle](oracle-backup-recovery.md)


## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Creare VM a disponibilità elevata](../../linux/create-cli-complete.md)
- [Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](../../linux/cli-samples.md)
