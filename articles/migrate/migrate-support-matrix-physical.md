---
title: Supporto per la valutazione del server fisico con Azure Migrate
description: Informazioni sul supporto per la valutazione del server fisico con Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028770"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matrice di supporto per la valutazione del server fisico 

È possibile usare il [servizio Azure migrate](migrate-overview.md) per valutare ed eseguire la migrazione di computer nel cloud Microsoft Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto per la valutazione e la migrazione di server fisici locali.


## <a name="overview"></a>Overview

Per valutare i computer locali per la migrazione ad Azure con questo articolo, è necessario aggiungere lo strumento Azure Migrate: server Assessment a un progetto Azure Migrate. Si distribuisce il [dispositivo Azure migrate](migrate-appliance.md). L'appliance individua continuamente i computer locali e invia i dati di configurazione e delle prestazioni in Azure. Dopo l'individuazione del computer, si raccolgono i computer individuati in gruppi ed è stata eseguita una valutazione per un gruppo

## <a name="limitations"></a>Limitazioni

**Supporto** | **Dettagli**
--- | ---
**Limiti di valutazione**| Individuare e valutare fino a 35.000 di server fisici in un singolo [progetto](migrate-support-matrix.md#azure-migrate-projects).
**Limiti del progetto** | È possibile creare più progetti in una sottoscrizione di Azure. Un progetto può includere VM VMware, macchine virtuali Hyper-V e server fisici, fino ai limiti di valutazione.
**Individuazione** | L'appliance Azure Migrate è in grado di individuare fino a 250 di server fisici.
**Valutazione** | È possibile aggiungere fino a 35.000 computer in un singolo gruppo.<br/><br/> È possibile valutare fino a 35.000 computer in un'unica valutazione.

[Altre](concepts-assessment-calculation.md) informazioni sulle valutazioni.




## <a name="physical-server-requirements"></a>Requisiti del server fisico

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione server fisico**       | Il server fisico può essere autonomo o distribuito in un cluster. |
| **Autorizzazioni**           | **Windows:** Configurare un account utente locale in tutti i server Windows che si desidera includere nell'individuazione. L'account utente deve essere aggiunto a questi gruppi: Desktop remoto utenti, Performance Monitor Users e Performance Log Users. <br/> **Linux:** È necessario un account radice nei server Linux che si desidera individuare. |
| **Sistema operativo** | Sono supportati tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , tranne i seguenti:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Requisiti del dispositivo Azure Migrate

Azure Migrate usa l' [appliance Azure migrate](migrate-appliance.md) per l'individuazione e la valutazione. L'appliance per i server fisici può essere eseguita in una macchina virtuale o in un computer fisico. È possibile configurarlo usando uno script di PowerShell che è possibile scaricare dal portale di Azure.

- Informazioni sui [requisiti degli appliance](migrate-appliance.md#appliance---physical) per i server fisici.
- Informazioni sugli [URL](migrate-appliance.md#url-access) a cui l'appliance deve accedere.

## <a name="port-access"></a>Accesso alla porta

Nella tabella seguente sono riepilogati i requisiti di porta per la valutazione.

**Dispositivo** | **Connection**
--- | ---
**Appliance** | Connessioni in ingresso sulla porta TCP 3389 per consentire le connessioni Desktop remoto al dispositivo.<br/> Connessioni in ingresso sulla porta 44368 per accedere in remoto all'app di gestione Appliance usando l'URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Connessioni in uscita sulle porte 443, 5671 e 5672 per inviare i metadati di individuazione e prestazioni a Azure Migrate.
**Server fisici** | **Windows:** Connessioni in ingresso sulla porta 443, porte WinRM 5985 (HTTP) e 5986 (HTTPS) per il pull dei metadati di configurazione e delle prestazioni dai server Windows. <br/> **Linux:**  Connessioni in ingresso sulla porta 22 (UDP) per eseguire il pull dei metadati di configurazione e delle prestazioni dai server Linux. |


## <a name="next-steps"></a>Passaggi successivi

[Preparare la valutazione del server fisico](tutorial-prepare-physical.md).
