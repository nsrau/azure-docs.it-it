---
title: Anteprima di Desktop virtuale di Windows host pool bilanciamento del carico metodi - Azure
description: Host pool bilanciamento del carico metodi per un ambiente di anteprima di Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 8b18224339654c067d8ab9b543fa49a9c7d55ddd
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400199"
---
# <a name="host-pool-load-balancing-methods"></a>Metodo di bilanciamento del carico per i pool di host

Anteprima di Desktop virtuale Windows supporta due metodi di bilanciamento del carico. Ogni metodo determina quali host sessione sarà ospitata una sessione utente quando si connettono a una risorsa in un pool di host.

I seguenti metodi di bilanciamento del carico sono disponibili in Desktop virtuali Windows:

- Il bilanciamento del carico breadth-first consente di distribuire uniformemente le sessioni utente tra gli host della sessione in un pool di host.
- Il bilanciamento del carico di profondità consente saturare un host di sessione con le sessioni utente in un pool di host. Dopo la prima sessione raggiunge il limite della soglia di sessione, il bilanciamento del carico indirizza tutte le nuove connessioni utente all'host della sessione successiva nel pool di host finché non raggiunge il limite e così via.

Ogni pool di host è possibile configurare solo un tipo di bilanciamento del carico specifico. Tuttavia, i comportamenti seguenti indipendentemente da quale host pool di bilanciamento del carico condividono i metodi in cui si trovano:

- Se un utente già dispone di una sessione nel pool di host ed è la riconnessione alla sessione, il servizio di bilanciamento del carico verrà correttamente reindirizzarle all'host di sessione con la sessione esistente. Questo comportamento si applica anche se AllowNewConnections proprietà tale sessione host è impostata su False.
- Se un utente non ha ancora una sessione nel pool di host, quindi il servizio di bilanciamento del carico non prendere in considerazione gli host sessione la cui proprietà AllowNewConnections viene impostata su False durante il bilanciamento del carico.

## <a name="breadth-first-load-balancing-method"></a>Metodo di bilanciamento del carico breadth-first

Il metodo di bilanciamento del carico breadth-first consente di distribuire le connessioni utente per ottimizzare questo scenario. Questo metodo è ideale per le organizzazioni che vogliono offrire la migliore esperienza per utenti che si connettono al proprio ambiente di desktop virtuali in pool.

Il metodo breadth-first esegue una query prima di tutto gli host sessione che consentono nuove connessioni. Il metodo seleziona quindi l'host di sessione con il minor numero di sessioni. Se è presente un valore equivalente, il metodo selezionato il primo host sessione nella query.

## <a name="depth-first-load-balancing-method"></a>Metodo di bilanciamento del carico di profondità

Il metodo di bilanciamento del carico depth-first consente saturare host sessione uno alla volta per ottimizzare questo scenario. Questo metodo è ideale per le organizzazioni attento ai costi che desidera un controllo più granulare sul numero di macchine virtuali che è stato allocato per un pool di host.

Il metodo depth-first esegue una query prima di tutto gli host sessione che consente nuove connessioni e non hanno superato il limite di durata massima della sessione. Il metodo seleziona quindi l'host di sessione con il numero massimo di sessioni. Se è presente un valore equivalente, il metodo selezionato il primo host sessione nella query.