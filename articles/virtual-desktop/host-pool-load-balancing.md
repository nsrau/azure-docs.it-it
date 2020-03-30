---
title: Windows Virtual Desktop host pool load-balancing - Azure
description: Metodi di bilanciamento del carico del pool host per un ambiente Windows Virtual Desktop.Host pool load-balancing methods for a Windows Virtual Desktop environment.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127866"
---
# <a name="host-pool-load-balancing-methods"></a>Metodo di bilanciamento del carico per i pool di host

Windows Virtual Desktop supporta due metodi di bilanciamento del carico. Ogni metodo determina quale host di sessione ospiterà la sessione di un utente quando si connette a una risorsa in un pool host.

In Windows Virtual Desktop sono disponibili i seguenti metodi di bilanciamento del carico:

- Il bilanciamento del carico breadth-first consente di distribuire uniformemente le sessioni utente tra gli host di sessione in un pool host.
- Il bilanciamento del carico con profondità consente di saturare un host di sessione con sessioni utente in un pool host. Quando la prima sessione raggiunge la soglia del limite di sessione, il servizio di bilanciamento del carico indirizza tutte le nuove connessioni utente all'host della sessione successiva nel pool host fino a quando non raggiunge il limite e così via.

Ogni pool host può configurare un solo tipo di bilanciamento del carico specifico. Tuttavia, entrambi i metodi di bilanciamento del carico condividono i comportamenti seguenti indipendentemente dal pool host in cui si trovano:However, both load-balancing methods share the following behaviors indipendentemente dal pool host in cui si trovano:

- Se un utente ha già una sessione nel pool host e si riconnette a tale sessione, il servizio di bilanciamento del carico li reindirizzerà correttamente all'host della sessione con la sessione esistente. Questo comportamento si applica anche se la proprietà AllowNewConnections dell'host di sessione è impostata su False.This behavior applies even if that session host's AllowNewConnections property is set to False.
- Se un utente non dispone già di una sessione nel pool host, il servizio di bilanciamento del carico non considererà gli host di sessione la cui proprietà AllowNewConnections è impostata su False durante il bilanciamento del carico.

## <a name="breadth-first-load-balancing-method"></a>Metodo di bilanciamento del carico Breadth-first

Il metodo di bilanciamento del carico in base all'ampiezza consente di distribuire le connessioni utente da ottimizzare per questo scenario. Questo metodo è ideale per le organizzazioni che desiderano offrire la migliore esperienza per gli utenti che si connettono al proprio ambiente desktop virtuale in pool.

Il metodo breadth-first esegue prima una query su host di sessione che consentono nuove connessioni. Il metodo seleziona quindi l'host della sessione con il minor numero di sessioni. Se è presente un pareggio, il metodo seleziona il primo host di sessione nella query.

## <a name="depth-first-load-balancing-method"></a>Metodo di bilanciamento del carico con primo profondità

Il metodo di bilanciamento del carico con priorità di primo utilizzo consente di saturare un host di sessione alla volta per ottimizzare questo scenario. Questo metodo è ideale per le organizzazioni consapevoli dei costi che desiderano un controllo più granulare sul numero di macchine virtuali allocate per un pool host.

Il metodo depth-first esegue innanzitutto una query sugli host di sessione che consentono nuove connessioni e non hanno superato il limite massimo di sessioni. Il metodo seleziona quindi l'host della sessione con il maggior numero di sessioni. Se è presente un pareggio, il metodo seleziona il primo host di sessione nella query.