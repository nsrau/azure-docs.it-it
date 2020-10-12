---
title: Configurare le impostazioni di Windows Update per Gestione aggiornamenti di Automazione di Azure
description: Questo articolo descrive come configurare le impostazioni di Windows Update per l'uso con Gestione aggiornamenti di Automazione di Azure.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: aa4e49d0e79704b6fc9f19eb1b736b2358cf3a14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450742"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Configurare le impostazioni di Windows Update per Gestione aggiornamenti di Automazione di Azure

Gestione aggiornamenti di Automazione di Azure si basa sul [client di Windows Update](/windows/deployment/update/windows-update-overview) per scaricare e installare gli aggiornamenti di Windows. Sono disponibili impostazioni specifiche utilizzate dal client di Windows Update durante la connessione a Windows Server Update Services (WSUS) o Windows Update. Molte di queste impostazioni possono essere gestite con:

- Editor Criteri di gruppo locali
- Criteri di gruppo
- PowerShell
- Modifica diretta del Registro di sistema

Gestione aggiornamenti rispetta molte delle impostazioni specificate per controllare il client di Windows Update. Se si usano le impostazioni per abilitare gli aggiornamenti non Windows, Gestione aggiornamenti gestisce anche questi. Se si abilita il download degli aggiornamenti prima che venga eseguita una distribuzione degli aggiornamenti, le distribuzioni degli aggiornamenti saranno più veloci, più efficienti e avranno meno probabilità di superare la finestra di manutenzione.

Per raccomandazioni aggiuntive sulla configurazione di WSUS nella sottoscrizione di Azure e la conservazione sicura delle macchine virtuali di Windows, vedere [Pianificare la distribuzione per l'aggiornamento delle macchine virtuali di Windows in Azure usando WSUS](/azure/architecture/example-scenario/wsus/).

## <a name="pre-download-updates"></a>Download anticipato degli aggiornamenti

Per configurare il download automatico degli aggiornamenti senza installarli automaticamente, è possibile usare Criteri di gruppo per [configurare l'impostazione Aggiornamenti automatici](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) su 3. Questa impostazione consente di scaricare gli aggiornamenti necessari in background e di notificare che gli aggiornamenti sono pronti per l'installazione. In questo modo, Gestione aggiornamenti mantiene il controllo delle pianificazioni, ma consente di scaricare gli aggiornamenti al di fuori della rispettiva finestra di manutenzione. Questo comportamento impedisce errori di `Maintenance window exceeded` in Gestione aggiornamenti.

È possibile abilitare questa impostazione in PowerShell:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Configurare le impostazioni di riavvio

Le chiavi del registro di sistema elencate in [Configurazione degli Aggiornamenti automatici modificando il Registro di sistema](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) e [Chiavi del registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) possono causare il riavvio dei computer, anche se si specifica **N0on riavviare mai** nelle impostazioni di **Distribuzione degli aggiornamenti**. Configurare queste chiavi del registro di sistema per adattarle al proprio ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Abilitare gli aggiornamenti per altri prodotti Microsoft

Per impostazione predefinita, il client di Windows Update è configurato per fornire aggiornamenti solo per Windows. Selezionando l'impostazione **Scarica aggiornamenti per altri prodotti Microsoft durante l'aggiornamento di Windows** si ricevono anche gli aggiornamenti per altri prodotti, incluso le patch di protezione per Microsoft SQL Server o altri software Microsoft. È possibile configurare questa opzione se sono stati scaricati e copiati i [file dei modelli amministrativi](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) più recenti disponibili per Windows 2016 e versioni successive.

Se sono presenti computer che eseguono Windows Server 2012 R2, non è possibile configurare questa impostazione tramite Criteri di gruppo. Eseguire il comando di PowerShell seguente su questi computer:

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>Creare le impostazioni di configurazione WSUS

Gestione aggiornamenti supporta le impostazioni WSUS. È possibile specificare le origini per l'analisi e il download degli aggiornamenti utilizzando le istruzioni disponibili in [Specifica il percorso del servizio di aggiornamento Microsoft nella rete Intranet](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Per impostazione predefinita, il client di Windows Update è configurato per scaricare aggiornamenti da Windows Update. Quando si specifica un server WSUS come origine per i computer, se gli aggiornamenti non sono approvati in WSUS, la distribuzione degli aggiornamenti ha esito negativo. 

Per limitare i computer al servizio di aggiornamento interno, impostare [Non connettersi a nessun percorso Internet di Windows Update](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Passaggi successivi

Pianificare una distribuzione di aggiornamenti seguendo le istruzioni riportate in [gestire gli aggiornamenti e le patch per le macchine virtuali](update-mgmt-manage-updates-for-vm.md).
