---
title: Limitazioni di Azure Cloud Shell | Microsoft Docs
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
ms.date: 01/17/2018
ms.author: juluk
ms.openlocfilehash: 7e498582d78d2807070c943dfd838dd9efeb4ed2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitazioni di Azure Cloud Shell

Di seguito vengono descritte le limitazioni note di Azure Cloud Shell:

## <a name="general-limitations"></a>Limitazioni generali

### <a name="system-state-and-persistence"></a>Persistenza e stato del sistema

Il computer che distribuisce la sessione Cloud Shell è temporaneo e viene riciclato dopo 20 minuti di inattività della sessione. Cloud Shell richiede che sia montata una condivisione file di Azure. La sottoscrizione, quindi, deve essere in grado di configurare le risorse di archiviazione per accedere a Cloud Shell. Altre considerazioni di cui tenere conto:

* Con l'archiviazione montata vengono rese persistenti soltanto le modifiche apportate all'interno della directory `clouddrive`. In Bash anche la directory `$Home` è permanente.
* Le condivisioni file di Azure possono essere implementate solo dall'interno dell'[area assegnata](persisting-shell-storage.md#mount-a-new-clouddrive).
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

Le autorizzazioni sono impostate come utenti normali senza accesso SUDO. Qualsiasi installazione esterna alla directory `$Home` non è permanente.

### <a name="clouddrive-smb-limited-permissions"></a>Autorizzazioni limitate SMB per clouddrive
Alcuni comandi all'interno della directory `clouddrive`, ad esempio `git clone`, non hanno le autorizzazioni appropriate per eseguire operazioni di lettura/scrittura in determinati file. Se si riscontra questo problema, provare nuovamente a eseguire l'operazione dalla directory `$Home` che non ha limitazioni SMB.

### <a name="editing-bashrc"></a>Modifica di .bashrc

Fare attenzione quando si modifica il file con estensione bashrc, poiché questa operazione può provocare errori imprevisti in Cloud Shell.

### <a name="bashhistory"></a>.bash_history

È possibile che la cronologia dei comandi bash sia incoerente a causa dell'interruzione della sessione Cloud Shell o di sessioni simultanee.

## <a name="powershell-limitations"></a>Limitazioni PowerShell

### <a name="slow-startup-time"></a>Tempo di avvio lento

L'inizializzazione di PowerShell in Azure Cloud Shell (anteprima) può richiedere fino a 60 secondi durante l'anteprima.

### <a name="no-home-directory-persistence"></a>Nessuna persistenza directory $Home

I dati scritti in `$Home` da qualsiasi applicazione (ad esempio git, vim e così via) non vengono mantenuti nelle sessioni di PowerShell. Per una soluzione alternativa, [vedere qui](troubleshooting.md#powershell-resolutions).

### <a name="default-file-location-when-created-from-azure-drive"></a>Percorso file predefinito quando creato dall'unità Azure:

Usando dei cmdlet di PowerShell, gli utenti non possono creare i file sotto l'unità Azure. Quando gli utenti creano nuovi file con altri strumenti, ad esempio vim o nano, i file vengono salvati nella cartella C:\Utenti per impostazione predefinita. 

### <a name="gui-applications-are-not-supported"></a>Le applicazioni GUI non sono supportate

Se l'utente esegue un comando che determina la generazione di una finestra di dialogo di Windows, come `Connect-AzureAD` o `Login-AzureRMAccount`, viene visualizzato un messaggio di errore, ad esempio: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="next-steps"></a>Passaggi successivi

[Risoluzione dei problemi di Cloud Shell](troubleshooting.md) <br>
[Guida introduttiva di Bash](quickstart.md) <br>
[Avvio rapido di PowerShell](quickstart-powershell.md)
