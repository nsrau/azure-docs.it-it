---
title: Limitazioni di Azure Cloud Shell | Microsoft Docs
description: Panoramica delle limitazioni di Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951480"
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitazioni di Azure Cloud Shell

Di seguito vengono descritte le limitazioni note di Azure Cloud Shell:

## <a name="general-limitations"></a>Limitazioni generali

### <a name="system-state-and-persistence"></a>Persistenza e stato del sistema

Il computer che distribuisce la sessione Cloud Shell è temporaneo e viene riciclato dopo 20 minuti di inattività della sessione. Cloud Shell richiede che sia montata una condivisione file di Azure. La sottoscrizione, quindi, deve essere in grado di configurare le risorse di archiviazione per accedere a Cloud Shell. Altre considerazioni di cui tenere conto:

* Con l'archiviazione montata vengono rese persistenti soltanto le modifiche apportate all'interno della directory `$Home`.
* Le condivisioni file di Azure possono essere implementate solo dall'interno dell'[area assegnata](persisting-shell-storage.md#mount-a-new-clouddrive).
  * In Bash, eseguire `env` per trovare l'area geografica impostata come `ACC_LOCATION`.

### <a name="browser-support"></a>Supporto browser

Cloud Shell supporta le versioni più recenti di Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. Safari in modalità privata non è supportato.

### <a name="copy-and-paste"></a>Copia e Incolla

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Per un determinato utente, può essere attiva una sola shell

Gli utenti possono avviare solo un tipo di shell contemporaneamente, **Bash** o **PowerShell**. Tuttavia, si possono avere più istanze di Bash o PowerShell in esecuzione contemporaneamente. Lo scambio tra bash o PowerShell tramite il menu causa il riavvio Cloud Shell, che termina le sessioni esistenti. In alternativa, è possibile eseguire bash all'interno di PowerShell digitando `bash`ed è possibile eseguire PowerShell all'interno di bash digitando `pwsh`.

### <a name="usage-limits"></a>Limiti di utilizzo

Cloud Shell è pensato per l'uso interattivo e qualsiasi sessione non interattiva in esecuzione prolungata viene quindi interrotta senza preavviso.

## <a name="bash-limitations"></a>Limitazioni Bash

### <a name="user-permissions"></a>Autorizzazioni utente

Le autorizzazioni sono impostate come utenti normali senza accesso SUDO. Qualsiasi installazione esterna alla directory `$Home` non è permanente.

### <a name="editing-bashrc-or-profile"></a>Modifica di bashrc o $PROFILE

Prestare attenzione quando si modifica il file con estensione bashrc o $PROFILE di PowerShell. in questo modo possono verificarsi errori imprevisti nel Cloud Shell.

## <a name="powershell-limitations"></a>Limitazioni PowerShell

### <a name="azuread-module-name"></a>Nome modulo `AzureAD`

Il nome del modulo `AzureAD` è attualmente `AzureAD.Standard.Preview`. Il modulo fornisce la stessa funzionalità.

### <a name="sqlserver-module-functionality"></a>Funzionalità del modulo `SqlServer`

Il modulo `SqlServer` incluso in Cloud Shell include solo il supporto delle versioni precedenti di PowerShell Core. In particolare, `Invoke-SqlCmd` non è ancora disponibile.

### <a name="default-file-location-when-created-from-azure-drive"></a>Percorso file predefinito quando creato dall'unità Azure:

Usando i cmdlet di PowerShell, gli utenti non possono creare file nell'unità Azure:. Quando gli utenti creano nuovi file con altri strumenti, ad esempio vim o nano, i file vengono salvati nella cartella `$HOME` per impostazione predefinita. 

### <a name="gui-applications-are-not-supported"></a>Le applicazioni GUI non sono supportate

Se l'utente esegue un comando che crea una finestra di dialogo di Windows, viene visualizzato un messaggio di errore simile al seguente: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="large-gap-after-displaying-progress-bar"></a>Ampio spazio vuoto dopo aver visualizzato l'indicatore di stato

Se l'utente esegue un'azione che visualizza un indicatore di stato, ad esempio il completamento di una scheda mentre si trova nell'unità di `Azure:`, è possibile che il cursore non sia impostato correttamente e che venga visualizzato un gap in cui si trovava in precedenza l'indicatore di stato.

## <a name="next-steps"></a>Passaggi successivi

[Risoluzione dei problemi di Cloud Shell](troubleshooting.md) <br>
[Guida introduttiva di Bash](quickstart.md) <br>
[Avvio rapido di PowerShell](quickstart-powershell.md)
