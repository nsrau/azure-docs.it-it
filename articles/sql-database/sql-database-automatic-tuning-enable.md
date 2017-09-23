---
title: Abilitare l'ottimizzazione automatica per il database SQL di Azure | Microsoft Docs
description: "È possibile abilitare facilmente l'ottimizzazione automatica nel database SQL di Azure."
services: sql-database
documentationcenter: 
author: vvasic
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 06/05/2016
ms.author: vvasic
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: b391b1f7aa37c5a06fc320ce892534187deb4959
ms.contentlocale: it-it
ms.lasthandoff: 06/13/2017

---
# <a name="enable-automatic-tuning"></a>Abilitare l'ottimizzazione automatica

Il database SQL di Azure è un servizio dati gestito automaticamente che esegue un monitoraggio costante delle query e identifica l'azione che è possibile eseguire per migliorare le prestazioni del carico di lavoro. È possibile esaminare le raccomandazioni e applicarle manualmente oppure delegare al database SQL di Azure l'applicazione automatica delle azioni correttive, condizione nota come **modalità di ottimizzazione automatica**. L'ottimizzazione automatica può essere abilitata a livello di server o di database.

## <a name="enable-automatic-tuning-on-server"></a>Abilitare l'ottimizzazione automatica nel server

Per abilitare l'ottimizzazione automatica nel server di database SQL di Azure, passare al server nel portale di Azure e quindi selezionare **Ottimizzazione automatica** nel menu. Selezionare le opzioni di ottimizzazione automatica che si vuole abilitare e selezionare **Applica**:

![Server](./media/sql-database-automatic-tuning-enable/server.png)

Le opzioni di ottimizzazione automatica nel server vengono applicate a tutti i database nel server. Per impostazione predefinita, tutti i database ereditano la configurazione dal relativo server padre, ma è possibile modificare questa impostazione e specificarla singolarmente per ogni database.

## <a name="configure-automatic-tuning-on-database"></a>Configurare l'ottimizzazione automatica per il database

Il portale di Azure consente di specificare singolarmente la configurazione di ottimizzazione automatica in ogni database.

> [!NOTE]
> La raccomandazione generale suggerisce di gestire la configurazione di ottimizzazione automatica a livello del server, in modo che le stesse impostazioni di configurazione possano essere applicate automaticamente in ogni database. Configurare l'ottimizzazione automatica per un singolo database se il database è diverso dagli altri nello stesso server.
>

Per abilitare l'ottimizzazione automatica per un singolo database, passare al database nel portale di Azure e quindi selezionare **Ottimizzazione automatica**. È possibile configurare un singolo database in modo che erediti le impostazioni dal database selezionando la casella di controllo oppure è possibile specificare la configurazione per un database singolarmente.

![Database](./media/sql-database-automatic-tuning-enable/database.png)

Dopo aver selezionato la configurazione appropriata, fare clic su **Applica**.

## <a name="next-steps"></a>Passaggi successivi
* Leggere l'[articolo sull'ottimizzazione automatica](sql-database-automatic-tuning.md) per altre informazioni sull'ottimizzazione automatica e sull'utilità di questa funzione per il miglioramento delle prestazioni.
* Vedere le [Raccomandazioni per le prestazioni](sql-database-advisor.md) per una panoramica delle raccomandazioni per le prestazioni per il database SQL di Azure.
* Vedere l'articolo [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) per informazioni sull'analisi dell'impatto sulle prestazioni delle query principali.

