---
title: Limitazioni di Azure Cloud Shell (anteprima) | Microsoft Docs
description: Panoramica delle limitazioni di Azure Cloud Shell
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: c007b73375c8c82248228f4e549c0ac95640d7ec
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017

---

# <a name="limitations-for-azure-cloud-shell"></a>Limitazioni per Azure Cloud Shell
Di seguito vengono indicate le limitazioni note di Azure Cloud Shell.

## <a name="system-state-and-persistence"></a>Persistenza e stato del sistema
Il computer che distribuisce la sessione Cloud Shell è temporaneo e viene riciclato dopo 10 minuti di inattività della sessione. Cloud Shell richiede l'implementazione di una condivisione file.
* Con l'archiviazione montata vengono conservate soltanto le modifiche apportate all'interno della directory `$Home` o `clouddrive`
  * Le condivisioni file possono essere implementate solo dall'interno dell'[area assegnata](persisting-shell-storage.md#pre-requisites-for-manual-mounting)
  * File di Azure supporta solo gli account di archiviazione con ridondanza locale e geografica

## <a name="user-permissions"></a>Autorizzazioni utente
Le autorizzazioni sono impostate come utenti normali senza accesso SUDO. Qualsiasi installazione esterna a $Home non verrà conservata.
Alcuni comandi come `git clone` all'interno della directory `clouddrive` non dispongono delle autorizzazioni appropriate, mentre la directory $Home le ha.

## <a name="browser-support"></a>Supporto browser
Cloud Shell supporta le versioni più recenti di Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. Safari in modalità privata non è supportato.

## <a name="copy-and-paste"></a>Copia e Incolla
Per copiare e incollare, le combinazioni di tasti Ctrl+V e CTRL+C tipiche dei sistemi Windows non funzionano; usare, invece, le combinazioni di tasti Ctrl+Ins e Maius+Ins.
Sono inoltre disponibili le opzioni copia/incolla facendo clic con il pulsante destro del mouse, anche se soggette all'accesso agli Appunti specifici del browser.

## <a name="usage-limits"></a>Limiti di consumo
Cloud Shell è pensato per l'uso interattivo, pertanto tutte le sessioni non interattive in esecuzione prolungata vengono interrotte senza alcun avviso.

## <a name="network-connectivity"></a>Connettività di rete
Qualsiasi latenza in Cloud Shell è soggetta alla connettività internet locale, Cloud Shell continuerà a tentare di funzionare con le istruzioni inviate.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Cloud Shell](quickstart.md)
