---
title: Abilitare l'ottimizzazione automatica per il database SQL di Azure | Microsoft Docs
description: "È possibile abilitare facilmente l'ottimizzazione automatica nel database SQL di Azure."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: b599273874a4b5a3bbcb78284d69b4c8d02b5f2c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="enable-automatic-tuning"></a>Abilitare l'ottimizzazione automatica

Il database SQL di Azure è un servizio dati gestito automaticamente che esegue un monitoraggio costante delle query e identifica l'azione che è possibile eseguire per migliorare le prestazioni del carico di lavoro. È possibile esaminare le raccomandazioni e applicarle manualmente oppure delegare al database SQL di Azure l'applicazione automatica delle azioni correttive, condizione nota come **modalità di ottimizzazione automatica**. L'ottimizzazione automatica può essere abilitata a livello di server o di database.

## <a name="enable-automatic-tuning-on-server"></a>Abilitare l'ottimizzazione automatica nel server
A livello di server è possibile scegliere di ereditare o meno la configurazione dell'ottimizzazione automatica da "Impostazioni predefinite di Azure". Le impostazioni predefinite di Azure sono FORCE_LAST_GOOD_PLAN (abilitato), CREATE_INDEX (abilitato) e DROP_INDEX (disabilitato).

## <a name="configure-automiatic-tuning-e-mail-notifications"></a>Configurare notifiche tramite posta elettronica per l'ottimizzazione automatica

Vedere [Notifiche tramite posta elettronica per l'ottimizzazione automatica](sql-database-automatic-tuning-email-notifications.md)

### <a name="azure-portal"></a>Portale di Azure
Per abilitare l'ottimizzazione automatica nel server di database SQL di Azure, passare al server nel portale di Azure e quindi selezionare **Ottimizzazione automatica** nel menu. Selezionare le opzioni di ottimizzazione automatica che si vuole abilitare e selezionare **Applica**:

![Server](./media/sql-database-automatic-tuning-enable/server.png)

Le opzioni di ottimizzazione automatica nel server vengono applicate a tutti i database nel server. Per impostazione predefinita, tutti i database ereditano la configurazione dal relativo server padre, ma è possibile modificare questa impostazione e specificarla singolarmente per ogni database.

### <a name="rest-api"></a>API REST
[Fare clic qui per altre informazioni su come abilitare l'ottimizzazione automatica a livello di server tramite l'API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Abilitare l'ottimizzazione automatica per un database singolo

Il database SQL di Azure consente di specificare singolarmente la configurazione di ottimizzazione automatica per ogni database. A livello di database è possibile scegliere di ereditare o meno la configurazione dell'ottimizzazione automatica dal server padre o da "Impostazioni predefinite di Azure". Le impostazioni predefinite di Azure sono FORCE_LAST_GOOD_PLAN (abilitato), CREATE_INDEX (abilitato) e DROP_INDEX (disabilitato).

> [!NOTE]
> La raccomandazione generale suggerisce di gestire la configurazione di ottimizzazione automatica a livello del server, in modo che le stesse impostazioni di configurazione possano essere applicate automaticamente in ogni database. Configurare l'ottimizzazione automatica per un singolo database se il database è diverso dagli altri nello stesso server.
>

### <a name="azure-portal"></a>Portale di Azure

Per abilitare l'ottimizzazione automatica per un singolo database, passare al database nel portale di Azure e quindi selezionare **Ottimizzazione automatica**. È possibile configurare un singolo database in modo che erediti le impostazioni dal server selezionando l'opzione oppure è possibile specificare la configurazione per un database singolarmente.

![Database](./media/sql-database-automatic-tuning-enable/database.png)

Dopo aver selezionato la configurazione appropriata, fare clic su **Applica**.

### <a name="rest-api"></a>API REST
[Fare clic qui per altre informazioni su come abilitare l'ottimizzazione automatica su un singolo database tramite l'API REST](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Per abilitare l'ottimizzazione automatica per un singolo database con Transact-SQL, connettersi al database ed eseguire la query seguente:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
L'impostazione dell'ottimizzazione automatica su AUTO verrà applicata alle impostazioni predefinite di Azure. Se viene impostata su INHERIT, la configurazione dell'ottimizzazione automatica verrà ereditata dal server padre. Se si sceglie CUSTOM, l'ottimizzazione automatica dovrà essere configurata manualmente.

Per configurare le singole opzioni di ottimizzazione automatica con Transact-SQL, connettersi al database ed eseguire la query seguente:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Impostare l'opzione di ottimizzazione specifica su ON per eseguire l'override di qualsiasi impostazione ereditata dal database e abilitare l'opzione di ottimizzazione specifica. Se viene impostata su OFF, verrà eseguito l'override di qualsiasi impostazione ereditata dal database, ma l'opzione di ottimizzazione specifica verrà disabilitata. L'opzione di ottimizzazione automatica impostata su DEFAULT fa sì che la configurazione verrà ereditata dall'impostazione dell'ottimizzazione automatica a livello di database.  

## <a name="disabled-by-the-system"></a>Disabilitazione da parte del sistema
L'ottimizzazione automatica esegue il monitoraggio di tutte le azioni eseguite a livello di database e in alcuni casi potrebbe non funzionare correttamente nel database. In questo caso, l'opzione di ottimizzazione viene disabilitata dal sistema. Nella maggior parte dei casi ciò accade perché Query Store non è abilitato o è in stato di sola lettura in un database specifico.

## <a name="next-steps"></a>Passaggi successivi
* Leggere l'[articolo sull'ottimizzazione automatica](sql-database-automatic-tuning.md) per altre informazioni sull'ottimizzazione automatica e sull'utilità di questa funzione per il miglioramento delle prestazioni.
* Vedere le [Raccomandazioni per le prestazioni](sql-database-advisor.md) per una panoramica delle raccomandazioni per le prestazioni per il database SQL di Azure.
* Vedere l'articolo [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) per informazioni sull'analisi dell'impatto sulle prestazioni delle query principali.
