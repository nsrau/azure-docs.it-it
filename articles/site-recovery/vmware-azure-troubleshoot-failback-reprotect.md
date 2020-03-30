---
title: Risolvere i problemi di failback nel ripristino di emergenza della macchina virtuale VMware con Azure Site RecoveryTroubleshoot failback in VMware VM disaster recovery with Azure Site Recovery
description: Questo articolo descrive le soluzioni agli errori di failback e riprotezione che possono verificarsi durante il ripristino di emergenza di macchine virtuali VMware in Azure con Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b577b82585ffad0547818b4f19554a2f39cb830c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498094"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Risolvere i problemi di failback da Azure all'ambiente locale

In questo articolo viene illustrato come risolvere i problemi che possono verificarsi durante il failback di macchine virtuali di Azure all'infrastruttura VMware locale, dopo il failover ad Azure mediante [Azure Site Recovery](site-recovery-overview.md).

Il failback è composto essenzialmente da due passaggi. Innanzitutto, dopo il failover, è necessario riproteggere le macchine virtuali di Azure in locale, in modo da avviarne la replica. Il secondo passaggio consiste nell'eseguire un failover da Azure, in modo da eseguire il failback al sito locale.

## <a name="common-issues"></a>Problemi comuni

- Se si esegue l'individuazione di vCenter con utente di sola lettura e la protezione delle macchine virtuali, la protezione funziona e il failover viene eseguito. Durante la riprotezione, il failover ha esito negativo perché non è possibile individuare gli archivi dati. Se durante la riprotezione gli archivi dati non vengono elencati, è possibile che si sia verificato questo problema. Per risolverlo, è possibile aggiornare le credenziali di vCenter usando un account appropriato con le dovute autorizzazioni e quindi ripetere il processo.
- Quando si esegue il failback di una macchina virtuale Linux e l'esecuzione è locale, si nota che il pacchetto di gestione reti viene disinstallato dal computer. Tale installazione si verifica perché, quando la macchina virtuale viene ripristinata in Azure, il pacchetto di gestione reti viene rimosso.
- Se per la configurazione di una macchina virtuale Linux viene usato un indirizzo IP statico e viene eseguito il failover in Azure, l'indirizzo IP viene acquisito da DHCP. Se il failover viene eseguito in locale, la macchina virtuale continua a usare il protocollo DHCP per acquisire l'indirizzo IP. Se necessario, accedere manualmente al computer e quindi impostare di nuovo l'indirizzo IP su un indirizzo statico. Una macchina virtuale Windows può acquisire ancora l'indirizzo IP statico.
- Se si usa l'edizione gratuita di ESXi 5.5 o di vSphere 6 Hypervisor, è possibile effettuare il failover, ma il failback non riesce. Per abilitare il failback, scegliere l'aggiornamento alla licenza di valutazione del programma.
- Se non è possibile raggiungere il server di configurazione dal server di elaborazione, usare Telnet per verificare la connettività al server di configurazione sulla porta 443. È anche possibile provare a eseguire il ping del server di configurazione dal server di elaborazione. Un server di elaborazione deve avere anche un heartbeat quando è connesso al server di configurazione.
- Non è possibile eseguire il failback da Azure a un sito locale di un server Windows Server 2008 R2 SP1 protetto come server locale fisico.
- Non è possibile eseguire il failback nelle circostanze seguenti:
    - È stata eseguita la migrazione di macchine virtuali ad Azure. [Scopri di più](migrate-overview.md#what-do-we-mean-by-migration).
    - Una macchina virtuale è stata spostata in un altro gruppo di risorse.
    - La macchina virtuale Azure è stata eliminata.
    - È stata disabilitata la protezione della macchina virtuale.
    - La macchina virtuale è stata creata manualmente in Azure. Prima della riprotezione è necessario che la macchina virtuale sia stata inizialmente protetta in locale e che ne sia stato eseguito il failover in Azure.
    - È possibile eseguire il failback solo a un host ESXi. Non è possibile eseguire il failback di macchine virtuali VMware o server fisici a host Hyper-V, computer fisici o workstation VMware.


## <a name="troubleshoot-reprotection-errors"></a>Risolvere gli errori di riprotezione

Questa sezione illustra nel dettaglio i più comuni errori di riprotezione, con la relativa risoluzione.

### <a name="error-code-95226"></a>Codice di errore 95226

**La riprotezione non è riuscita perché la macchina virtuale di Azure non è in grado di raggiungere il server di configurazione locale.**

Questo errore si verifica quando:

* La macchina virtuale di Azure non riesce a connettersi al server di configurazione locale. Non è possibile individuare la macchina virtuale e registrarla nel server di configurazione.
* Il servizio dell'applicazione InMage Scout non è in esecuzione nella macchina virtuale di Azure dopo il failover. Il servizio è necessario per le comunicazioni con il server di configurazione locale.

Per risolvere il problema:

* Verificare che la rete delle macchine virtuali di Azure consenta alla macchina virtuale di Azure di comunicare con il server di configurazione locale. È possibile configurare una VPN da sito a sito nel data center locale o configurare una connessione Azure ExpressRoute con peering privato sulla rete virtuale della macchina virtuale di Azure.
* Se la macchina virtuale è in grado di comunicare con il server di configurazione locale, accedere alla macchina virtuale. Controllare quindi il servizio dell'applicazione InMage Scout. Se non risulta in esecuzione, avviare il servizio manualmente. Verificare che il tipo di avvio del servizio sia impostato su **Automatico**.

### <a name="error-code-78052"></a>Codice di errore 78052

**Non è stato possibile completare l'abilitazione della protezione per la macchina virtuale.**

Questo problema può verificarsi se esiste già una macchina virtuale con lo stesso nome nel server di destinazione master a cui si sta eseguendo il failback.

Per risolvere il problema:

* Selezionare un server di destinazione master diverso in un host diverso, in modo che la riprotezione crei la macchina virtuale in un host diverso in cui i nomi non siano in conflitto.
* È anche possibile usare vMotion per spostare il server di destinazione master in un host diverso, in cui non si verifica il conflitto di nomi. Se la macchina virtuale esistente è una macchina rimasta inutilizzata, è possibile rinominarla in modo che la nuova macchina virtuale possa essere creata nello stesso host ESXi.


### <a name="error-code-78093"></a>Codice di errore 78093

**La macchina virtuale non è in esecuzione, in stato bloccato o non è accessibile.**

Per risolvere il problema:

Per riproteggere la macchina virtuale sottoposta a failover, la machina virtuale di Azure deve essere eseguita in modo che il servizio Mobility venga registrato con il server di configurazione locale e possa avviare la replica mediante la comunicazione con il server di elaborazione. Se il computer si trova in una rete non corretta o non è in esecuzione (non risponde o si arresta), il server di configurazione non può raggiungere il servizio Mobility nella macchina virtuale per avviare la riprotezione.

* Riavviare la macchina virtuale in modo che possa a ristabilire la comunicazione in locale.
* Riavviare il processo di riprotezione dopo l'avvio della macchina virtuale di Azure.

### <a name="error-code-8061"></a>Codice di errore 8061

**L'archivio dati non è accessibile dall'host ESXi.**

Verificare i [prerequisiti del server di destinazione master](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) e gli archivi dati supportati per il failback.


## <a name="troubleshoot-failback-errors"></a>Risolvere gli errori di failback

Questa sezione descrive i più comuni errori che possono verificarsi durante il failback.

### <a name="error-code-8038"></a>Codice di errore 8038

**Impossibile visualizzare la macchina virtuale locale a causa dell'errore.**

Questo problema si verifica quando si accede alla macchina virtuale locale su un host in cui non è stato effettuato il provisioning di una quantità di memoria sufficiente. 

Per risolvere il problema:

* Effettuare il provisioning di una quantità maggiore di memoria nell'host ESXi.
* Inoltre, è possibile usare vMotion per spostare la macchina virtuale su un altro host ESXi in cui sia disponibile memoria sufficiente per avviare la macchina virtuale.
