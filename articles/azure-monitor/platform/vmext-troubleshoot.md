---
title: Risolvere i problemi di estensione di macchina virtuale di Analitica di Log di Azure in Monitoraggio di Azure | Microsoft Docs
description: Vengono descritti i sintomi, le cause e la risoluzione dei problemi più comuni con l'estensione della VM di Log Analytics per le macchine virtuali di Azure per Windows e Linux.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2019
ms.author: magoedte
ms.openlocfilehash: dd5e0749116ef335887ea634b9d2790c63bf171d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751937"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Risoluzione dei problemi l'estensione macchina virtuale di Analitica di Log in Monitoraggio di Azure
Questo articolo fornisce indicazioni sulla risoluzione di errori che possono verificarsi con l'estensione della VM di Log Analytics per le macchine virtuali di Windows e Linux in esecuzione su Microsoft Azure e suggerisce le possibili soluzioni per risolverli.

Per verificare lo stato dell'estensione eseguire questi passaggi dal portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **macchine virtuali**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Macchine virtuali**.
3. Nell'elenco delle macchine virtuali trovarla e selezionarla.
3. Nel pannello della macchina virtuale fare clic su **Extensions** (Estensioni).
4. Nell'elenco verificare se l'estensione di Log Analytics è abilitata o meno.  Per Linux l'agente viene elencato come **OMSAgentforLinux** mentre per Windows viene elencato come **MicrosoftMonitoringAgent**.

   ![Visualizzazione dell'estensione della VM](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Fare clic sull'estensione per visualizzare i dettagli. 

   ![Dettagli dell'estensione VM](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Risoluzione dei problemi relativi all'estensione della VM Windows di Azure

Se l'estensione della VM di *Microsoft Monitoring Agent* non viene installata o non segnala dati è possibile seguire queste procedure per risolvere il problema.

1. Controllare che l'agente di macchine virtuali di Azure sia installato e funzioni correttamente seguendo la procedura descritta nell'articolo [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * È anche possibile esaminare il file di log dell'agente di macchine virtuali `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se il log non esiste, l'agente di macchine virtuali non è installato
   * [Installare l'agente di macchine virtuali di Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Esaminare i file di log dell'estensione macchina virtuale Microsoft Monitoring Agent in `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Verificare che la macchina virtuale possa eseguire script di PowerShell
4. Verificare che le autorizzazioni per C:\Windows\temp non siano state modificate
5. Visualizzare lo stato di Microsoft Monitoring Agent digitando quanto riportato di seguito in una finestra di PowerShell con privilegi elevati nella macchina virtuale: `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Esaminare i file di log dell'installazione di Microsoft Monitoring Agent in `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Per altre informazioni, vedere l'articolo relativo alla [risoluzione dei problemi delle estensioni Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Risoluzione dei problemi relativi all'estensione della VM di Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Se l'estensione della macchina virtuale per l'*agente di Log Analytics per Linux* non viene installata o non segnala dati, è possibile seguire questi passaggi per risolvere il problema.

1. Se lo stato dell'estensione è *Sconosciuto*, controllare che l'agente di macchine virtuali di Azure sia installato e funzioni correttamente esaminando il relativo file di log `/var/log/waagent.log`
   * Se il log non esiste, l'agente di macchine virtuali non è installato
   * [Installare l'agente di macchine virtuali di Azure nelle VM Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Per altri stati non integri, esaminare i file di log dell'estensione della macchina virtuale per l'agente di Log Analytics per Linux in `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se lo stato dell'estensione è integro ma i dati non vengono caricati, esaminare i file di log dell'agente di Log Analytics per Linux in `/var/opt/microsoft/omsagent/log/omsagent.log`

Per altre informazioni, vedere l'articolo relativo alla [risoluzione dei problemi delle estensioni Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive sulla risoluzione dei problemi relativi all'agente di Log Analytics per Linux ospitato in computer esterni ad Azure, vedere [Risolvere i problemi relativi all'agente di Azure Log Analytics per Linux](agent-linux-troubleshoot.md).  
