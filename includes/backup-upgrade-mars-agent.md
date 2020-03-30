---
title: Eseguire l'aggiornamento di Azure Backup Agent
description: Queste informazioni spiegano perché è necessario aggiornare l'agente di Backup di Azure e dove scaricare l'aggiornamento.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673206"
---
## <a name="upgrade-the-mars-agent"></a>Aggiornare l'agente MARS

Le versioni dell'agente di Microsoft Azure Recovery Services (MARS) inferiori alla 2.0.9083.0 dipendono dal servizio Controllo di accesso di Azure.Versions of the Microsoft Azure Recovery Services (MARS) Agent below 2.0.9083.0 have a dependency on the Azure Access Control service. L'agente MARS viene anche definito agente di Backup di Azure.The MARS Agent is also referred to the Azure Backup Agent.

Nel 2018 Microsoft [ha deprecato il servizio Controllo](../articles/active-directory/azuread-dev/active-directory-acs-migration.md)di accesso di Azure. A partire dal 19 marzo 2018, tutte le versioni dell'agente MARS al di sotto della 2.0.9083.0 si verificheranno errori di backup. Per evitare o risolvere gli errori di backup, [aggiornare l'agente MARS alla versione più recente.](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent) Per identificare i server che richiedono un aggiornamento dell'agente MARS, seguire i passaggi descritti in [Aggiornare l'agente di Microsoft Azure Recovery Services (MARS).](../articles/backup/upgrade-mars-agent.md)

L'agente MARS viene usato per eseguire il backup di file e cartelle e dati dello stato del sistema in Azure.The MARS Agent is used to back up files and folders and system state data to Azure. System Center DPM e Azure Backup Server utilizzano l'agente MARS per eseguire il backup dei dati in Azure.
