---
title: Panoramica dello scenario di ripristino di emergenza di Oracle nell'ambiente Azure | Microsoft Docs
description: Scenario di ripristino di emergenza del Database Oracle 12c nell'ambiente Azure.
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4f6fc6ed79606f0510bc3d6258205d1e6105de25
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="disaster-recovery-dr-of-oracle-12c-database-on-an-azure-environment"></a>Ripristino di emergenza del Database Oracle 12c nell'ambiente Azure

## <a name="assumptions"></a>Presupposti

- Informazioni sull'ambiente Azure e la progettazione di Oracle Data Guard


## <a name="goals"></a>Obiettivi
- Progettare la topologia e la configurazione che soddisfano i requisiti di ripristino di emergenza.

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenario 1: siti primari e di ripristino di emergenza in Azure

Un cliente ha una configurazione del database Oracle nel sito primario. Un sito di un ripristino di emergenza si trova in un'area diversa. Oracle Data Guard viene usato per eseguire un ripristino rapido tra questi siti. Il sito primario ha anche un database secondario per la creazione di report e altri usi. 

### <a name="topology"></a>Topologia

Ecco un riepilogo della configurazione di Azure.

- Due siti: sito primario e sito di ripristino di emergenza
- Due reti virtuali
- Due database di Oracle con Data Guard, database primario e standby
- Due database di Oracle con Golden Gate o Data Guard, solo per il sito primario
- Due servizi dell'applicazione nel sito primario e di ripristino di emergenza
- I "set di disponibilità" vengono usati per il servizio dell'applicazione e il database nel sito primario
- Uno Jumpbox in ogni sito, che limita l'accesso alla rete privata, consente solo l'accesso da amministratore
- Jumpbox, il servizio dell'applicazione, il database e il gateway VPN si trovano in subnet separate
- Il gruppo di sicurezza di rete verrà applicato nella subnet dell'applicazione e del database

![Schermata della pagina di topologia del ripristino di emergenza](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenario 2: sito primario locale e sito di ripristino di emergenza in Azure

Il cliente ha una configurazione del database Oracle locale nel sito primario. Il sito di un ripristino di emergenza è in Azure. Oracle Data Guard viene usato per eseguire un ripristino rapido tra questi siti. Il sito primario ha anche un database secondario per la creazione di report e altri usi. 

Per questo tipo di configurazione esistono due approcci.

### <a name="1-direct-connections-between-on-premises-and-azure-required-open-tcp-ports-on-firewall-this-approach-is-not-recommended-as-it-expose-the-tcp-ports-to-outside-world"></a>1. Connessioni dirette tra siti locali e Azure, sono richieste porte TCP aperte sul firewall. Questo approccio non è consigliato perché espone le porte TCP all'esterno.

### <a name="topology"></a>Topologia

Ecco un riepilogo della configurazione di Azure.

- Un sito, ovvero il sito di ripristino di emergenza
- Una rete virtuale
- Un database Oracle con Data Guard, attivo
- Un servizio dell'applicazione nel sito di ripristino di emergenza
- Un Jumpbox che limita l'accesso alla rete privata, consente l'accesso solo all'amministratore
- Jumpbox, il servizio dell'applicazione, il database e il gateway VPN si trovano in subnet separate
- Il gruppo di sicurezza di rete verrà applicato nella subnet dell'applicazione e del database
- Aggiungere criteri/regole del gruppo di sicurezza di rete per consentire l'ingresso dalla porta TCP 1521 o definito dall'utente
- Aggiungere criteri/regole del gruppo di sicurezza di rete per limitare l'accesso alla rete virtuale solo all'indirizzo IP/agli indirizzi locali, ovvero database o applicazione.

![Schermata della pagina di topologia del ripristino di emergenza](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="2-a-better-approach-is-using-site-to-site-vpn-more-information-about-setting-up-vpn-is-available-from-this-linkhttpsdocsmicrosoftcomen-usazurevpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-cli"></a>2. Un approccio migliore implica l'uso di una VPN da sito a sito. Altre informazioni sulla configurazione della connessione VPN sono disponibile a questo [collegamento](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)

### <a name="topology"></a>Topologia

Ecco un riepilogo della configurazione di Azure.

- Un sito, ovvero il sito di ripristino di emergenza
- Una rete virtuale
- Un database Oracle con Data Guard, attivo
- Un servizio dell'applicazione nel sito di ripristino di emergenza
- Un Jumpbox che limita l'accesso alla rete privata, consente l'accesso solo all'amministratore
- Jumpbox, il servizio dell'applicazione, il database e il gateway VPN si trovano in subnet separate
- Il gruppo di sicurezza di rete verrà applicato nella subnet dell'applicazione e del database
- Connessione VPN da sito a sito tra rete locale e Azure

![Schermata della pagina di topologia del ripristino di emergenza](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-readings"></a>Letture aggiuntive:

- [Design and implement Oracle database on Azure](oracle-design.md) (Progettare e implementare il database Oracle in Azure)
- [Configurare Oracle Data Guard](configure-oracle-dataguard.md)
- [Configurare Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Backup e ripristino di Oracle](oracle-backup-recovery.md)


## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: Creare VM a disponibilità elevata](../../linux/create-cli-complete.md)

[Esplorare gli esempi dell'interfaccia della riga di comando di Azure per la distribuzione della VM](../../linux/cli-samples.md)

