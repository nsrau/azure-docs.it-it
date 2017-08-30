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
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e42841b126a9df9240bec3f489589d5ce4a6db80
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="limitations-of-azure-cloud-shell"></a>Limitazioni di Azure Cloud Shell
Di seguito vengono descritte le limitazioni note di Azure Cloud Shell:

## <a name="system-state-and-persistence"></a>Persistenza e stato del sistema
Il computer che distribuisce la sessione Cloud Shell è temporaneo e viene riciclato dopo 20 minuti di inattività della sessione. Cloud Shell richiede l'implementazione di una condivisione file. La sottoscrizione, quindi, deve essere in grado di configurare le risorse di archiviazione per accedere a Cloud Shell. Altre considerazioni di cui tenere conto:
* Con l'archiviazione montata vengono conservate soltanto le modifiche apportate all'interno della directory `$Home` o `clouddrive`.
* Le condivisioni file possono essere implementate solo dall'interno dell'[area assegnata](persisting-shell-storage.md#mount-a-new-clouddrive).
* File di Azure supporta solo account di archiviazione con ridondanza locale e account di archiviazione con ridondanza geografica.

## <a name="user-permissions"></a>Autorizzazioni utente
Le autorizzazioni sono impostate come utenti normali senza accesso SUDO. Qualsiasi installazione esterna alla directory `$Home` non verrà conservata.
Anche se alcuni comandi all'interno della directory `clouddrive`, come `git clone`, non hanno le autorizzazioni appropriate, la directory `$Home` dispone delle autorizzazioni necessarie.

## <a name="browser-support"></a>Supporto browser
Cloud Shell supporta le versioni più recenti di Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. Safari in modalità privata non è supportato.

## <a name="copy-and-paste"></a>Copiare e incollare
Per copiare e incollare collegamenti in Cloud Shell in ambiente Windows non è possibile usare le combinazioni di tasti CTRL+V e CTRL+C, ma è necessario usare, rispettivamente, CTRL+INS e MAIUSC+INS.

Sono disponibili anche le opzioni copia/incolla facendo clic con il pulsante destro del mouse, anche se soggette all'accesso agli Appunti specifici del browser.

## <a name="editing-bashrc"></a>Modifica di .bashrc
Fare attenzione quando si modifica il file con estensione bashrc, poiché questa operazione può provocare errori imprevisti in Cloud Shell.

## <a name="bashhistory"></a>.bash_history
È possibile che la cronologia dei comandi bash sia incoerente a causa dell'interruzione della sessione Cloud Shell o di sessioni simultanee.

## <a name="usage-limits"></a>Limiti di consumo
Cloud Shell è pensato per l'uso interattivo e qualsiasi sessione non interattiva in esecuzione prolungata viene quindi interrotta senza preavviso.

## <a name="network-connectivity"></a>Connettività di rete
Qualsiasi latenza in Cloud Shell è soggetta alla connettività Internet locale e Cloud Shell continuerà a tentare di eseguire le istruzioni inviate.

## <a name="next-steps"></a>Passaggi successivi
[Avvio rapido di Cloud Shell](quickstart.md)

