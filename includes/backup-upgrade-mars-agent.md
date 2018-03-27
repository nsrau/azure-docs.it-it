---
title: Eseguire l'aggiornamento dell'agente di Backup di Azure
description: Queste informazioni spiegano perché è consigliabile eseguire l'aggiornamento dell'agente di Backup di Azure e dove scaricare l'aggiornamento.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
## <a name="upgrade-the-mars-agent"></a>Eseguire l'aggiornamento dell'agente MARS
Le versioni dell'agente di Servizi di ripristino di Microsoft Azure Recovery Service (MARS) precedenti alla 2.0.9083.0 hanno una dipendenza dal Servizio di controllo di accesso di Azure (ACS). Nel 2018 il [Servizio di controllo di accesso di Azure (ACS) sarà deprecato](../articles/active-directory/develop/active-directory-acs-migration.md). A partire dal 19 marzo 2018, in tutte le versioni dell'agente MARS precedenti alla 2.0.9083.0 si verificheranno errori di backup. Per evitare o risolvere gli errori di backup, [eseguire l'aggiornamento dell'agente MARS alla versione più recente](https://go.microsoft.com/fwlink/?linkid=229525). Per identificare i server che richiedono un aggiornamento dell'agente MARS, seguire la procedura illustrata nel [blog su Backup relativo all'aggiornamento degli agenti di Backup di Azure](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). L'agente MARS viene usato per eseguire un backup in Azure dei tipi di dati seguenti:
- File 
- Dati sullo stato del sistema
- Backup di System Center DPM in Azure
- Server di backup di Azure (MABS)
