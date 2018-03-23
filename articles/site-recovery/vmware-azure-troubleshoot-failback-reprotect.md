---
title: Risoluzione dei problemi che possono verificarsi durante il failback di macchine virtuali di Azure a un'istanza di VMware locale con Azure Site Recovery | Microsoft Docs
description: In questo articolo vengono descritte le soluzioni ai più comuni errori di failback e riprotezione che possono verificarsi quando si esegue il failback da Azure a VMware mediante Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: rajanaki
ms.openlocfilehash: 6dcecce78de3caaefb40cb3fe4853d5d550163b4
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Risoluzione dei problemi di failback da Azure a VM VMware

In questo articolo viene illustrato come risolvere i problemi che possono verificarsi durante il failback di macchine virtuali di Azure all'infrastruttura VMware locale, dopo il failover ad Azure mediante [Azure Site Recovery](site-recovery-overview.md).

Il failback è composto essenzialmente da due passaggi. Dopo il failover, è necessario riproteggere le macchine virtuali di Azure in locale, in modo da avviarne la replica. Il secondo passaggio consiste nell'eseguire un failover da Azure, in modo da eseguire il failback al sito locale.

## <a name="troubleshoot-reprotection-errors"></a>Risolvere gli errori di riprotezione

Questa sezione illustra nel dettaglio i più comuni errori di riprotezione, con la relativa risoluzione.

### <a name="error-code-95226"></a>Codice di errore 95226

**La riprotezione non è riuscita perché la macchina virtuale di Azure non è in grado di raggiungere il server di configurazione locale.**

Questo errore si verifica quando:

1. La macchina virtuale di Azure non riesce a connettersi al server di configurazione locale. Non è possibile individuare la macchina virtuale e registrarla nel server di configurazione.
2. Il servizio dell'applicazione InMage Scout non è in esecuzione nella macchina virtuale di Azure dopo il failover. Il servizio è necessario per le comunicazioni con il server di configurazione locale.

Per risolvere il problema:

1. Verificare che la rete delle macchine virtuali di Azure consenta alla macchina virtuale di Azure di comunicare con il server di configurazione locale. A tale scopo, configurare una VPN da sito a sito nel data center locale o configurare una connessione ExpressRoute con peering privato sulla rete virtuale della macchina virtuale di Azure.
2. Se la macchina virtuale è in grado di comunicare con il server di configurazione locale, accedere alla macchina virtuale e verificare la voce relativa al servizio dell'applicazione InMage Scout. Se il servizio non è in esecuzione, avviarlo manualmente e verificare che il tipo di avvio del servizio sia impostato su Automatico.

### <a name="error-code-78052"></a>Codice di errore 78052

**Non è stato possibile completare l'abilitazione della protezione per la macchina virtuale.**

Questo errore può verificarsi se esiste già una macchina virtuale con lo stesso nome nel server di destinazione master a cui si sta eseguendo il failback.

Per risolvere il problema, eseguire questi passaggi:
1. Selezionare un server di destinazione master diverso in un host diverso, in modo che la riprotezione crei la macchina virtuale in un host diverso in cui i nomi non siano in conflitto.
2. È anche possibile usare vMotion nel server di destinazione master in modo da spostarlo in un host diverso, in cui non si verificherà il conflitto di nomi. Se la macchina virtuale esistente è una macchina rimasta inutilizzata, è possibile rinominarla in modo che la nuova macchina virtuale possa essere creata nello stesso host ESXi.

### <a name="error-code-78093"></a>Codice di errore 78093

**La macchina virtuale non è in esecuzione, è bloccata o non è accessibile.**

Per riproteggere una macchina virtuale dopo il failover, la macchina virtuale di Azure deve essere in esecuzione, in modo che il servizio Mobility venga registrato con il server di configurazione locale e possa avviare la replica mediante la comunicazione con il server di elaborazione. Se la macchina virtuale si trova su una rete non corretta o non è in esecuzione (bloccata o arrestata), il server di configurazione non sarà in grado di connettersi al servizio Mobility nella macchina virtuale per avviare la riprotezione.

1. Riavviare la macchina virtuale in modo che possa a ristabilire la comunicazione in locale.
2. Riavviare il processo di riprotezione dopo l'avvio della macchina virtuale di Azure

### <a name="error-code-8061"></a>Codice di errore 8061

**L'archivio dati non è accessibile dall'host ESXi.**

Verificare i [prerequisiti del server di destinazione master](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) e gli archivi dati supportati per il failback.


## <a name="troubleshoot-failback-errors"></a>Risolvere gli errori di failback

Questa sezione descrive i più comuni errori che possono verificarsi durante il failback.

### <a name="error-code-8038"></a>Codice di errore 8038

**Non è stato possibile accedere alla macchina virtuale locale a causa dell'errore**

Questo errore si verifica quando si accede alla macchina virtuale locale su un host in cui non è stato effettuato il provisioning di una quantità di memoria sufficiente. Per risolvere il problema:

1. Effettuare il provisioning di una quantità maggiore di memoria nell'host ESXi.
2. Inoltre, è possibile usare vMotion per spostare la macchina virtuale su un altro host ESXi in cui sia disponibile memoria sufficiente per avviare la macchina virtuale.
