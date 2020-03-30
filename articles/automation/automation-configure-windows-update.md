---
title: Configurare le impostazioni di Windows Update per l'utilizzo con Gestione aggiornamenti di AzureConfigure Windows Update settings to work with Azure Update Management
description: Questo articolo descrive le impostazioni di Windows Update configurate per l'utilizzo con Gestione aggiornamenti di Azure.This article describes the Windows Update settings that you configure to work with Azure Update Management.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279026"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Configurare le impostazioni di Windows Update per la gestione degli aggiornamenti

Gestione aggiornamenti di Azure si basa sul [client Windows Update](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) per scaricare e installare gli aggiornamenti di Windows. Esistono impostazioni specifiche utilizzate dal client Windows Update per la connessione a Windows Server Update Services (WSUS) o Windows Update. Molte di queste impostazioni possono essere gestite con:

- Editor Criteri di gruppo locali
- Criteri di gruppo
- PowerShell
- Modifica diretta del Registro di sistema

Update Management rispetta molte delle impostazioni specificate per controllare il client Windows Update. Se si utilizzano le impostazioni per abilitare gli aggiornamenti non Windows, Gestione aggiornamenti gestirà anche tali aggiornamenti. Se si desidera abilitare il download degli aggiornamenti prima che si verifichi una distribuzione degli aggiornamenti, la distribuzione degli aggiornamenti può essere più veloce, più efficiente e meno probabile che superi la finestra di manutenzione.

## <a name="pre-download-updates"></a>Aggiornamenti pre-download

Per configurare il download automatico degli aggiornamenti ma non installarli automaticamente, è possibile utilizzare Criteri di gruppo per [impostare l'impostazione Configura Aggiornamenti automatici](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) su **3**. Questa impostazione abilita il download degli aggiornamenti necessari in background e notifica che gli aggiornamenti sono pronti per l'installazione. In questo modo, Gestione aggiornamenti rimane in controllo delle pianificazioni, ma gli aggiornamenti possono essere scaricati all'esterno della finestra di manutenzione di Gestione aggiornamenti. Questo comportamento impedisce **finestra di manutenzione ha superato** gli errori in Gestione aggiornamenti.

È possibile abilitare questa impostazione usando PowerShell eseguendo il comando seguente:You can enable this setting setting using PowerShell, by running the following command:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Configurare le impostazioni di riavvio

Le chiavi del Registro di sistema elencate in Configurazione di [Aggiornamenti automatici modificando il Registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) di sistema e [le chiavi del Registro](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) di sistema utilizzate per gestire il riavvio possono causare il riavvio dei computer, anche se si specifica Non **riavviare mai** nelle impostazioni di distribuzione di **aggiornamento.** Configurare queste chiavi del Registro di sistema per soddisfare l'ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Abilitare gli aggiornamenti per altri prodotti Microsoft

Per impostazione predefinita, il client Windows Update è configurato per fornire aggiornamenti solo per Windows. Se si attiva l'impostazione **Invia aggiornamenti per altri prodotti Microsoft durante l'aggiornamento** di Windows, si ricevono anche gli aggiornamenti per altri prodotti, incluse le patch di protezione per Microsoft SQL Server e altro software Microsoft. Questa opzione può essere configurata se sono stati scaricati e copiati i file dei [modelli amministrativi](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) più recenti disponibili per Windows 2016 e versioni successive.

Se si esegue Windows Server 2012 R2, questa impostazione non può essere configurata da Criteri di gruppo. Eseguire il comando di PowerShell seguente in tali computer. La gestione degli aggiornamenti è conforme a questa impostazione.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Impostazioni di configurazione di WSUS

Update Management supporta le impostazioni WSUS. Le impostazioni WSUS che è possibile configurare per l'utilizzo di Gestione aggiornamenti sono elencate di seguito.

### <a name="intranet-microsoft-update-service-location"></a>Percorso del servizio di aggiornamento Microsoft Intranet

È possibile specificare le origini per l'analisi e il download degli aggiornamenti in [Specificare](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)il percorso del servizio Microsoft Update intranet . Per impostazione predefinita, il client Windows Update è configurato per scaricare gli aggiornamenti da Windows Update. Quando si specifica un server WSUS come origine per i computer, se gli aggiornamenti non vengono approvati in WSUS, la distribuzione degli aggiornamenti non riesce. 

Per limitare i computer solo al servizio di aggiornamento interno, configurare [Non connettersi ad alcun percorsi Internet di Windows Update.](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations) 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le impostazioni di Windows Update, è possibile pianificare una distribuzione degli aggiornamenti seguendo le istruzioni in Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure.After you configure Windows Update settings, you can schedule an update deployment by following the instructions in [Manage updates and patches for your Azure VMs](automation-tutorial-update-management.md).
