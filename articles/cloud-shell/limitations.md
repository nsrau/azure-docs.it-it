---
title: Limitazioni di Azure Cloud Shell (anteprima) | Microsoft Docs
description: Panoramica delle limitazioni di Azure Cloud Shell
services: azure
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
ms.date: 09/25/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: a1a220e27fc4baafa4184405c88f81fd2338b98a
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="limitations-of-azure-cloud-shell"></a>Limitazioni di Azure Cloud Shell
Di seguito vengono descritte le limitazioni note di Azure Cloud Shell:

## <a name="general-limitations"></a>Limitazioni generali

### <a name="system-state-and-persistence"></a>Persistenza e stato del sistema
Il computer che distribuisce la sessione Cloud Shell è temporaneo e viene riciclato dopo 20 minuti di inattività della sessione. Cloud Shell richiede l'implementazione di una condivisione file. La sottoscrizione, quindi, deve essere in grado di configurare le risorse di archiviazione per accedere a Cloud Shell. Altre considerazioni di cui tenere conto:
* Con l'archiviazione montata vengono rese persistenti soltanto le modifiche apportate all'interno della directory `clouddrive`. In Bash anche la directory `$Home` è resa persistente.
* Le condivisioni file possono essere implementate solo dall'interno dell'[area assegnata](persisting-shell-storage.md#mount-a-new-clouddrive).
  * In Bash, eseguire `env` per trovare l'area geografica impostata come `ACC_LOCATION`.
* File di Azure supporta solo account di archiviazione con ridondanza locale e account di archiviazione con ridondanza geografica.

### <a name="browser-support"></a>Supporto browser
Cloud Shell supporta le versioni più recenti di Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. Safari in modalità privata non è supportato.

### <a name="copy-and-paste"></a>Copiare e incollare

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Per un determinato utente, può essere attiva una sola shell
Gli utenti possono avviare solo un tipo di shell contemporaneamente, **Bash** o **PowerShell**. Tuttavia, si possono avere più istanze di Bash o PowerShell in esecuzione contemporaneamente. Lo scambio tra Bash o PowerShell ha come conseguenza il riavvio di Cloud Shell e le sessioni esistenti vengono terminate.

### <a name="usage-limits"></a>Limiti di consumo
Cloud Shell è pensato per l'uso interattivo e qualsiasi sessione non interattiva in esecuzione prolungata viene quindi interrotta senza preavviso.

## <a name="bash-limitations"></a>Limitazioni Bash

### <a name="user-permissions"></a>Autorizzazioni utente
Le autorizzazioni sono impostate come utenti normali senza accesso SUDO. Qualsiasi installazione esterna alla directory `$Home` non verrà conservata.
Anche se alcuni comandi all'interno della directory `clouddrive`, come `git clone`, non hanno le autorizzazioni appropriate, la directory `$Home` dispone delle autorizzazioni necessarie.

### <a name="editing-bashrc"></a>Modifica di .bashrc
Fare attenzione quando si modifica il file con estensione bashrc, poiché questa operazione può provocare errori imprevisti in Cloud Shell. 

### <a name="bashhistory"></a>.bash_history
È possibile che la cronologia dei comandi bash sia incoerente a causa dell'interruzione della sessione Cloud Shell o di sessioni simultanee.

## <a name="powershell-limitations"></a>Limitazioni PowerShell

### <a name="slow-startup-time"></a>Tempo di avvio lento
L'inizializzazione di PowerShell in Azure Cloud Shell può richiedere fino a 60 secondi durante l'anteprima.

### <a name="no-home-directory-persistence"></a>Nessuna persistenza directory $Home
I dati scritti su $Home da qualsiasi applicazione (ad esempio: git, vim e così via) non vengono mantenuti nelle sessioni di PowerShell.  Per una soluzione alternativa [vedere qui](troubleshooting.md#powershell-resolutions).

## <a name="next-steps"></a>Passaggi successivi
[Risoluzione dei problemi di Cloud Shell](troubleshooting.md) <br>
[Avvio rapido di Bash](quickstart.md) <br>
[Avvio rapido di PowerShell](quickstart-powershell.md)

