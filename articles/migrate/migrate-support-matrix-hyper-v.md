---
title: Supporto per la valutazione di Hyper-V in Azure Migrate
description: Informazioni sul supporto per la valutazione di Hyper-V con Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 20bdbb16d2f0610f6519424141b09190eae3cc42
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028791"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matrice di supporto per la valutazione di Hyper-V

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la valutazione delle macchine virtuali Hyper-V con [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Per informazioni sulla migrazione di macchine virtuali Hyper-V in Azure, vedere la matrice di [supporto](migrate-support-matrix-hyper-v-migration.md)per la migrazione.

## <a name="overview"></a>Overview

Per valutare i computer locali per la migrazione ad Azure con questo articolo, è necessario aggiungere lo strumento Azure Migrate: server Assessment a un progetto Azure Migrate. Si distribuisce il [dispositivo Azure migrate](migrate-appliance.md). L'appliance individua continuamente i computer locali e invia i dati di configurazione e delle prestazioni in Azure. Dopo l'individuazione del computer, si raccolgono i computer individuati in gruppi ed è stata eseguita una valutazione per un gruppo.


## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione**| Individuare e valutare fino a 35.000 macchine virtuali Hyper-V in un singolo [progetto](migrate-support-matrix.md#azure-migrate-projects).
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure. Un progetto può includere VM VMware, macchine virtuali Hyper-V e server fisici, fino ai limiti di valutazione.
**Individuazione** | L'appliance Azure Migrate è in grado di rilevare fino a 5000 VM Hyper-V.<br/><br/> Il dispositivo può connettersi a un massimo di 300 host Hyper-V.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 VM in un'unica valutazione.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.



## <a name="hyper-v-host-requirements"></a>Requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione host**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. <br/> In alternativa, se non si desidera assegnare autorizzazioni di amministratore, creare un account utente locale o di dominio e aggiungere l'utente a questi gruppi: utenti di gestione remota, amministratori di Hyper-V e performance monitor. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2.<br/> Non è possibile valutare le macchine virtuali presenti negli host Hyper-V che eseguono Windows Server 2012. |
| **Comunicazione remota di PowerShell**   | Deve essere abilitato in ogni host. |
| **Replica Hyper-V**       | Se si usa la replica Hyper-V (o sono presenti più macchine virtuali con gli stessi identificatori di VM) e si individuano le VM originali e replicate usando Azure Migrate, la valutazione generata da Azure Migrate potrebbe non essere accurata. |


## <a name="hyper-v-vm-requirements"></a>Requisiti della macchina virtuale Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) supportati da Azure. |
| **Integration Services**       | Per acquisire le informazioni sul sistema operativo, è necessario che [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) sia in esecuzione in macchine virtuali valutate. |


## <a name="azure-migrate-appliance-requirements"></a>Requisiti del dispositivo Azure Migrate

Azure Migrate usa l' [appliance Azure migrate](migrate-appliance.md) per l'individuazione e la valutazione. L'appliance per Hyper-V viene eseguita in una macchina virtuale Hyper-V e viene distribuita usando un VHD Hyper-V compresso che è possibile scaricare dal portale di Azure. 

- Informazioni sui [requisiti di appliance](migrate-appliance.md#appliance---hyper-v) per Hyper-V.
- Informazioni sugli [URL](migrate-appliance.md#url-access) a cui l'appliance deve accedere.

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Connessioni in uscita sulle porte 443, 5671 e 5672 per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Host/cluster Hyper-V** | Connessioni in ingresso sulle porte WinRM 5985 (HTTP) e 5986 (HTTPS) per eseguire il pull dei metadati di configurazione e delle prestazioni delle VM Hyper-V usando una sessione di Common Information Model (CIM).



## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione della macchina virtuale Hyper-V](tutorial-prepare-hyper-v.md)
