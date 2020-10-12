---
title: 'Architettura: ripristino di emergenza di VMware/fisico in un sito secondario con Azure Site Recovery'
description: Questo articolo fornisce una panoramica dei componenti e dell'architettura usati durante il ripristino di emergenza di macchine virtuali VMware locali o di server fisici Windows/Linux in un sito VMware secondario con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 7c884ce839523706e67e4278f43e237e1a2b0580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87496968"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Architettura per la replica di server VMware o fisici in un sito locale secondario

Questo articolo descrive l'architettura e i processi usati per la replica, il failover e il ripristino di emergenza di macchine virtuali (VM) VMware locali o server Windows/Linux fisici in un sito VMware secondario usando [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componenti dell'architettura

**Area** | **Componente** | **Dettagli**
--- | --- | ---
**Azure** | Questo scenario viene distribuito con InMage Scout. | Per ottenere InMage Scout, è necessaria una sottoscrizione di Azure.<br/><br/> Dopo aver creato un insieme di credenziali di Servizi di ripristino, scaricare InMage Scout e installare gli aggiornamenti più recenti per configurare la distribuzione.
**Server di elaborazione** | Situato nel sito primario | Il server di elaborazione viene distribuito per gestire la memorizzazione nella cache, la compressione e l'ottimizzazione dei dati.<br/><br/> Gestisce anche l'installazione push di Unified Agent nei computer da proteggere.
**Server di configurazione** | Situato nel sito secondario | Il server di configurazione gestisce, configura e monitora la distribuzione usando il sito Web di gestione o la console vContinuum.
**Server vContinuum** | Facoltativa. Installato nella stessa posizione del server di configurazione. | Fornisce una console per la gestione e il monitoraggio dell'ambiente protetto.
**Server master di destinazione** | Situato nel sito secondario | Questo server contiene i dati replicati. Riceve i dati dal server di elaborazione e crea un computer di replica nel sito secondario e include i punti di conservazione dei dati.<br/><br/> Il numero di server di destinazione master necessari dipende dal numero di computer da proteggere.<br/><br/> Se si vuole eseguire il failback al sito primario, deve essere disponibile anche un server di destinazione master. Unified Agent viene installato in questo server.
**Server VMware ESX/ESXi e vCenter** |  Le macchine virtuali sono ospitate in host ESX/ESXi. Gli host vengono gestiti con un server vCenter. | È necessaria un'infrastruttura VMware per replicare le VM VMware.
**VM/server fisici** |  Unified Agent installato nei server fisici e nelle macchine virtuali VMware da replicare. | L'agente funge da provider di comunicazioni tra tutti i componenti.

## <a name="set-up-outbound-network-connectivity"></a>Configurare la connettività di rete in uscita

Affinché Site Recovery funzioni come previsto, è necessario modificare la connettività di rete in uscita per consentire la replica dell'ambiente.

> [!NOTE]
> Site Recovery non supporta l'uso di un proxy di autenticazione per controllare la connettività di rete.

### <a name="outbound-connectivity-for-urls"></a>Connettività in uscita per gli URL

Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi URL:

| **Nome**                  | **Commerciale**                               | **Enti pubblici**                                 | **Descrizione** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Archiviazione                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Consente la scrittura di dati dalla macchina virtuale nell'account di archiviazione della cache all'area di origine. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornisce l'autenticazione e l'autorizzazione per gli URL del servizio Site Recovery. |
| Replica               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Consente alla macchina virtuale di comunicare con il servizio Site Recovery. |
| Bus di servizio               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Consente alla macchina virtuale di scrivere i dati di diagnostica e monitoraggio di Site Recovery. |

## <a name="replication-process"></a>Processo di replica

1. Configurare i server del componente in ogni sito, ovvero configurazione, elaborazione, destinazione master, e installare Unified Agent nei computer da replicare.
2. Dopo la replica iniziale, l'agente installato in ogni computer invia le modifiche della replica differenziale al server di elaborazione.
3. Il server di elaborazione ottimizza i dati e li trasferisce nel server di destinazione master nel sito secondario. Il server di configurazione gestisce il processo di replica.

![Diagramma che illustra la replica di VM VMware e server fisici in un data center secondario](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Passaggi successivi

[Configurare](vmware-physical-secondary-disaster-recovery.md) il ripristino di emergenza di macchine virtuali VMware e server fisici in un sito secondario.
