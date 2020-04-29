---
title: 'Panoramica di Configurazione stato di Automazione di Azure '
description: Panoramica di Azure Automation state Configuration (DSC), dei relativi termini e dei problemi noti
keywords: powershell dsc, configurazione dello stato desiderato, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: afceb11180662416aa4953b8b58ef03ffaa70eec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406191"
---
# <a name="state-configuration-overview"></a>Panoramica della configurazione dello stato

La configurazione dello stato di automazione di Azure è un servizio di Azure che consente di scrivere, gestire e compilare [configurazioni](/powershell/scripting/dsc/configurations/configurations)di PowerShell DSC (Desired state Configuration). Il servizio importa anche [le risorse DSC](/powershell/scripting/dsc/resources/resources)e assegna le configurazioni ai nodi di destinazione, tutto nel cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Perché usare Configurazione stato di Automazione di Azure

Configurazione stato di Automazione di Azure offre diversi vantaggi rispetto all'uso di DSC all'esterno di Azure.

### <a name="built-in-pull-server"></a>Server di pull predefinito

La configurazione dello stato di automazione di Azure fornisce un server di pull DSC simile alla [funzionalità di Windows DSC-Service](/powershell/scripting/dsc/pull-server/pullserver). I nodi di destinazione possono ricevere automaticamente le configurazioni, conformi allo stato desiderato e segnalare la rispettiva conformità. Il server di pull predefinito in Automazione di Azure elimina la necessità di configurare e gestire un proprio server di pull. Automazione di Azure può avere come destinazione macchine virtuali o computer fisici Windows o Linux, nel cloud o locali.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestione di tutti gli elementi DSC

La configurazione dello stato di automazione di Azure porta lo stesso livello di gestione a PowerShell DSC ( [desired state Configuration](/powershell/scripting/dsc/overview/overview) ) che offre per gli script di PowerShell. Dal portale di Azure o da PowerShell è possibile gestire tutte le configurazioni, le risorse e i nodi di destinazione di DSC.

![Screenshot della pagina di accesso di Automazione di Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importazione di dati di report nei log di monitoraggio di Azure

I nodi gestiti con Configurazione stato di Automazione di Azure inviano dati dettagliati sullo stato dei report al server di pull predefinito. È possibile configurare Configurazione stato di Automazione di Azure per inviare questi dati all'area di lavoro Log Analytics. Vedere [trasmettere i dati di report della configurazione dello stato di automazione di Azure ai log di monitoraggio di Azure](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Prerequisiti per l'uso della configurazione dello stato di automazione di Azure

Quando si usa la configurazione dello stato di automazione di Azure per DSC, tenere presenti i requisiti seguenti.

### <a name="operating-system-requirements"></a>Requisiti del sistema operativo

Per i nodi che eseguono Windows sono supportate le versioni seguenti:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Lo SKU del prodotto [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) autonomo non contiene un'implementazione di DSC. Non può quindi essere gestita da PowerShell DSC o dalla configurazione dello stato di automazione di Azure.

Per i nodi che eseguono Linux, l'estensione DSC per Linux supporta tutte le distribuzioni di Linux elencate in [distribuzioni di Linux supportate](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisiti DSC

Per tutti i nodi Windows in esecuzione in Azure, [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) viene installato durante l'onboarding. Per i nodi che eseguono Windows Server 2012 e Windows 7, [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) è abilitato.

Per tutti i nodi Linux in esecuzione in Azure, [PowerShell DSC per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) viene installato durante l'onboarding.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configurazione di reti private

Se i nodi si trovano in una rete privata, sono necessari gli URL e la porta seguenti. Queste risorse forniscono la connettività di rete per il nodo gestito e consentono a DSC di comunicare con automazione di Azure.

* Porta: solo TCP 443 necessari per l'accesso a Internet in uscita
* URL globale: ***. Azure-Automation.NET**
* URL globale di US Gov Virginia: ***. Azure-Automation.US**
* Servizio Agent: **https://\<workspaceId\>. agentsvc.Azure-Automation.NET**

Se si usano risorse DSC che comunicano tra i nodi, ad esempio le [risorse di aspetter *](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), è necessario anche consentire il traffico tra i nodi. Per informazioni sui requisiti di rete, vedere la documentazione relativa a ogni risorsa DSC.

#### <a name="proxy-support"></a>Supporto proxy

Il supporto del proxy per l'agente DSC è disponibile nella versione 1809 e successive di Windows. Questa opzione viene abilitata impostando i valori `ProxyURL` per `ProxyCredential` e nello [script di metaconfigurazione](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) usato per registrare i nodi.

>[!NOTE]
>La configurazione dello stato di automazione di Azure non fornisce il supporto proxy DSC per le versioni precedenti di Windows.

Per i nodi Linux, l'agente DSC supporta il proxy e `http_proxy` usa la variabile per determinare l'URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Spazi dei nomi e intervalli di rete della configurazione dello stato di automazione

Per la definizione delle eccezioni è consigliabile usare gli indirizzi elencati di seguito. Per gli indirizzi IP, è possibile scaricare gli [intervalli IP del data center Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Questo file viene aggiornato ogni settimana e presenta gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP.

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
>Viene pubblicata una versione aggiornata del file ogni settimana. Il file include gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana. È consigliabile scaricare il nuovo file XML ogni settimana Aggiornare quindi il sito per identificare correttamente i servizi in esecuzione in Azure. 

Per gli utenti di ExpressRoute è importante sottolineare che questo file viene usato per aggiornare l'annuncio BGP (Border Gateway Protocol) dello spazio di Azure la prima settimana del mese.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare DSC in configurazione dello stato di automazione di Azure, vedere [Guida introduttiva alla configurazione dello stato di automazione di Azure](automation-dsc-getting-started.md).
- Per informazioni su come caricare i nodi, vedere Caricamento [di computer per la gestione mediante la configurazione dello stato di automazione di Azure](automation-dsc-onboarding.md).
- Per informazioni sulla compilazione delle configurazioni DSC per poterle assegnare ai nodi di destinazione, vedere [compilazione di configurazioni in Azure Automation state Configuration](automation-dsc-compile.md).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per informazioni sui prezzi, vedere [prezzi di configurazione dello stato di automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per un esempio di come usare la configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [distribuzione continua con la configurazione dello stato di automazione di Azure e cioccolato](automation-dsc-cd-chocolatey.md).
