---
title: Gestione delle estensioni VM con i server abilitati per Azure Arc
description: I server abilitati per Azure Arc possono gestire la distribuzione delle estensioni delle macchine virtuali che forniscono attività di configurazione e automazione post-distribuzione con macchine virtuali non di Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: e9865761fd3e5897ee3f01cd3d6ca620d5ea2f4b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460887"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gestione delle estensioni delle macchine virtuali con i server abilitati per Azure Arc

Le estensioni delle macchine virtuali sono piccole applicazioni che forniscono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Ad esempio, se una macchina virtuale richiede l'installazione di software, la protezione antivirus o l'esecuzione di uno script al suo interno, è possibile usare un'estensione macchina virtuale.

I server abilitati per Azure ARC consentono di distribuire le estensioni di VM di Azure in macchine virtuali Windows e Linux non di Azure, semplificando la gestione del computer ibrido in locale, perimetrale e altri ambienti cloud attraverso il ciclo di vita. Le estensioni di macchina virtuale possono essere gestite usando i metodi seguenti nei computer ibridi o nei server gestiti da server abilitati per Arc:

- Il[portale di Azure](manage-vm-extensions-portal.md)
- L'[interfaccia della riga di comando di Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Modelli di gestione risorse](manage-vm-extensions-template.md) di Azure

## <a name="key-benefits"></a>Vantaggi principali

Il supporto delle estensioni VM per i server abilitati per Azure Arc offre i vantaggi principali seguenti:

- Usare la [configurazione dello stato di automazione di Azure](../../automation/automation-dsc-overview.md) per archiviare centralmente le configurazioni e mantenere lo stato desiderato dei computer connessi ibridi abilitati tramite l'estensione VM DSC.

- Raccogliere i dati di log per l'analisi con i [log in monitoraggio di Azure](../../azure-monitor/platform/data-platform-logs.md) abilitati tramite l'estensione della macchina virtuale agente log Analytics. Questa operazione è utile per eseguire analisi complesse tra i dati da diverse origini.

- Con [monitoraggio di Azure per le macchine virtuali](../../azure-monitor/insights/vminsights-overview.md), analizza le prestazioni delle macchine virtuali Windows e Linux e monitora i processi e le dipendenze da altre risorse e processi esterni. Questa operazione viene eseguita tramite l'abilitazione dell'agente Log Analytics e delle estensioni della macchina virtuale dell'agente di dipendenza.

- Scaricare ed eseguire script su computer connessi ibridi usando l'estensione script personalizzata. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o altre attività di configurazione o gestione.

## <a name="availability"></a>Disponibilità

La funzionalità di estensione della macchina virtuale è disponibile solo nell'elenco delle [aree supportate](overview.md#supported-regions). Assicurarsi di caricare il computer in una di queste aree.

## <a name="extensions"></a>Estensioni

In questa versione sono supportate le estensioni di macchina virtuale seguenti nei computer Windows e Linux.

|Estensione |Sistema operativo |Editore |Informazioni aggiuntive |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Estensione script personalizzato Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Estensione DSC di Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agente di Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics estensione VM per Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Estensione macchina virtuale di Dependency Agent per Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Estensione script personalizzato Linux versione 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Estensione DSC PowerShell per Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agente di Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics estensione VM per Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Estensione macchina virtuale dell'agente di dipendenza per Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Per informazioni sul pacchetto dell'agente di computer connesso di Azure e per informazioni dettagliate sul componente agente di estensione, vedere [Panoramica degli agenti](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Prerequisiti

Questa funzionalità dipende dai provider di risorse di Azure seguenti nella sottoscrizione:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se non sono già registrati, seguire la procedura descritta in [registrare i provider di risorse di Azure](agent-overview.md#register-azure-resource-providers).

Per l'estensione della macchina virtuale dell'agente Log Analytics per Linux è necessario che Python 2. x sia installato nel computer di destinazione.

### <a name="connected-machine-agent"></a>Agente Connected Machine

Verificare che il computer corrisponda alle [versioni supportate](agent-overview.md#supported-operating-systems) del sistema operativo Windows e Linux per l'agente del computer connesso di Azure.

La versione minima dell'agente del computer connesso supportato con questa funzionalità in Windows e Linux è la versione 1,0.

Per aggiornare il computer alla versione dell'agente richiesta, vedere [upgrade Agent](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Passaggi successivi

È possibile distribuire, gestire e rimuovere estensioni di VM usando l' [interfaccia](manage-vm-extensions-cli.md)della riga di comando di Azure, [PowerShell](manage-vm-extensions-powershell.md), dal [portale di Azure](manage-vm-extensions-portal.md)o da [modelli di Azure Resource Manager](manage-vm-extensions-template.md).
