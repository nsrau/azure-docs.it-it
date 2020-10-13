---
title: Bilanciamento del carico del pool host del desktop virtuale Windows-Azure
description: Informazioni sui metodi di bilanciamento del carico del pool host per un ambiente desktop virtuale di Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd8f9e4a3ef63cd97f96af3d4f96a2bb65c3cd09
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951860"
---
# <a name="host-pool-load-balancing-methods"></a>Metodo di bilanciamento del carico per i pool di host

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

Il desktop virtuale Windows supporta due metodi di bilanciamento del carico. Ogni metodo determina quale host sessione ospiterà la sessione di un utente quando si connetteranno a una risorsa in un pool host.

Nel desktop virtuale di Windows sono disponibili i metodi di bilanciamento del carico seguenti:

- Il bilanciamento del carico con il primo livello consente di distribuire uniformemente le sessioni utente tra gli host della sessione in un pool host.
- Il bilanciamento del carico depth-first consente di saturare un host sessione con sessioni utente in un pool host. Una volta che la prima sessione raggiunge la soglia di limite della sessione, il servizio di bilanciamento del carico indirizza le nuove connessioni utente all'host della sessione successiva nel pool host fino al raggiungimento del limite e così via.

Ogni pool host può configurare solo un tipo di bilanciamento del carico specifico. Tuttavia, entrambi i metodi di bilanciamento del carico condividono i comportamenti seguenti indipendentemente dal pool host in cui si trovano:

- Se un utente dispone già di una sessione nel pool host e si sta riconnettendo a tale sessione, il servizio di bilanciamento del carico reinstallerà correttamente l'host sessione con la sessione esistente. Questo comportamento si applica anche se la proprietà AllowNewConnections dell'host sessione è impostata su false.
- Se un utente non dispone già di una sessione nel pool host, il servizio di bilanciamento del carico non considera gli host di sessione la cui proprietà AllowNewConnections è impostata su false durante il bilanciamento del carico.

## <a name="breadth-first-load-balancing-method"></a>Metodo di bilanciamento del carico di primo respiro

Il metodo di bilanciamento del carico con la prima larghezza consente di distribuire le connessioni utente per ottimizzare questo scenario. Questo metodo è ideale per le organizzazioni che desiderano offrire la migliore esperienza per gli utenti che si connettono al proprio ambiente di desktop virtuale in pool.

Il metodo di primo respiro esegue prima una query sugli host di sessione che consentono nuove connessioni. Il metodo seleziona quindi un host sessione in modo casuale dalla metà del set di host di sessione con il minor numero di sessioni. Se ad esempio sono presenti nove macchine con 11, 12, 13, 14, 15, 16, 17, 18 e 19 sessioni, una nuova sessione creata non verrà automaticamente inviata al primo computer. Al contrario, può passare a uno dei primi cinque computer con il numero più basso di sessioni (11, 12, 13, 14, 15).

## <a name="depth-first-load-balancing-method"></a>Metodo di bilanciamento del carico depth-first

Il metodo di bilanciamento del carico depth-first consente di saturare un host di sessione alla volta per l'ottimizzazione per questo scenario. Questo metodo è ideale per le organizzazioni con costi ridotti che vogliono un controllo più granulare sul numero di macchine virtuali allocate per un pool host.

Il metodo depth-first esegue prima una query sugli host di sessione che consentono nuove connessioni e non hanno superato il limite massimo di sessioni. Il metodo seleziona quindi l'host della sessione con il numero più elevato di sessioni. Se è presente una cravatta, il metodo seleziona il primo host della sessione nella query.

>[!IMPORTANT]
>L'algoritmo di bilanciamento del carico depth-first distribuisce le sessioni agli host di sessione in base al limite massimo di host sessione. Questo parametro è obbligatorio quando si usa l'algoritmo di bilanciamento del carico con il primo livello di profondità. Per un'esperienza utente ottimale, assicurarsi di modificare il parametro limite massimo host sessione in un numero più adatto all'ambiente in uso.