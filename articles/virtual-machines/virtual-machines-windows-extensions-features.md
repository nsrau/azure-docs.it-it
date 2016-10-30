<properties
 pageTitle="Estensioni e funzionalità delle macchine virtuali | Microsoft Azure"
 description="Informazioni sulle estensioni disponibili per le macchine virtuali di Azure, raggruppate in base a ciò che forniscono o migliorano."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/30/2016"
 ms.author="nepeters"/>


# <a name="about-virtual-machine-extensions-and-features"></a>Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali

## <a name="azure-vm-extensions"></a>Estensioni delle macchine virtuali di Azure

Le estensioni delle macchine virtuali di Azure sono piccole applicazioni che eseguono attività di configurazione e automazione post-distribuzione sulle macchine virtuali di Azure. Ad esempio, se una macchina virtuale richiede l'installazione di software, la protezione antivirus o la configurazione di Docker, è possibile usare un'estensione di macchina virtuale per completare queste attività. Le estensioni delle macchine virtuali di Azure possono essere eseguite tramite l'interfaccia della riga di comando di Azure, PowerShell, i modelli di Resource Manager e il portale di Azure. Le estensioni possono essere unite in bundle con una nuova distribuzione di macchina virtuale o eseguite su un sistema esistente.

Questo documento illustra i prerequisiti per le estensioni delle macchine virtuali di Azure e include indicazioni su come rilevare le estensioni disponibili. 

## <a name="azure-vm-agent"></a>Agente di macchine virtuali di Azure

L'agente di macchine virtuali di Azure gestisce l'interazione tra una macchina virtuale di Azure e il controller di infrastruttura di Azure. L'agente di macchine virtuali è responsabile di molti aspetti funzionali della distribuzione e della gestione delle macchine virtuali di Azure, tra cui l'esecuzione delle estensioni delle macchine virtuali. L'agente di macchine virtuali di Azure è preinstallato nelle immagini della raccolta di Azure e può essere installato nei sistemi operativi supportati. 

Per informazioni sui sistemi operativi supportati e per le istruzioni di installazione, vedere l'[agente delle macchine virtuali di Azure](./virtual-machines-windows-classic-agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Individuare le estensioni delle macchine virtuali

Molte estensioni di macchine virtuali di diverso tipo sono disponibili per l'uso con Macchine virtuali di Azure. Per visualizzare un elenco completo, eseguire il comando seguente con l'interfaccia della riga di comando di Azure, sostituendo il percorso con quello preferito.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>Estensioni comuni delle macchine virtuali

|Nome estensione   |Descrizione   |Altre informazioni   |
|---|---|---|
|Estensione Script personalizzato per Windows  | Esegue script su una macchina virtuale di Azure.  |[Estensione script personalizzata per Windows](./virtual-machines-windows-extensions-customscript.md)   |
|Estensione DSC per Windows | Estensione PowerShell DSC (Desired State Configuration).  | [Estensione della VM Docker](./virtual-machines-windows-extensions-dsc-overview.md)  |
|Estensione di Diagnostica di Azure | Gestisce Diagnostica di Azure. |[Estensione di Diagnostica di Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |



<!--HONumber=Oct16_HO2-->


