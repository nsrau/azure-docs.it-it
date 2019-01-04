---
title: TCP Reset di Load Balancer per inattività in Azure
titlesuffix: Azure Load Balancer
description: Load Balancer con pacchetti TCP RST bidirezionali per timeout di inattività
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: kumud
ms.openlocfilehash: 0b3e8fc72eb22a67c0672be19f60d4956d3377b7
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257296"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Load Balancer con TCP Reset per inattività (anteprima pubblica)

È possibile usare [Load Balancer Standard](load-balancer-standard-overview.md) per creare un comportamento dell'applicazione più prevedibile per gli scenari con TCP Reset bidirezionali (pacchetto TCP RST) per ogni timeout di inattività configurabile.  Il comportamento predefinito di Load Balancer prevede l'eliminazione trasparente dei flussi quando viene raggiunto il timeout di inattività di un flusso.

![TCP Reset di Load Balancer](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>La funzionalità Load Balancer con TCP Reset per timeout di inattività è attualmente disponibile come anteprima pubblica in un set limitato di [aree](#regions). Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
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

## <a name="regions"></a> Disponibilità in base all'area geografica

Questo parametro è attualmente attivo nelle aree seguenti.  Nelle aree non incluse in questo elenco, il parametro non ha effetto.

| Region |
|---|
| Asia sudorientale |
| Brasile meridionale |
| Canada centrale |
| Europa occidentale |
| India centrale |
| India occidentale |
| Giappone occidentale |
| Corea del Sud centrale |
| Corea del Sud meridionale |
| Regno Unito settentrionale |
| Regno Unito meridionale 2 |
| Stati Uniti orientali |
| Stati Uniti orientali 2 |
| Stati Uniti settentrionali |
| Stati Uniti occidentali |

Questa tabella verrà aggiornata con l'estensione dell'anteprima ad altre aree.  

## <a name="limitations"></a>Limitazioni

- [Disponibilità in base all'area geografica](#regions) limitata.
- Non è possibile usare il portale per configurare o visualizzare TCP Reset.  Usare in alternativa i modelli, l'API REST, l'interfaccia della riga di comando di Azure 2.0 o PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure Load Balancer Standard](load-balancer-standard-overview.md).
- Altre informazioni sulle [regole in uscita](load-balancer-outbound-rules-overview.md).
