---
title: Configurare le impostazioni di Windows Update per l'uso con Gestione aggiornamenti di Azure
description: Questo articolo descrive le impostazioni di Windows Update configurate per l'uso con Gestione aggiornamenti di Azure.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79279026"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Configurare le impostazioni di Windows Update per Gestione aggiornamenti

Azure Gestione aggiornamenti si basa su [Windows Update client](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) per scaricare e installare gli aggiornamenti di Windows. Sono disponibili impostazioni specifiche utilizzate dal client di Windows Update durante la connessione a Windows Server Update Services (WSUS) o Windows Update. Molte di queste impostazioni possono essere gestite con:

- Editor Criteri di gruppo locali
- Criteri di gruppo
- PowerShell
- Modifica diretta del registro di sistema

Gestione aggiornamenti rispetta molte delle impostazioni specificate per controllare il client di Windows Update. Se si usano le impostazioni per abilitare gli aggiornamenti non Windows, Gestione aggiornamenti gestiranno anche tali aggiornamenti. Se si vuole abilitare il download degli aggiornamenti prima che si verifichi una distribuzione degli aggiornamenti, la distribuzione degli aggiornamenti può essere più veloce, più efficiente e meno probabile che superi la finestra di manutenzione.

## <a name="pre-download-updates"></a>Pre-download degli aggiornamenti

Per configurare il download automatico degli aggiornamenti ma non installarli automaticamente, è possibile usare Criteri di gruppo per impostare l' [impostazione configura aggiornamenti automatici](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) su **3**. Questa impostazione consente di scaricare gli aggiornamenti necessari in background e di notificare che gli aggiornamenti sono pronti per l'installazione. In questo modo, Gestione aggiornamenti mantiene il controllo delle pianificazioni, ma è possibile scaricare gli aggiornamenti al di fuori della finestra di manutenzione Gestione aggiornamenti. Questo comportamento impedisce che la **finestra di manutenzione superi** gli errori in Gestione aggiornamenti.

È possibile abilitare questa impostazione usando PowerShell eseguendo il comando seguente:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Configurare le impostazioni di riavvio

Le chiavi del registro di sistema elencate in [Configuring aggiornamenti automatici modificando il registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) di sistema e le [chiavi del registro di sistema usate per gestire il riavvio](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) possono causare il riavvio dei computer, anche se non si specifica **mai il riavvio** nelle impostazioni di **distribuzione degli aggiornamenti** . Configurare queste chiavi del registro di sistema per adattarle al proprio ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Abilitare gli aggiornamenti per altri prodotti Microsoft

Per impostazione predefinita, Windows Update client è configurato per fornire aggiornamenti solo per Windows. Se si Abilita l'impostazione **Invia aggiornamenti per altri prodotti Microsoft quando si aggiorna Windows** , si ricevono anche gli aggiornamenti per altri prodotti, incluse le patch di sicurezza per Microsoft SQL Server e altri prodotti software Microsoft. Questa opzione può essere configurata se sono stati scaricati e copiati i [file del modello amministrativo](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) più recenti disponibili per Windows 2016 e versioni successive.

Se si esegue Windows Server 2012 R2, questa impostazione non può essere configurata da Criteri di gruppo. Eseguire il comando di PowerShell seguente in tali computer. Gestione aggiornamenti è conforme a questa impostazione.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Impostazioni di configurazione WSUS

Gestione aggiornamenti supporta le impostazioni WSUS. Di seguito sono elencate le impostazioni di WSUS che è possibile configurare per l'utilizzo di Gestione aggiornamenti.

### <a name="intranet-microsoft-update-service-location"></a>Percorso del servizio di aggiornamento Microsoft nella rete Intranet

È possibile specificare le origini per l'analisi e il download degli aggiornamenti in [specificare il percorso del servizio Microsoft Update Intranet](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Per impostazione predefinita, Windows Update client è configurato per scaricare gli aggiornamenti da Windows Update. Quando si specifica un server WSUS come origine per i computer, se gli aggiornamenti non sono approvati in WSUS, la distribuzione degli aggiornamenti ha esito negativo. 

Per limitare i computer solo al servizio di aggiornamento interno, configurare non [connettersi ad alcuna Windows Update percorsi Internet](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Windows Update impostazioni, è possibile pianificare una distribuzione degli aggiornamenti seguendo le istruzioni riportate in [gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
