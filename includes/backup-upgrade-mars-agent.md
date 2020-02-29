---
title: Aggiornare l'agente di backup di Azure
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
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197122"
---
## <a name="upgrade-the-mars-agent"></a>Aggiornare l'agente MARS

Le versioni dell'agente Servizi di ripristino di Microsoft Azure (MARS) sotto 2.0.9083.0 hanno una dipendenza dal servizio di controllo di accesso di Azure. L'agente MARS è anche noto come agente di backup di Azure.

In 2018, Microsoft ha [deprecato il servizio di controllo di accesso di Azure](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). A partire dal 19 marzo 2018, per tutte le versioni dell'agente MARS al di sotto di 2.0.9083.0 si verificheranno errori di backup. Per evitare o risolvere gli errori di backup, [eseguire l'aggiornamento dell'agente MARS alla versione più recente](https://go.microsoft.com/fwlink/?linkid=229525). Per identificare i server che richiedono un aggiornamento dell'agente MARS, seguire i passaggi nel [Blog di backup per l'aggiornamento degli agenti Mars](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/).

L'agente MARS viene usato per eseguire il backup di file e cartelle e dei dati dello stato del sistema in Azure. System Center DPM e server di Backup di Azure usano l'agente MARS per eseguire il backup dei dati in Azure.
