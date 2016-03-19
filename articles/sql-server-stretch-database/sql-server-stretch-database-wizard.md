<properties
	pageTitle="Eseguire la procedura guidata per abilitare l'estensione per il database | Microsoft Azure"
	description="Scoprire come configurare un database per Database Estensione eseguendo la procedura guidata Abilita estensione per il database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Eseguire la procedura guidata per abilitare l'estensione per il database

Per configurare un database per Database Estensione, eseguire la procedura guidata Abilita estensione per il database. Questo argomento illustra le informazioni da immettere e le opzioni da selezionare nella procedura guidata.

Per sapere di più su Database Estensione, vedere l'articolo relativo a [Database Estensione](sql-server-stretch-database-overview.md).

## Avviare la procedura guidata

1.  In SQL Server Management Studio, in Esplora oggetti, selezionare il database in cui si desidera abilitare l'estensione.

2.  Fare clic con il pulsante destro del mouse e selezionare **Attività**, quindi **Estensione** e **Abilita** per avviare la procedura guidata.

## <a name="Intro"></a>Introduzione
Esaminare lo scopo della procedura guidata e i prerequisiti.

## <a name="Tables"></a>Selezionare le tabelle
Selezionare le tabelle di cui si desidera abilitare l'estensione.

|Colonna|Descrizione|
|----------|---------------|
|(nessun titolo)|Selezionare la casella di controllo in questa colonna per abilitare la tabella selezionata per l'estensione.|
|**Nome**|Consente di specificare il nome della colonna nella tabella.|
|(nessun titolo)|Un simbolo all'interno di questa colonna generalmente indica che non è possibile abilitare la tabella selezionata per l'estensione a causa di un problema di blocco. È possibile che la tabella usi un tipo di dati non supportato. Passare il puntatore del mouse sul simbolo per visualizzare altre informazioni in una descrizione comando. Per sapere di più, vedere l'articolo sulle [limitazioni della superficie di attacco e i problemi di blocco per Database Estensione](sql-server-stretch-database-limitations.md).|
|**Con estensione**|Indica se la tabella è già abilitata.|
|**prime righe**|Consente di specificare il numero di righe nella tabella.|
|**Dimensione (KB)**|Consente di specificare le dimensioni della tabella in KB.|
|**Migrazione**|In CTP 3.1 tramite RC0, è possibile eseguire solo la migrazione dell'intera tabella con la procedura guidata. Se si desidera specificare un predicato per selezionare le righe da una tabella contenente dati attuali e cronologici, eseguire l'istruzione ALTER TABLE per specificare un predicato dopo aver chiuso la procedura guidata. Per altre informazioni, vedere l'articolo relativo all'[abilitazione di Database Estensione per una tabella](sql-server-stretch-database-enable-table.md) o [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|

## <a name="Configure"></a>Configurare la distribuzione di Azure

1.  Accedere a Microsoft Azure con un account Microsoft.

2.  Selezionare la sottoscrizione di Azure da usare per Database Estensione.

3.  Selezionare un'area di Azure. Se si crea un nuovo server, il server viene creato in questa area.

    Per ridurre al minimo la latenza, selezionare l'area di Azure in cui si trova SQL Server. Per altre informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

4.  Specificare se si desidera usare un server esistente o creare un nuovo server Azure.

    Se Active Directory in SQL Server è federata con Azure Active Directory, si può scegliere di usare un account del servizio federato per consentire a SQL Server di comunicare con il server Azure remoto. Per sapere di più sui requisiti per questa opzione, vedere l'articolo relativo alle [opzioni ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Server esistente**

        1.  Selezionare o immettere il nome del server Azure esistente.

        2.  Selezionare il metodo di autenticazione.

            -   Se si seleziona **Autenticazione di SQL Server**, creare un nuovo account di accesso e una password.

            -   Selezionare **Autenticazione integrata di Active Directory** per usare un account del servizio federato e consentire a SQL Server di comunicare con il server Azure remoto.

    -   **Creare un nuovo server**

        1.  Creare un account di accesso e una password per l'amministratore del server.

        2.  Si può scegliere di usare un account del servizio federato per SQL Server per comunicare con il server Azure remoto.

## <a name="Credentials"></a>Credenziali protette
Immettere una password complessa per creare una chiave master del database o, se già presente, immettere la relativa password.

È necessario disporre di una chiave master del database per proteggere le credenziali usate da Database Estensione per la connessione al database remoto.

Per altre informazioni sulla chiave master del database, vedere l'articolo su come [CREARE UNA CHIAVE MASTER (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e su come [creare una chiave master del database](https://msdn.microsoft.com/library/aa337551.aspx). Per sapere di più sulle credenziali create dalla procedura guidata, vedere l'articolo su come [CREARE CREDENZIALI CON AMBITO DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selezionare l'indirizzo IP
Usare l'indirizzo IP pubblico di SQL Server o immettere un intervallo di indirizzi IP per creare una regola del firewall in Azure che consenta a SQL Server di comunicare con il server Azure remoto.

## <a name="Summary"></a>Riepilogo
Esaminare i valori immessi dall'utente e le opzioni selezionate nella procedura guidata. Selezionare quindi **Fine** per abilitare l'estensione.

## <a name="Results"></a>Risultati
Esaminare i risultati.

Si può scegliere di selezionare **Monitoraggio** per avviare il monitoraggio dello stato della migrazione dei dati in Monitoraggio Database Estensione. Per altre informazioni, vedere l'articolo relativo a [monitoraggio e risoluzione dei problemi di migrazione dei dati (Database Estensione)](sql-server-stretch-database-monitor.md).

## <a name="KnownIssues"></a>Risoluzione dei problemi della procedura guidata
**La procedura guidata di Database Estensione non viene eseguita correttamente.** La procedura guidata non viene eseguita correttamente quando Database Estensione non è ancora abilitato a livello di server e non si dispone delle necessarie autorizzazioni di amministratore di sistema. Chiedere all'amministratore di sistema di abilitare Database Estensione nell'istanza del server locale, quindi eseguire nuovamente la procedura guidata. Per altre informazioni, leggere la sezione di articolo relativa al [prerequisito delle autorizzazioni per abilitare Database Estensione sul server](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Passaggi successivi
Abilitare tabelle aggiuntive per Database Estensione. Monitorare la migrazione dei dati e gestire tabelle e database con estensione abilitata.

-   [Abilitare Database Estensione per una tabella](sql-server-stretch-database-enable-table.md) per abilitare tabelle aggiuntive.

-   [Monitorare Database Estensione](sql-server-stretch-database-monitor.md) per visualizzare lo stato della migrazione dei dati.

-   [Sospendere e riprendere Database Estensione](sql-server-stretch-database-pause.md)

-   [Gestire e risolvere i problemi di Database Estensione](sql-server-stretch-database-manage.md)

-   [Eseguire backup e ripristino di database con estensione abilitata](sql-server-stretch-database-backup.md)

## Vedere anche
Gli articoli relativi ad [abilitazione di Database Estensione per un database](sql-server-stretch-database-enable-database.md) e [abilitazione di Database Estensione per una tabella](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0302_2016-->