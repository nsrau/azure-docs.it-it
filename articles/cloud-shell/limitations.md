---
title: Limitazioni di Azure Cloud Shell (anteprima) | Microsoft Docs
description: Panoramica delle limitazioni di Azure Cloud Shell
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 926e6b1024a75fc29cfecfb6a02550e7fbd9007b
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="limitations-for-azure-cloud-shell"></a>Limitazioni per Azure Cloud Shell
Di seguito vengono indicate le limitazioni note di Azure Cloud Shell.

## <a name="system-state-and-persistence"></a>Persistenza e stato del sistema
Il computer che distribuisce la sessione Cloud Shell è temporaneo e viene riciclato dopo 20 minuti di inattività della sessione. Cloud Shell richiede l'implementazione di una condivisione file. Di conseguenza la sottoscrizione deve essere in grado di effettuare il provisioning delle risorse di archiviazione per accedere a Cloud Shell.
* Con l'archiviazione montata vengono conservate soltanto le modifiche apportate all'interno della directory `$Home` o `clouddrive`
  * Le condivisioni file possono essere implementate solo dall'interno dell'[area assegnata](persisting-shell-storage.md#pre-requisites-for-manual-mounting)
  * File di Azure supporta solo gli account di archiviazione con ridondanza locale e geografica

## <a name="user-permissions"></a>Autorizzazioni utente
Le autorizzazioni sono impostate come utenti normali senza accesso SUDO. Qualsiasi installazione esterna a $Home non verrà conservata.
Alcuni comandi come `git clone` all'interno della directory `clouddrive` non dispongono delle autorizzazioni appropriate, mentre la directory $Home le ha.

## <a name="browser-support"></a>Supporto browser
Cloud Shell supporta le versioni più recenti di Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. Safari in modalità privata non è supportato.

## <a name="copy-and-paste"></a>Copiare e incollare
Per copiare e incollare, le combinazioni di tasti Ctrl+V e CTRL+C tipiche dei sistemi Windows non funzionano; usare, invece, le combinazioni di tasti Ctrl+Ins e Maius+Ins.
Sono inoltre disponibili le opzioni copia/incolla facendo clic con il pulsante destro del mouse, anche se soggette all'accesso agli Appunti specifici del browser.

## <a name="editing-bashrc"></a>Modifica di .bashrc
Fare attenzione quando si modifica .bashrc in quanto può provocare errori imprevisti per Cloud Shell.

## <a name="bashhistory"></a>.bash_history
La cronologia dei comandi bash potrebbe essere incoerente a causa dell'interruzione della sessione Cloud Shell o delle sessioni simultanee.

## <a name="usage-limits"></a>Limiti di consumo
Cloud Shell è pensato per l'uso interattivo, pertanto tutte le sessioni non interattive in esecuzione prolungata vengono interrotte senza alcun avviso.

## <a name="network-connectivity"></a>Connettività di rete
Qualsiasi latenza in Cloud Shell è soggetta alla connettività internet locale, Cloud Shell continuerà a tentare di funzionare con le istruzioni inviate.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Cloud Shell](quickstart.md)
