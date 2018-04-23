---
title: File di inclusione
description: File di inclusione
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 226dfd9add69e8d89a030b858c819691d7b20627
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
Sebbene l'utilizzo di route del sistema faciliti il traffico automaticamente per la distribuzione, esistono casi in cui si desidera controllare il routing dei pacchetti tramite un dispositivo virtuale. Questo è possibile tramite la creazione di route definite dall'utente che specifichino l'hop successivo per i pacchetti che passano a una subnet specifica per accedere all'applicazione virtuale e tramite l’attivazione dell'inoltro IP per la macchina virtuale in esecuzione come dispositivo virtuale.

Alcuni dei casi in cui possono essere utilizzati dispositivi di rete:

* Monitoraggio del traffico con un sistema di rilevamento delle intrusioni (ID)
* Controllo del traffico con un firewall

Per altre informazioni su UDR, l’inoltro IP vedere [Route e inoltro IP definiti dall'utente](../articles/virtual-network/virtual-networks-udr-overview.md).

