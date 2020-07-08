---
title: Eseguire l'aggiornamento di Azure Backup Agent
description: Queste informazioni spiegano perché è necessario aggiornare l'agente di backup di Azure e dove scaricare l'aggiornamento.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78673206"
---
## <a name="upgrade-the-mars-agent"></a>Aggiornare l'agente MARS

Le versioni dell'agente Servizi di ripristino di Microsoft Azure (MARS) sotto 2.0.9083.0 hanno una dipendenza dal servizio di controllo di accesso di Azure. L'agente MARS è anche noto come agente di backup di Azure.

In 2018, Microsoft ha [deprecato il servizio di controllo di accesso di Azure](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). A partire dal 19 marzo 2018, per tutte le versioni dell'agente MARS al di sotto di 2.0.9083.0 si verificheranno errori di backup. Per evitare o risolvere gli errori di backup, [aggiornare l'agente Mars alla versione più recente](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Per identificare i server che richiedono un aggiornamento dell'agente MARS, attenersi alla procedura descritta in [aggiornare l'agente di servizi di ripristino di Microsoft Azure (Mars)](../articles/backup/upgrade-mars-agent.md).

L'agente MARS viene usato per eseguire il backup di file e cartelle e dei dati dello stato del sistema in Azure. System Center DPM e server di Backup di Azure usano l'agente MARS per eseguire il backup dei dati in Azure.
