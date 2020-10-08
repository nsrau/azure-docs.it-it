---
title: Reimpostazione Load Balancer TCP e timeout di inattività in Azure
titleSuffix: Azure Load Balancer
description: In questo articolo vengono fornite informazioni sulle Azure Load Balancer con i pacchetti RST TCP bidirezionali in timeout di inattività.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 060048bf786f424d5df6eb8fb4813877acb0fea0
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823215"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Reimpostazione Load Balancer TCP e timeout di inattività

È possibile usare [Load Balancer Standard](load-balancer-standard-overview.md) per creare un comportamento delle applicazioni più prevedibile per gli scenari abilitando TCP Reset per timeout di inattività per una determinata regola. Il comportamento predefinito di Load Balancer prevede l'eliminazione trasparente dei flussi quando viene raggiunto il timeout di inattività di un flusso.  Se si abilita questa funzionalità, Load Balancer invierà TCP Reset bidirezionali (pacchetto RST TCP) in caso di timeout per inattività.  In questo modo gli endpoint dell'applicazione verranno informati che si è verificato il timeout della connessione e quest'ultima non è più utilizzabile.  Gli endpoint possono stabilire immediatamente una nuova connessione, se necessario.

![TCP Reset di Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>Ripristino TCP

Si modifica questo comportamento predefinito e si abilita l'invio di TCP Reset per timeout di inattività per regole NAT in ingresso, regole di bilanciamento del carico e [regole in uscita](https://aka.ms/lboutboundrules).  Quando questo comportamento viene abilitato per ogni regola, Load Balancer invierà TCP Reset bidirezionali (pacchetti TCP RST) agli endpoint client e server al momento del timeout di inattività per tutti i flussi corrispondenti.

Gli endpoint che ricevono pacchetti TCP RST chiudono il socket corrispondente immediatamente. Gli endpoint ricevono così una notifica immediata del rilascio della connessione e le eventuali comunicazioni future sulla stessa connessione TCP avranno esito negativo.  Le applicazioni possono ripulire le connessioni alla chiusura del socket e ristabilire le connessioni all'occorrenza senza attendere il timeout della connessione TCP.

Per molti scenari, questo potrebbe ridurre la necessità di inviare keep-alive TCP (o di livello applicazione) per aggiornare il timeout di inattività di un flusso. 

Se la durata dell'inattività supera quella consentita dalla configurazione o l'applicazione mostra un comportamento indesiderato con TCP Reset abilitato, potrebbe comunque essere necessario usare keep-alive TCP (o al livello applicazione) per monitorare l'attività delle connessioni TCP.  Inoltre, i keep-alive possono anche rimanere utili in caso di uso di proxy per la connessione nel percorso, in particolare i keep-alive al livello applicazione.  

Esaminare attentamente l'intero scenario per decidere se l'abilitazione dei TCP Reset e la regolazione del timeout di inattività risultano vantaggiosi e se possono essere necessari passaggi aggiuntivi per garantire il comportamento desiderato dell'applicazione.

## <a name="configurable-tcp-idle-timeout"></a>Timeout di inattività TCP configurabile

Azure Load Balancer presenta l'intervallo di timeout di inattività seguente:
-  da 4 minuti a 100 minuti per le regole in uscita
-  da 4 minuti a 30 minuti per Load Balancer le regole e le regole NAT in ingresso

Per impostazione predefinita, questa proprietà è impostata su 4 minuti. Se un periodo di inattività è più lungo del valore di timeout, non ci sono garanzie che venga mantenuta la sessione TCP o HTTP tra il client e il servizio cloud.

Quando la connessione viene chiusa, l'applicazione client potrebbe ricevere il messaggio di errore seguente: "La connessione sottostante è stata chiusa: una connessione che doveva restare attiva è stata chiusa dal server in modo imprevisto".

Una prassi comune consiste nell'usare una connessione TCP keep-alive per mantenere la connessione attiva per un periodo più lungo. Per altre informazioni, vedere questi [esempi .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Con la connessione keep-alive abilitata, i pacchetti vengono inviati durante i periodi di inattività della connessione. I pacchetti keep-alive garantiscono che il valore del timeout di inattività non venga raggiunto e che la connessione sia mantenuta per un lungo periodo.

L'impostazione funziona solo per le connessioni in entrata. Per evitare di perdere la connessione, configurare l'impostazione keep-alive TCP con un intervallo minore rispetto all'impostazione di timeout di inattività o aumentare il valore del timeout di inattività. Per supportare questi scenari, è stato aggiunto il supporto per un timeout di inattività configurabile.

La connessione TCP keep-alive è adatta per gli scenari non vincolati alla durata della batteria, mentre non è consigliabile per le applicazioni mobili. L'uso di un'impostazione keep-alive TCP in un'applicazione per dispositivi mobili può far scaricare più velocemente la batteria del dispositivo.


## <a name="limitations"></a>Limitazioni

- Il ripristino TCP viene inviato solo durante la connessione TCP nello stato stabilito.
- La reimpostazione TCP non viene inviata per i bilanciamento del carico interni con le porte a disponibilità elevata configurate.
- Il timeout di inattività TCP non influisce sulle regole di bilanciamento del carico sul protocollo UDP.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [Load Balancer standard](load-balancer-standard-overview.md).
- Informazioni sulle [regole in uscita](load-balancer-outbound-rules-overview.md).
- [Configurare la RST TCP per il timeout di inattività](load-balancer-tcp-idle-timeout.md)
