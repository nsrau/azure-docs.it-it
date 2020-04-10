---
title: Panoramica della configurazione dello stato
description: Panoramica della configurazione dello stato di automazione di Azure, dei termini e dei problemi noti
keywords: powershell dsc, configurazione dello stato desiderato, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 02e540c9ac2654be97ff247749d1ed18573cfc24
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010257"
---
# <a name="state-configuration-overview"></a>Panoramica della configurazione dello stato

Configurazione stato di automazione di Azure è un servizio di Azure che consente di scrivere, gestire e compilare [le configurazioni](/powershell/scripting/dsc/configurations/configurations)di PowerShell Desired State Configuration (DSC). Il servizio importa anche [le risorse DSC](/powershell/scripting/dsc/resources/resources)e assegna le configurazioni ai nodi di destinazione, tutti nel cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Perché usare Configurazione stato di Automazione di Azure

Configurazione stato di Automazione di Azure offre diversi vantaggi rispetto all'uso di DSC all'esterno di Azure.

### <a name="built-in-pull-server"></a>Server di pull predefinito

La configurazione dello stato di automazione di Azure offre un server di pull DSC simile al servizio [DSC-Service delle funzionalità](/powershell/scripting/dsc/pull-server/pullserver)di Windows. I nodi di destinazione possono ricevere automaticamente le configurazioni, conformarsi allo stato desiderato e creare report sulla conformità. Il server di pull predefinito in Automazione di Azure elimina la necessità di configurare e gestire un proprio server di pull. Automazione di Azure può avere come destinazione macchine virtuali o computer fisici Windows o Linux, nel cloud o locali.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestione di tutti gli elementi DSC

La configurazione dello stato di automazione di Azure porta lo stesso livello di gestione alla configurazione dello stato desiderato di PowerShell offerta per gli script di PowerShell.Azure Automation State Configuration brings the same management layer to [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) as it offers for PowerShell scripting. Dal portale di Azure o da PowerShell è possibile gestire tutte le configurazioni, le risorse e i nodi di destinazione di DSC.

