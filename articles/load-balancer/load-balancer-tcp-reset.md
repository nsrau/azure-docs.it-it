---
title: TCP Reset di Load Balancer per inattività in Azure
titleSuffix: Azure Load Balancer
description: Con questo articolo, informazioni su Azure Load Balancer con pacchetti TCP RST bidirezionali in caso di timeout di inattività.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: d3d836ddea8d07a25ad09e6f19d9f17a680decd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294398"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Load Balancer con tcp Reset on Idle

È possibile usare [Load Balancer Standard](load-balancer-standard-overview.md) per creare un comportamento delle applicazioni più prevedibile per gli scenari abilitando TCP Reset per timeout di inattività per una determinata regola. Il comportamento predefinito di Load Balancer prevede l'eliminazione trasparente dei flussi quando viene raggiunto il timeout di inattività di un flusso.  Se si abilita questa funzionalità, Load Balancer invierà TCP Reset bidirezionali (pacchetto RST TCP) in caso di timeout per inattività.  In questo modo gli endpoint dell'applicazione verranno informati che si è verificato il timeout della connessione e quest'ultima non è più utilizzabile.  Gli endpoint possono stabilire immediatamente una nuova connessione, se necessario.

![TCP Reset di Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Si modifica questo comportamento predefinito e si abilita l'invio di TCP Reset per timeout di inattività per regole NAT in ingresso, regole di bilanciamento del carico e [regole in uscita](https://aka.ms/lboutboundrules).  Quando questo comportamento viene abilitato per ogni regola, Load Balancer invierà TCP Reset bidirezionali (pacchetti TCP RST) agli endpoint client e server al momento del timeout di inattività per tutti i flussi corrispondenti.

Gli endpoint che ricevono pacchetti TCP RST chiudono il socket corrispondente immediatamente. Gli endpoint ricevono così una notifica immediata del rilascio della connessione e le eventuali comunicazioni future sulla stessa connessione TCP avranno esito negativo.  Le applicazioni possono ripulire le connessioni alla chiusura del socket e ristabilire le connessioni all'occorrenza senza attendere il timeout della connessione TCP.

Per molti scenari, questo potrebbe ridurre la necessità di inviare keep-alive TCP (o di livello applicazione) per aggiornare il timeout di inattività di un flusso. 

Se la durata dell'inattività supera quella consentita dalla configurazione o l'applicazione mostra un comportamento indesiderato con TCP Reset abilitato, potrebbe comunque essere necessario usare keep-alive TCP (o al livello applicazione) per monitorare l'attività delle connessioni TCP.  Inoltre, i keep-alive possono anche rimanere utili in caso di uso di proxy per la connessione nel percorso, in particolare i keep-alive al livello applicazione.  

Esaminare attentamente l'intero scenario per decidere se l'abilitazione dei TCP Reset e la regolazione del timeout di inattività risultano vantaggiosi e se possono essere necessari passaggi aggiuntivi per garantire il comportamento desiderato dell'applicazione.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Abilitazione di TCP Reset in caso di timeout di inattività

Con l'API versione 2018-07-01, è possibile abilitare l'invio di TCP Reset bidirezionali per timeout di inattività in base a regole:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>Disponibilità dell'area

Disponibile in tutte le aree.

## <a name="limitations"></a>Limitazioni

- TCP RST inviato solo durante la connessione TCP nello stato ESTABLISHED.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure Load Balancer Standard](load-balancer-standard-overview.md).
- Altre informazioni sulle [regole in uscita](load-balancer-outbound-rules-overview.md).
- [Configurare TCP RST in base al timeout di inattivitàConfigure TCP RST on Idle Timeout](load-balancer-tcp-idle-timeout.md)
