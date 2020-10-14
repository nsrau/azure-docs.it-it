---
title: Gruppo di attendibilità del server
titleSuffix: Azure SQL Managed Instance
description: Informazioni sul gruppo di attendibilità del server e su come gestire la relazione di trust tra le istanze gestite di SQL Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: 6154625f1e943007d0ed4c3341dc1265657f3bfc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046351"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>Usare i gruppi di attendibilità del server per configurare e gestire l'attendibilità tra le istanze gestite di SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Il gruppo di trust del server è un concetto usato per la gestione dell'attendibilità tra le istanze gestite di SQL Azure. Creando un gruppo, viene stabilito un trust basato sui certificati tra i membri. Questa relazione di trust può essere utilizzata per diversi scenari tra istanze. Rimuovendo i server dal gruppo o eliminando il gruppo, la relazione di trust tra i server viene rimossa. Per creare o eliminare l'utente del gruppo di attendibilità del server, è necessario disporre delle autorizzazioni di scrittura per Istanza gestita.
Il [gruppo di attendibilità del server](https://aka.ms/mi-server-trust-group-arm) è un oggetto Azure Resource Manager che è stato etichettato come gruppo di **attendibilità SQL** nella portale di Azure.

> [!NOTE]
> Il gruppo di attendibilità del server è stato introdotto nell'anteprima pubblica delle transazioni distribuite tra le istanze gestite di SQL di Azure e attualmente presenta alcune limitazioni che verranno descritte più avanti in questo articolo.

## <a name="server-trust-group-setup"></a>Impostazione gruppo di attendibilità server

Nella sezione seguente viene descritta l'installazione del gruppo di attendibilità del server.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare a Istanza gestita SQL di Azure che si prevede di aggiungere a un gruppo di attendibilità del server appena creato.

3. In impostazioni **sicurezza** selezionare la scheda **gruppi di attendibilità SQL** .

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Gruppi di trust server":::

4. Nella pagina Configurazione gruppo di attendibilità server selezionare l'icona **nuovo gruppo** .

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Gruppi di trust server":::

5. Nel pannello Crea del **gruppo di attendibilità SQL** impostare il **nome del gruppo**. Deve essere univoco in tutte le aree in cui risiedono i membri del gruppo. **Ambito di trust** definisce il tipo di scenario tra istanze che è abilitato con il gruppo di attendibilità del server. Nell'anteprima l'unico ambito di attendibilità applicabile è costituito dalle **transazioni distribuite**, quindi è preselezionato e non può essere modificato. Tutti **i membri del gruppo** devono appartenere alla stessa **sottoscrizione** , ma possono trovarsi in gruppi di risorse diversi. Selezionare il **gruppo di risorse** e **SQL Server/istanza** per scegliere il istanza gestita SQL di Azure che sarà membro del gruppo.

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Gruppi di trust server":::

6. Una volta popolati tutti i campi obbligatori, fare clic su **Salva**.

## <a name="server-trust-group-maintenance-and-deletion"></a>Manutenzione ed eliminazione del gruppo di attendibilità del server

Il gruppo di trust del server non può essere modificato. Per rimuovere un Istanza gestita da un gruppo, è necessario eliminare il gruppo e crearne uno nuovo.

Nella sezione seguente viene descritto il processo di eliminazione del gruppo di trust del server. 
1. Accedere al portale di Azure.
2. Passare a un Istanza gestita appartenente al gruppo di trust.
3. In impostazioni **protezione** selezionare la scheda **gruppi di attendibilità SQL** .
4. Selezionare il gruppo di trust che si desidera eliminare.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Gruppi di trust server":::
5. Fare clic su **Elimina gruppo**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Gruppi di trust server":::
6. Digitare il nome del gruppo di attendibilità server per confermare l'eliminazione, quindi fare clic su **Elimina**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Gruppi di trust server":::

> [!NOTE]
> L'eliminazione del gruppo di attendibilità del server potrebbe non rimuovere immediatamente il trust tra le due istanze gestite. La rimozione dei trust può essere applicata richiamando un [failover](https://docs.microsoft.com/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) di istanze gestite. Controllare i [problemi noti](https://docs.microsoft.com/azure/azure-sql/database/doc-changes-updates-release-notes?tabs=managed-instance#known-issues) per gli aggiornamenti più recenti.

## <a name="limitations"></a>Limitazioni

Durante l'anteprima pubblica, le limitazioni seguenti si applicano ai gruppi di attendibilità del server.
 * Il nome del gruppo di attendibilità del server deve essere univoco in tutte le aree in cui i relativi membri sono.
 * Il gruppo può contenere solo istanze gestite da SQL di Azure e deve trovarsi nella stessa sottoscrizione di Azure.
 * In anteprima, il gruppo può avere esattamente due istanze gestite. Per eseguire transazioni distribuite in più di due istanze gestite, sarà necessario creare il gruppo di attendibilità del server per ogni coppia di istanze gestite.
 * Le transazioni distribuite sono l'unico ambito applicabile per i gruppi di attendibilità del server.
 * Il gruppo di attendibilità del server può essere gestito solo da portale di Azure. Il supporto per PowerShell e l'interfaccia della riga di comando verrà successivamente.
 * Il gruppo di trust del server non può essere modificato nel portale di Azure. È possibile crearla o eliminarla.
 * Eventuali limitazioni aggiuntive delle transazioni distribuite potrebbero essere correlate allo scenario. Il più importante è il fatto che deve essere disponibile la connettività tra le istanze gestite su endpoint privati, tramite il peering VNET o VNET. Assicurarsi di essere a conoscenza delle limitazioni correnti delle [transazioni distribuite per istanza gestita](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview#limitations).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle transazioni distribuite in Azure SQL Istanza gestita, vedere [Distributed Transactions](../database/elastic-transactions-overview.md).
* Per gli aggiornamenti della versione e lo stato dei problemi noti, vedere [Note sulla versione di istanza gestita](../database/doc-changes-updates-release-notes.md).
* Se si hanno richieste di funzionalità, aggiungerle al [forum istanza gestita](https://feedback.azure.com/forums/915676-sql-managed-instance).