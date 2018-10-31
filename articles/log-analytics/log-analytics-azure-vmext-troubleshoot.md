---
title: Risoluzione dei problemi relativi all'estensione della VM di Log Analytics di Azure | Microsoft Docs
description: Vengono descritti i sintomi, le cause e la risoluzione dei problemi più comuni con l'estensione della VM di Log Analytics per le macchine virtuali di Azure per Windows e Linux.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 845bc46ec56bfd6681c4fb318a57de19f66c0edf
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403872"
---
# <a name="troubleshooting-the-log-analytics-vm-extension"></a>Risoluzione dei problemi relativi all'estensione della VM di Log Analytics
Questo articolo fornisce indicazioni sulla risoluzione di errori che possono verificarsi con l'estensione della VM di Log Analytics per le macchine virtuali di Windows e Linux in esecuzione su Microsoft Azure e suggerisce le possibili soluzioni per risolverli.

Per verificare lo stato dell'estensione eseguire questi passaggi dal portale di Azure.

1. Accedere al [portale di Azure](http://portal.azure.com).
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **macchine virtuali**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Macchine virtuali**.
3. Nell'elenco delle macchine virtuali trovarla e selezionarla.
3. Nel pannello della macchina virtuale fare clic su **Extensions** (Estensioni).
4. Nell'elenco verificare se l'estensione di Log Analytics è abilitata o meno.  Per Linux l'agente viene elencato come **OMSAgentforLinux** mentre per Windows viene elencato come **MicrosoftMonitoringAgent**.

   ![Visualizzazione dell'estensione della VM](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Fare clic sull'estensione per visualizzare i dettagli. 

   ![Dettagli dell'estensione VM](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Risoluzione dei problemi relativi all'estensione della VM Windows di Azure

Se l'estensione della VM di *Microsoft Monitoring Agent* non viene installata o non segnala dati è possibile seguire queste procedure per risolvere il problema.

1. Controllare che l'agente di macchine virtuali di Azure sia installato e funzioni correttamente seguendo la procedura descritta nell'articolo [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * È anche possibile esaminare il file di log dell'agente di macchine virtuali `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se il log non esiste, l'agente di macchine virtuali non è installato
   * [Installare l'agente di macchine virtuali di Azure](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Verificare che l'attività dell'heartbeat dell'estensione Microsoft Monitoring Agent sia in esecuzione seguendo questa procedura:
   * Accedere alla macchina virtuale
   * Aprire Utilità di pianificazione e trovare l'attività `update_azureoperationalinsight_agent_heartbeat`
   * Verificare che l'attività sia abilitata e venga eseguita ogni minuto
   * Controllare il file di log dell'heartbeat in `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Esaminare i file di log dell'estensione macchina virtuale Microsoft Monitoring Agent in `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Verificare che la macchina virtuale possa eseguire script di PowerShell
5. Verificare che le autorizzazioni per C:\Windows\temp non siano state modificate
6. Visualizzare lo stato di Microsoft Monitoring Agent digitando quanto riportato di seguito in una finestra di PowerShell con privilegi elevati nella macchina virtuale: `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Esaminare i file di log dell'installazione di Microsoft Monitoring Agent in `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Per altre informazioni, vedere l'articolo relativo alla [risoluzione dei problemi delle estensioni Windows](../virtual-machines/windows/extensions-oms.md).

## <a name="troubleshooting-linux-vm-extension"></a>Risoluzione dei problemi relativi all'estensione della VM di Linux
[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 
Se l'estensione della macchina virtuale per l'*agente di Log Analytics per Linux* non viene installata o non segnala dati, è possibile seguire questi passaggi per risolvere il problema.

1. Se lo stato dell'estensione è *Sconosciuto*, controllare che l'agente di macchine virtuali di Azure sia installato e funzioni correttamente esaminando il relativo file di log `/var/log/waagent.log`
   * Se il log non esiste, l'agente di macchine virtuali non è installato
   * [Installare l'agente di macchine virtuali di Azure nelle VM Linux](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Per altri stati non integri, esaminare i file di log dell'estensione della macchina virtuale per l'agente di Log Analytics per Linux in `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se lo stato dell'estensione è integro ma i dati non vengono caricati, esaminare i file di log dell'agente di Log Analytics per Linux in `/var/opt/microsoft/omsagent/log/omsagent.log`

Per altre informazioni, vedere l'articolo relativo alla [risoluzione dei problemi delle estensioni Linux](../virtual-machines/linux/extensions-oms.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive sulla risoluzione dei problemi relativi all'agente di Log Analytics per Linux ospitato in computer esterni ad Azure, vedere [Risolvere i problemi relativi all'agente di Azure Log Analytics per Linux](log-analytics-agent-linux-support.md).  