![Screenshot della pagina di accesso di Automazione di Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importazione di dati di report nei log di Monitoraggio di AzureImport of reporting data into Azure Monitor logs

I nodi gestiti con Configurazione stato di Automazione di Azure inviano dati dettagliati sullo stato dei report al server di pull predefinito. È possibile configurare Configurazione stato di Automazione di Azure per inviare questi dati all'area di lavoro Log Analytics. Vedere Inoltrare i dati dei report di Configurazione stato di automazione di Azure ai log di [Monitoraggio di Azure.See Forward Azure Automation State Configuration reporting data to Azure Monitor logs](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Prerequisiti per l'uso della configurazione dello stato di automazione di AzurePrerequisites for using Azure Automation State Configuration

Quando si usa La configurazione dello stato di automazione di Azure per DSC, considerare i requisiti seguenti.

### <a name="operating-system-requirements"></a>Requisiti del sistema operativo

Per i nodi che eseguono Windows, sono supportate le seguenti versioni:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Lo SKU del prodotto autonomo [di Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) non contiene un'implementazione di DSC. Pertanto non può essere gestito da PowerShell DSC o Azure Automation State Configuration.

Per i nodi che eseguono Linux, l'estensione DSC Linux supporta tutte le distribuzioni Linux elencate in [Distribuzioni Linux supportate](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisiti DSC

Per tutti i nodi Windows in esecuzione in Azure, [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) viene installato durante l'onboarding. Per i nodi che eseguono Windows Server 2012 e Windows 7, [Gestione remota Windows](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) è abilitato.

Per tutti i nodi Linux in esecuzione in Azure, [PowerShell DSC per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) viene installato durante l'onboarding.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configurazione di reti private

Se i nodi si trovano in una rete privata, sono necessari la porta e gli URL seguenti. Queste risorse forniscono la connettività di rete per il nodo gestito e consentono a DSC di comunicare con l'automazione di Azure.These resources provide network connectivity for the managed node and allow DSC to communicate with Azure Automation.

* Porta: solo TCP 443 necessario per l'accesso a Internet in uscita
* URL globale:**.azure-automation.net**
* URL globale del Gov Virginia degli Stati Uniti:**.azure-automation.us**
* Servizio agente: **\<https://\>workspaceId .agentsvc.azure-automation.net**

Se si utilizzano risorse DSC che comunicano tra i nodi, ad esempio le [risorse WaitFor](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), è necessario consentire anche il traffico tra i nodi. Vedere la documentazione per ogni risorsa DSC per comprendere questi requisiti di rete.

#### <a name="proxy-support"></a>Supporto proxy

Il supporto proxy per l'agente DSC è disponibile in Windows versione 1809 e successive. Questa opzione viene abilitata `ProxyURL` impostando `ProxyCredential` i valori per e nello script di [metaconfigurazione](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) utilizzato per registrare i nodi.

>[!NOTE]
>Azure Automation State Configuration does not provide DSC proxy support for previous versions of Windows.

Per i nodi Linux, l'agente DSC supporta il proxy e utilizza la `http_proxy` variabile per determinare l'URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Intervalli e spazio dei nomi di rete di Configurazione stato di automazione di AzureAzure Automation State Configuration intervalli e spazio dei nomi

Si consiglia di utilizzare gli indirizzi elencati di seguito quando si definiscono le eccezioni. Per gli indirizzi IP, è possibile scaricare gli [intervalli IP del data center di Microsoft Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

Se è disponibile un account di Automazione definito per un'area specifica, è possibile limitare la comunicazione a tale data center regionale. La tabella seguente indica il record DNS per ogni area:

| **Area** | **Record DNS** |
| --- | --- |
| Stati Uniti centro-occidentali | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Stati Uniti centro-meridionali |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Stati Uniti orientali 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canada centrale |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa occidentale |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa settentrionale |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Asia sud-orientale |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India centrale |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Giappone orientale |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia sud-orientale |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Regno Unito meridionale | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Per un elenco degli indirizzi IP di area invece dei nomi di area, scaricare il file XML degli [indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) dall'Area download Microsoft.

> [!NOTE]
> Il file XML degli indirizzi IP dei data center di Azure elenca gli intervalli di indirizzi IP usati nei data center di Microsoft Azure. Il file include gli intervalli per le risorse di calcolo, SQL e di archiviazione.
>
>Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana. È consigliabile scaricare il nuovo file XML ogni settimana Aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure.Then update your site to correctly identify services running in Azure. 

Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare DSC in Configurazione dello stato di automazione di Azure, vedere Introduzione alla configurazione dello stato di automazione di Azure.To get started using DSC in Azure Automation [State Configuration,](automation-dsc-getting-started.md)see Getting started with Azure Automation State Configuration .
- Per informazioni su come eseguire l'onboarding dei nodi, vedere [Onboarding di macchine per la gestione da parte](automation-dsc-onboarding.md)della configurazione dello stato di automazione di Azure .
- Per informazioni sulla compilazione di configurazioni DSC in modo da poterle assegnare ai nodi di destinazione, vedere [Compilazione di configurazioni in Configurazione dello stato](automation-dsc-compile.md)di automazione di Azure.To learn about compiling DSC configurations so that you can assign them to target nodes, see Compilazioning configurations in Azure Automation State Configuration .
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere Cmdlet per la configurazione dello stato di [automazione di Azure.](/powershell/module/azurerm.automation/#automation)
- Per informazioni sui prezzi, vedere Prezzi per la configurazione dello stato di automazione di Azure.For pricing [information,](https://azure.microsoft.com/pricing/details/automation/)see Azure Automation State Configuration pricing .
- Per un esempio di utilizzo della configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [Distribuzione continua tramite Configurazione stato](automation-dsc-cd-chocolatey.md)di automazione di Azure e Chocolatey .
