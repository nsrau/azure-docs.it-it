---
title: Configurare le impostazioni di Windows Update per l'uso con Gestione aggiornamenti di Azure
description: Questo articolo descrive le impostazioni di Windows Update configurate per l'uso con Gestione aggiornamenti di Azure.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 813d34f9c07e6c2909c483f040d4f3bf09b3ad24
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690851"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Configurare le impostazioni di Windows Update per Gestione aggiornamenti

Azure Gestione aggiornamenti si basa su Windows Update per scaricare e installare gli aggiornamenti di Windows. Di conseguenza, Gestione aggiornamenti rispetta molte delle impostazioni usate da Windows Update. Se si usano le impostazioni per abilitare gli aggiornamenti non Windows, Gestione aggiornamenti gestiranno anche tali aggiornamenti. Se si vuole abilitare il download degli aggiornamenti prima che si verifichi una distribuzione degli aggiornamenti, la distribuzione degli aggiornamenti può essere più veloce, più efficiente e meno probabile che superi la finestra di manutenzione.

## <a name="pre-download-updates"></a>Pre-download degli aggiornamenti

Per configurare il download automatico degli aggiornamenti in Criteri di gruppo, impostare l' [impostazione configura aggiornamenti automatici](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) su **3**. Questa impostazione consente di scaricare gli aggiornamenti necessari in background, ma non di installarli. In questo modo, Gestione aggiornamenti mantiene il controllo delle pianificazioni, ma è possibile scaricare gli aggiornamenti al di fuori della finestra di manutenzione Gestione aggiornamenti. Questo comportamento impedisce gli errori di "finestra di manutenzione superata" nel Gestione aggiornamenti.

È anche possibile attivare questa impostazione eseguendo il comando di PowerShell seguente in un sistema che si vuole configurare per il download automatico degli aggiornamenti:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Disabilita installazione automatica

Per impostazione predefinita, nelle macchine virtuali (VM) di Azure è abilitata l'installazione automatica degli aggiornamenti. Questo potrebbe causare l'installazione degli aggiornamenti prima di pianificarli per l'installazione di Gestione aggiornamenti. È possibile disabilitare questo comportamento impostando la chiave del registro di sistema `NoAutoUpdate` su `1`. Il frammento di codice di PowerShell seguente mostra come eseguire questa operazione:

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Configurare le impostazioni di riavvio

Le chiavi del registro di sistema elencate in [configurazione di aggiornamenti automatici modificando il registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) di sistema e le [chiavi del registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) possono causare il riavvio dei computer, anche se si specifica **mai il riavvio** nelle impostazioni di **distribuzione degli aggiornamenti** . È necessario configurare queste chiavi del registro di sistema in modo da adattarle al proprio ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Abilitare gli aggiornamenti per altri prodotti Microsoft

Per impostazione predefinita, Windows Update fornisce aggiornamenti solo per Windows. Se si Abilita l'impostazione **Invia aggiornamenti per altri prodotti Microsoft quando si aggiorna Windows** , si ricevono anche gli aggiornamenti per altri prodotti, incluse le patch di sicurezza per Microsoft SQL Server e altri prodotti software Microsoft. Non è possibile configurare questa opzione tramite Criteri di gruppo. Eseguire il comando di PowerShell seguente nei sistemi in cui si desidera abilitare altri aggiornamenti Microsoft. Gestione aggiornamenti conforme a questa impostazione.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Impostazioni di configurazione WSUS

Gestione aggiornamenti è conforme alle impostazioni di Windows Server Update Services (WSUS). Di seguito sono elencate le impostazioni di WSUS che è possibile configurare per l'utilizzo di Gestione aggiornamenti.

### <a name="intranet-microsoft-update-service-location"></a>Percorso del servizio di aggiornamento Microsoft nella rete Intranet

È possibile specificare le origini per l'analisi e il download degli aggiornamenti in [specificare il percorso del servizio Microsoft Update Intranet](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Windows Update impostazioni, è possibile pianificare una distribuzione degli aggiornamenti seguendo le istruzioni riportate in [gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).