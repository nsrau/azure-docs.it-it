---
title: Configurare le impostazioni di Windows Update per l'uso con Gestione aggiornamenti di Azure
description: Questo articolo descrive le impostazioni di Windows Update configurate per l'uso con Gestione aggiornamenti
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377564"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Configurare le impostazioni di Windows Update per Gestione aggiornamenti

Gestione aggiornamenti si basa su Windows Update per scaricare e installare gli aggiornamenti di Windows. Di conseguenza, vengono applicate molte delle impostazioni usate da Windows Update. Se si usano le impostazioni per abilitare gli aggiornamenti non Windows, Gestione aggiornamenti gestisce anche questi. Se si abilita il download degli aggiornamenti prima che venga eseguita una distribuzione degli aggiornamenti, le distribuzioni degli aggiornamenti saranno più veloci e avranno meno probabilità di superare la finestra di manutenzione.

## <a name="pre-download-updates"></a>Eseguire il pre-download degli aggiornamenti

Per configurare automaticamente il download degli aggiornamenti in Criteri di gruppo, impostare [Configura Aggiornamenti automatici](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) su **3**. In questo modo gli aggiornamenti necessari vengono scaricati in background senza essere installati. Gestione aggiornamenti mantiene il controllo delle pianificazioni, ma consente di scaricare gli aggiornamenti al di fuori della finestra di manutenzione. Ciò contribuisce a evitare errori di **superamento della finestra di manutenzione** di Gestione aggiornamenti.

Questa configurazione è possibile anche con PowerShell. A tal fine, eseguire il comando PowerShell seguente nel sistema in cui si intende scaricare automaticamente gli aggiornamenti.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Disabilita installazione automatica

Per le macchine virtuali di Azure l'installazione automatica degli aggiornamenti è abilitata per impostazione predefinita. Questa operazione può causare l'installazione degli aggiornamenti prima di pianificarne l'installazione da Gestione aggiornamenti. È possibile disabilitare questo comportamento impostando la chiave del registro di sistema `NoAutoUpdate` su `1`. Il seguente frammento di codice di PowerShell Mostra un modo per eseguire questa operazione.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Configurare le impostazioni di riavvio

Le chiavi del registro di sistema elencate in [Configuring aggiornamenti automatici modificando il registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) di sistema e le [chiavi del registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) possono causare il riavvio dei computer, anche se non è stato specificato **mai il riavvio** nelle impostazioni di distribuzione degli aggiornamenti . È necessario configurare queste chiavi del registro di sistema come desiderato per l'ambiente in uso.

## <a name="enable-updates-for-other-microsoft-products"></a>Abilitare gli aggiornamenti per altri prodotti Microsoft

Per impostazione predefinita, Windows Update fornisce soltanto gli aggiornamenti per Windows. Se si abilitano **gli aggiornamenti per altri prodotti Microsoft durante l'aggiornamento di Windows**, vengono forniti aggiornamenti per altri prodotti, incluse le patch di sicurezza per SQL Server o altro software di terze parti. Non è possibile configurare questa opzione tramite Criteri di gruppo. Eseguire il comando PowerShell seguente nei sistemi sui quali si intende abilitare gli aggiornamenti proprietari per applicare l'impostazione a Gestione aggiornamenti.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Impostazioni di configurazione WSUS

**Gestione aggiornamenti** rispetta le impostazioni di configurazione di WSUS. Di seguito è riportato l'elenco delle impostazioni di WSUS che è possibile configurare per l'utilizzo di Gestione aggiornamenti.

### <a name="intranet-microsoft-update-service-location"></a>Percorso del servizio di aggiornamento Microsoft nella rete Intranet

È possibile specificare le origini per l'analisi e il download degli aggiornamenti in [Intranet Microsoft Update servizio percorso](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Fasi successive

Dopo aver configurato le impostazioni di Windows Update, è possibile pianificare una distribuzione degli aggiornamenti seguendo le istruzioni in [gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md)