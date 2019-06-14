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
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 56310b7356dd9e263238234cf3e28bd498fa70fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127779"
---
## <a name="upgrade-the-mars-agent"></a>Eseguire l'aggiornamento dell'agente MARS

Le versioni dell'agente di Servizi di ripristino di Microsoft Azure (MARS) precedenti alla 2.0.9083.0 hanno una dipendenza dal Servizio di controllo di accesso di Azure (ACS). L'agente MARS è detto anche Agente di Backup di Azure. Nel 2018 il [Servizio di controllo di accesso di Azure (ACS) sarà deprecato](../articles/active-directory/develop/active-directory-acs-migration.md). A partire dal 19 marzo 2018, in tutte le versioni dell'agente MARS precedenti alla 2.0.9083.0 si verificheranno errori di backup. Per evitare o risolvere gli errori di backup, [eseguire l'aggiornamento dell'agente MARS alla versione più recente](https://go.microsoft.com/fwlink/?linkid=229525). Per identificare i server che richiedono un aggiornamento dell'agente MARS, seguire la procedura illustrata nel [Blog sul backup relativo all'aggiornamento degli agenti MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). L'agente MARS viene usato per eseguire il backup di file e cartelle e dei dati di stato del sistema in Azure. System Center DPM e il server di Backup di Azure usano l'agente di MARS per eseguire il backup dei dati in Azure.
