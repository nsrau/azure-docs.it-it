---
title: Risoluzione dei problemi che possono verificarsi durante il failback in locale da Azure e la successiva riprotezione in Azure | Microsoft Docs
description: "In questo articolo vengono descritte le soluzioni ai problemi più comuni che possono verificarsi quando si esegue il fail back in locale da Azure e durante la riprotezione"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>Risoluzione degli errori segnalati durante il processo di failback
Il failback è composto essenzialmente da due passaggi. Il primo consiste nella riprotezione delle macchine virtuali da Azure a locale, in secondo consiste nell'esecuzione vera e propria del failback da Azure a locale.

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>Risoluzione degli errori durante la riprotezione di una macchina virtuale in locale, dopo il failover
Durante la riprotezione di una macchina virtuale in Azure è possibile che l'utente riceva uno dei seguenti errori. Per risolvere i problemi, usare i passaggi descritti per ogni condizione di errore.


### <a name="error-code-95226"></a>Codice di errore 95226

*La riprotezione non è riuscita perché la macchina virtuale di Azure non è in grado di raggiungere il server di configurazione locale.*

Ciò si verifica quando 
1. La macchina virtuale di Azure non ha potuto raggiungere il server di configurazione locale e di conseguenza è stato impossibile individuarla e registrarla nel server di configurazione. 
2. Il servizio dell'applicazione InMage Scout nella macchina virtuale di Azure che deve essere in esecuzione per comunicare al server di configurazione locale potrebbe non eseguire il failover post.

Per risolvere il problema
1. È necessario assicurarsi che la rete della macchina virtuale di Azure sia configurata in modo che la macchina virtuale possa comunicare con il server di configurazione locale. A tale scopo, configurare una VPN da sito a sito nel data center locale o configurare una connessione ExpressRoute con peering privato sulla rete virtuale della macchina virtuale di Azure. 
2. Se si dispone già di una rete configurata in modo che la macchina virtuale di Azure possa comunicare con il server di configurazione locale, accedere alla macchina virtuale e verificare l'opzione 'InMage Scout Application Service'. Se si osserva che InMage Scout Application Service non è in esecuzione, avviare manualmente il servizio e verificare che il tipo di avvio sia impostato su Automatico.

### <a name="error-code-78052"></a>Codice di errore 78052
La riprotezione ha esito negativo e viene visualizzato il messaggio di errore: *Non è stato possibile completare l'abilitazione della protezione per la macchina virtuale*.

Questa situazione può verificarsi per due motivi
1. La macchina virtuale da riproteggere è Windows Server 2016. Questo sistema operativo non è attualmente supportato per il failback, ma lo sarà presto.
2. Esiste già una macchina virtuale con lo stesso nome nel server di destinazione master.

Per risolvere questo problema è possibile selezionare un server di destinazione master diverso in un host diverso, per cui la riprotezione creerà la macchina in un host diverso, in cui i nomi non sono in conflitto. È anche possibile usare vMotion nel server di destinazione master in modo da reindirizzarlo verso un host diverso, in cui non si verificherà il conflitto di nomi. Se la macchina virtuale esistente è una macchina errata, è possibile rinominarla in modo che la nuova macchina virtuale possa essere creata nello stesso host ESXi.

### <a name="error-code-78093"></a>Codice di errore 78093

*La macchina virtuale non è in esecuzione, è bloccata o non è accessibile.*

Per la riprotezione di una macchina virtuale su cui è stato eseguito il failover di nuovo in locale, è necessario che la macchina virtuale di Azure sia in esecuzione, in modo che il servizio di mobilità sia registrato con il server di configurazione locale e possa avviare la replica mediante la comunicazione con il server di processo. Se il computer si trova su una rete non corretta o non è in esecuzione (bloccato o in arresto), il server di configurazione non riesce a raggiungere il servizio di mobilità nella macchina virtuale per avviare la riprotezione. È possibile riavviare la macchina virtuale in modo che questa riesca a riavviare la comunicazione locale. Riavviare il processo di riprotezione dopo l'avvio della macchina virtuale di Azure

### <a name="error-code-8061"></a>Codice di errore 8061

*L'archivio dati non è accessibile dall'host ESXi.*

Fare riferimento ai [prerequisiti di destinazione master](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) e agli [archivi dati di supporto](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) per il failback


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>Risoluzione degli errori che possono verificarsi durante l'esecuzione del failback di una macchina virtuale di Azure in locale
Durante il failback di una macchina virtuale di Azure in locale è possibile che l'utente riceva uno dei seguenti errori. Per risolvere i problemi, usare i passaggi descritti per ogni condizione di errore.

### <a name="error-code-8038"></a>Codice di errore 8038

*Non è stato possibile accedere alla macchina virtuale locale a causa dell'errore*

Questo avviene quando si accede alla macchina virtuale locale su un host in cui non è stato effettuato il provisioning di una quantità di memoria sufficiente.

Per risolvere il problema

1. È possibile effettuare il provisioning di una quantità maggiore di memoria nell'host ESXi.
1. Spostare la macchina virtuale su un altro host ESXi in cui sia disponibile memoria sufficiente per avviare la macchina virtuale.