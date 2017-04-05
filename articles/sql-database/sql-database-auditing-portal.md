---
title: Portale di Azure - Gestire il controllo del database SQL| Documentazione Microsoft
description: "Il controllo dei database SQL di Azure può essere configurato nel portale di Azure, per tenere traccia degli eventi di database e scriverli in un log di controllo nell&quot;account di archiviazione di Azure."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/25/2017
ms.author: ronitr;giladm
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e20a30a565198c01f213895a87fe8807a37272cb
ms.lasthandoff: 03/30/2017


---

# <a name="configure-and-manage-sql-database-auditing-in-the-azure-portal"></a>Configurare e gestire il controllo del database SQL nel portale di Azure

La sezione seguente descrive come configurare e gestire il controllo usando il portale di Azure. Per configurare e gestire il controllo tramite PowerShell, vedere [Configurare il controllo con PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 

Per una panoramica sul del controllo, vedere [Controllo del database SQL](sql-database-auditing.md).

## <a name="configure-blob-auditing"></a>Configurare il controllo BLOB

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare al pannello di configurazione del database SQL/SQL Server che si vuole controllare. Nel pannello Impostazioni selezionare **Controllo e rilevamento delle minacce**.

    ![Riquadro di spostamento](./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png)
3. Nel pannello per la configurazione del controllo del database è possibile selezionare la casella di controllo **Eredita impostazioni di controllo del server** per indicare che il database viene controllato in base alle impostazioni del relativo server. Se questa opzione è selezionata, viene visualizzato il collegamento **Visualizza impostazioni di controllo del server** che consente di visualizzare o modificare le impostazioni di controllo del server da questo contesto.

    ![Riquadro di spostamento][2]
4. Se si vuole abilitare il controllo BLOB a livello di database, in aggiunta o al posto del controllo a livello di server, **deselezionare** l'opzione **Eredita impostazioni di controllo del server**, **attivare** Controllo e scegliere il tipo di controllo **BLOB**.

    ![Riquadro di spostamento][3]
5. Selezionare i **Dettagli di archiviazione** per aprire il pannello di archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure in cui vengono salvati i log e il periodo di conservazione, dopo il quale vengono eliminati i vecchi log, quindi fare clic su **OK** nella parte inferiore. **Suggerimento:** per sfruttare al massimo i modelli di report preconfigurati, usare lo stesso account di archiviazione per tutti i database controllati.

    <a id="storage-screenshot"></a>
    ![Riquadro di spostamento][4]
6. Per personalizzare gli eventi controllati, è possibile usare PowerShell o l'API REST.
7. Dopo aver configurato le impostazioni di controllo, è possibile attivare la nuova funzionalità Rilevamento delle minacce (anteprima) e configurare gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. La funzione di Rilevamento delle minacce consente di ricevere avvisi proattivi sulle attività del database anomale che possono indicare potenziali minacce alla protezione. Vedere [Rilevamento delle minacce](sql-database-threat-detection.md) per altre informazioni.
8. Fare clic su **Save**.


## <a name="table-auditing"></a>Controllo Tabella

> [!IMPORTANT]
> Prima di impostare il **controllo Tabella** verificare se è in uso un ["Client di livello inferiore"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md). Se le impostazioni del firewall sono restrittive, tenere anche presente che l'[endpoint IP del database viene modificato](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) quando si abilita il controllo tabelle.
>

1. Avviare il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare al pannello di configurazione del database SQL/SQL Server che si vuole controllare. Nel pannello Impostazioni selezionare **Controllo e rilevamento minacce** (*[vedere la schermata illustrata nella sezione Controllo BLOB](#auditing-screenshot)*).
3. Nel pannello per la configurazione del controllo del database è possibile selezionare la casella di controllo **Eredita impostazioni di controllo del server** per indicare che il database viene controllato in base alle impostazioni del relativo server. Se questa opzione è selezionata, viene visualizzato il collegamento **Visualizza impostazioni di controllo del server** che consente di visualizzare o modificare le impostazioni di controllo del server da questo contesto.

    ![Riquadro di spostamento][2]
4. Se si preferisce non ereditare le impostazioni di controllo dal server, **deselezionare** l'opzione **Eredita impostazioni di controllo dal server**, **attivare** Controllo e scegliere il tipo di controllo **Tabella**.

    ![Riquadro di spostamento][3-tbl]
5. Selezionare i **Dettagli di archiviazione** per aprire il pannello di archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure in cui vengono salvati i log e il periodo di conservazione, dopo il quale vengono eliminati i vecchi log. **Suggerimento:** per sfruttare al meglio i modelli di report preconfigurati, usare lo stesso account di archiviazione per tutti i database controllati (*[vedere la schermata illustrata nella sezione Controllo BLOB](#storage-screenshot)*).
6. Fare clic su **Eventi controllati** per personalizzare gli eventi da controllare. Nel pannello Registrazione per evento fare clic su **Operazione riuscita** e **Operazione non riuscita** per registrare tutti gli eventi oppure scegliere singole categorie di eventi.

    ![Riquadro di spostamento][5]
7. Dopo aver configurato le impostazioni di controllo, è possibile attivare la nuova funzionalità Rilevamento delle minacce (anteprima) e configurare gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. La funzione di Rilevamento delle minacce consente di ricevere avvisi proattivi sulle attività del database anomale che possono indicare potenziali minacce alla protezione. Vedere [Rilevamento delle minacce](sql-database-threat-detection.md) per altre informazioni.
8. Fare clic su **Save**.

## <a name="auditing-geo-replicated-databases"></a>Controllo dei database con replica geografica

Se si usano database con replica geografica è possibile configurare il controllo nel database primario, nel database secondario o in entrambi, a seconda del tipo di controllo.

**Controllo Tabella**: è possibile configurare un criterio separato, a livello di database o a livello di server, per ciascuno dei due database (primario e secondario).

**Controllo BLOB**: seguire questa procedura:

1. **Database primario:** attivare il controllo BLOB sul server o sullo stesso database.
2. **Database secondario**: il controllo BLOB può essere attivato/disattivato solo dalle impostazioni di controllo del database primario.

   * Attivare il controllo BLOB nel database primario. Il controllo BLOB deve essere abilitato nello *stesso database primario* e non nel server.
   * Dopo che il controllo BLOB è stato abilitato nel database primario, viene abilitato anche nel database secondario.

    > [!IMPORTANT]
    > Per impostazione predefinita, le impostazioni di archiviazione per il database secondario sono identiche a quelle del database primario e generano traffico tra aree. È possibile evitare questo problema abilitando il controllo BLOB sul server secondario e configurando la risorsa di archiviazione locale nelle impostazioni di archiviazione del server secondario. In questo modo viene eseguito l'override del percorso di archiviazione per il database secondario e di conseguenza ogni database salva i log di controllo nella risorsa di archiviazione locale.  

## <a name="viewing-blob-auditing-logs"></a>Visualizzare log di controllo BLOB

I log di controllo BLOB vengono salvati come un insieme di file BLOB in un contenitore denominato "**sqldbauditlogs**".

Per altre informazioni sulla gerarchia delle cartelle di archiviazione dei log di controllo BLOB, le convenzioni di denominazione BLOB e il formato dei log, vedere le [informazioni di riferimento sul formato BLOB dei log di controllo (download del file doc)](https://go.microsoft.com/fwlink/?linkid=829599).

Esistono diversi metodi per visualizzare i log di controllo BLOB:

* Tramite il [portale di Azure](https://portal.azure.com): aprire il database corrispondente. Nella parte superiore del pannello Controllo e rilevamento minacce fare clic su **Visualizza log di controllo**.

    ![Riquadro di spostamento][10]

    Viene aperto il pannello Record di controllo in cui sarà possibile visualizzare i log.

    - Si può scegliere di visualizzare date specifiche facendo clic su **Filtro** nell'area superiore del pannello Record di controllo
    - È possibile passare dai record di controllo creati da criteri del server ai record di controllo creati dai criteri del database e viceversa

    ![Riquadro di spostamento][11]
* Scaricare i file di log dal contenitore BLOB di Archiviazione di Azure tramite il portale o tramite uno strumento come [Azure Storage Explorer](http://storageexplorer.com/).

    Dopo aver scaricato il file di log in locale, è possibile fare doppio clic su file per aprire, visualizzare e analizzare i log in SSMS.

* Altri metodi:

   * Per scaricare più file contemporaneamente tramite Azure Storage Explorer, fare clic su una sottocartella specifica (ad esempio una sottocartella che include tutti i file di log per una determinata data) e scegliere **Salva con nome** per salvare i dati in una cartella locale.

       Dopo aver scaricato diversi file (o un'intera giornata, come descritto in precedenza), è possibile unirli in locale nel modo seguente:

       **Aprire SSMS -> File -> Apri -> Unisci eventi estesi -> Scegliere tutti i file da unire**
   * A livello di codice:

     * [Libreria dei lettori di Eventi estesi C#](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/)
     * [Esecuzione di query sui file di eventi estesi tramite PowerShell](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)

   * È stata creata un'[applicazione di esempio](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) che viene eseguita in Azure e usa le API OMS pubbliche per inviare i log di controllo SQL in OMS per l'uso tramite il dashboard OMS.

## <a name="viewing-table-audit-logs"></a>Visualizzazione dei log di controllo Tabella

I log di controllo Tabella vengono salvati come una raccolta di tabelle di Archiviazione di Azure con prefisso **SQLDBAuditLogs**.

Per altre informazioni sui log di controllo in formato Tabella, vedere le [informazioni di riferimento sul formato Tabella dei log di controllo (download del file doc)](http://go.microsoft.com/fwlink/?LinkId=506733).

Esistono diversi metodi per visualizzare i log di controllo Tabella:

* Tramite il [portale di Azure](https://portal.azure.com): aprire il database corrispondente. Nella parte superiore del pannello Controllo e rilevamento minacce fare clic su **Visualizza log di controllo**.

    ![Riquadro di spostamento][10]

    Viene aperto il pannello Record di controllo in cui sarà possibile visualizzare i log.

    * Si può scegliere di visualizzare date specifiche facendo clic su **Filtro** nell'area superiore del pannello Record di controllo
    * È possibile scaricare e visualizzare i log di controllo in formato Excel facendo clic su **Apri in Excel** nell'area superiore del pannello Record di controllo

    ![Riquadro di spostamento][12]

* In alternativa si può scaricare un modello di report preconfigurato in formato [foglio di calcolo di Excel scaricabile](http://go.microsoft.com/fwlink/?LinkId=403540) che consente di analizzare rapidamente i dati di log. Per usare il modello nei log di controllo sono necessari Excel 2013 o versione successiva e [Power Query (http://www.microsoft.com/download/details.aspx?id=39379).

* È possibile anche importare i registri di controllo nel modello di Excel direttamente dall'account di Archiviazione di Azure usando Power Query. È quindi possibile esplorare i record di controllo e creare dashboard e report sui dati di log.

    ![Riquadro di spostamento][9]

## <a name="storage-key-regeneration"></a>Rigenerazione delle chiavi di archiviazione
Durante la produzione è probabile che periodicamente vengano aggiornate le chiavi di archiviazione. Quando si aggiornano le chiavi, è necessario salvare nuovamente il criterio di controllo. Il processo è il seguente:

1. Nel pannello delle impostazioni di archiviazione cambiare **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e fare clic su **OK** in fondo. Fare clic su **SALVA** nella parte superiore del pannello di configurazione del controllo.

    ![Riquadro di spostamento][6]
2. Passare al pannello di configurazione dell'archiviazione e **rigenerare** la *Chiave di accesso primaria*.

    ![Riquadro di spostamento][8]
3. Tornare al pannello di configurazione del controllo, cambiare la **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e fare clic su **OK** in fondo. Fare clic su **Salva** nella parte superiore del pannello di configurazione del controllo.
4. Tornare al pannello di configurazione e **rigenerare** il *Tasto di scelta secondario* (in preparazione al successivo ciclo di aggiornamento delle chiavi).


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

## <a name="next-steps"></a>Passaggi successivi

* Per configurare e gestire il controllo tramite PowerShell, vedere [Configurare il controllo del database con PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).
* Per una panoramica sul del controllo, vedere [Controllo del database](sql-database-auditing.md).


