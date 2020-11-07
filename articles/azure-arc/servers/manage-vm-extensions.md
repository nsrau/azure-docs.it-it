---
title: Gestione delle estensioni VM con i server abilitati per Azure Arc
description: I server abilitati per Azure Arc possono gestire la distribuzione delle estensioni delle macchine virtuali che forniscono attività di configurazione e automazione post-distribuzione con macchine virtuali non di Azure.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 7682f6c8631bbaf2310d501d7cee6aecb2311226
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358032"
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

- Aggiorna automaticamente i certificati archiviati in un [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Disponibilità

La funzionalità di estensione della macchina virtuale è disponibile solo nell'elenco delle [aree supportate](overview.md#supported-regions). Assicurarsi di caricare il computer in una di queste aree.

## <a name="extensions"></a>Estensioni

In questa versione sono supportate le estensioni di macchina virtuale seguenti nei computer Windows e Linux.

|Estensione |Sistema operativo |Publisher |Informazioni aggiuntive |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Estensione script personalizzato Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Estensione DSC di Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agente di Log Analytics |Windows |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics estensione VM per Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Estensione macchina virtuale di Dependency Agent per Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Key Vault | Windows | Microsoft.Compute | [Estensione di macchina virtuale Key Vault per Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|CustomScript|Linux |Microsoft. Azure. Extension |[Estensione script personalizzato Linux versione 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Estensione DSC PowerShell per Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agente di Log Analytics |Linux |Microsoft.EnterpriseCloud.Monitoring |[Log Analytics estensione VM per Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Estensione macchina virtuale dell'agente di dipendenza per Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Key Vault | Linux | Microsoft.Compute | [Estensione macchina virtuale di Key Vault per Linux](../../virtual-machines/extensions/key-vault-linux.md) |

Per informazioni sul pacchetto dell'agente di computer connesso di Azure e per informazioni dettagliate sul componente agente di estensione, vedere [Panoramica degli agenti](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Prerequisiti

Questa funzionalità dipende dai provider di risorse di Azure seguenti nella sottoscrizione:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se non sono già registrati, seguire la procedura descritta in [registrare i provider di risorse di Azure](agent-overview.md#register-azure-resource-providers).

### <a name="log-analytics-vm-extension"></a>Estensione della macchina virtuale Log Analytics

Per l'estensione della macchina virtuale dell'agente Log Analytics per Linux è necessario che Python 2. x sia installato nel computer di destinazione.

### <a name="azure-key-vault-vm-extension-preview"></a>Estensione della macchina virtuale Azure Key Vault (anteprima)

L'estensione della macchina virtuale Key Vault (anteprima) non supporta i sistemi operativi Linux seguenti:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

La distribuzione dell'estensione della macchina virtuale Key Vault (anteprima) è supportata solo con:

- Interfaccia della riga di comando di Azure
- Azure PowerShell
- Modello di Azure Resource Manager

Prima di distribuire l'estensione, è necessario completare le operazioni seguenti:

1. [Creare un insieme di credenziali e un certificato](../../key-vault/certificates/quick-create-portal.md) (autofirmato o importazione).

2. Concedere al server Azure Arc abilitato l'accesso al segreto del certificato. Se si usa l' [Anteprima RBAC](../../key-vault/general/rbac-guide.md), cercare il nome della risorsa Azure Arc e assegnargli il ruolo **utente Key Vault Secrets (anteprima)** . Se si usano i [criteri di accesso key Vault](../../key-vault/general/assign-access-policy-portal.md), assegnare le autorizzazioni **Get** Secret all'identità assegnata al sistema della risorsa di Azure Arc.

### <a name="connected-machine-agent"></a>Agente Connected Machine

Verificare che il computer corrisponda alle [versioni supportate](agent-overview.md#supported-operating-systems) del sistema operativo Windows e Linux per l'agente del computer connesso di Azure.

La versione minima dell'agente del computer connesso supportato con questa funzionalità in Windows e Linux è la versione 1,0.

Per aggiornare il computer alla versione dell'agente richiesta, vedere [upgrade Agent](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Passaggi successivi

È possibile distribuire, gestire e rimuovere le estensioni di macchina virtuale usando l'interfaccia della riga di comando di [Azure](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), dal [portale di Azure](manage-vm-extensions-portal.md)o dai [modelli di Azure Resource Manager](manage-vm-extensions-template.md).
