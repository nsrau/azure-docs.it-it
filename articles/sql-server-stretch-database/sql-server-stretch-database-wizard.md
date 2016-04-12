<properties
	pageTitle="Introduzione all'esecuzione della procedura guidata Abilitare il database per l'estensione | Microsoft Azure"
	description="Scoprire come configurare un database per l'estensione database eseguendo la procedura guidata Abilitare il database per l'estensione."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Introduzione all'esecuzione della procedura guidata Abilitare il database per l'estensione

Per configurare un database per l'estensione database, eseguire la procedura guidata Abilitare il database per l'estensione. Questo argomento illustra le informazioni da immettere e le opzioni da selezionare nella procedura guidata.

Per saperne di più sull'estensione database, vedere l'articolo relativo all'[estensione database](sql-server-stretch-database-overview.md).

## Avviare la procedura guidata

1.  In SQL Server Management Studio, in Esplora oggetti, selezionare il database in cui si desidera abilitare l'estensione.

2.  Fare clic con il pulsante destro del mouse e selezionare **Attività**, quindi **Estensione** e **Abilita** per avviare la procedura guidata.

## <a name="Intro"></a>Introduzione
Esaminare lo scopo della procedura guidata e i prerequisiti.

![Pagina introduttiva della procedura guidata di estensione database][StretchWizardImage1]

## <a name="Tables"></a>Selezionare le tabelle
Selezionare le tabelle di cui si desidera abilitare l'estensione.

![Pagina di selezione tabelle della procedura guidata di estensione database][StretchWizardImage2]

|Colonna|Descrizione|
|----------|---------------|
|(nessun titolo)|Selezionare la casella di controllo in questa colonna per abilitare la tabella selezionata per l'estensione.|
|**Nome**|Consente di specificare il nome della colonna nella tabella.|
|(nessun titolo)|Un simbolo all'interno di questa colonna generalmente indica che non è possibile abilitare la tabella selezionata per l'estensione a causa di un problema di blocco. È possibile che la tabella usi un tipo di dati non supportato. Passare il puntatore del mouse sul simbolo per visualizzare altre informazioni in una descrizione comando. Per altre informazioni, vedere l'articolo relativo alle [limitazioni della superficie di attacco e problemi di blocco per l'estensione database](sql-server-stretch-database-limitations.md).|
|**Con estensione**|Indica se la tabella è già abilitata.|
|**prime righe**|Consente di specificare il numero di righe nella tabella.|
|**Dimensione (KB)**|Consente di specificare le dimensioni della tabella in KB.|
|**Migrazione**|In CTP 3.1, fino a RC2, è possibile eseguire la migrazione solo di un'intera tabella con la procedura guidata. Se si desidera specificare un predicato per selezionare le righe da una tabella contenente dati attuali e cronologici, eseguire l'istruzione ALTER TABLE per specificare un predicato dopo aver chiuso la procedura guidata. Per altre informazioni, vedere l'articolo relativo all'[abilitazione del database per l'estensione per una tabella](sql-server-stretch-database-enable-table.md) o [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|

## <a name="Configure"></a>Configurare la distribuzione di Azure

1.  Accedere a Microsoft Azure con un account Microsoft.

    ![Pagina di accesso ad Azure della procedura guidata di estensione database][StretchWizardImage3]

2.  Selezionare la sottoscrizione di Azure da usare per l'estensione database.

3.  Selezionare un'area di Azure. Se si crea un nuovo server, il server viene creato in questa area.

    Per ridurre al minimo la latenza, selezionare l'area di Azure in cui si trova SQL Server. Per altre informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

4.  Specificare se si desidera usare un server esistente o creare un nuovo server Azure.

    Se Active Directory in SQL Server è federata con Azure Active Directory, si può scegliere di usare un account del servizio federato per consentire a SQL Server di comunicare con il server Azure remoto. Per altre informazioni sui requisiti per questa opzione, vedere l'articolo relativo alle [opzioni ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

	-   **Creare un nuovo server**

        1.  Creare un account di accesso e una password per l'amministratore del server.

        2.  Si può scegliere di usare un account del servizio federato per SQL Server per comunicare con il server Azure remoto.

		![Pagina di creazione di un nuovo server di Azure della procedura guidata di estensione database][StretchWizardImage4]

    -   **Server esistente**

        1.  Selezionare o immettere il nome del server Azure esistente.

        2.  Selezionare il metodo di autenticazione.

            -   Se si seleziona **Autenticazione di SQL Server**, creare un nuovo account di accesso e una password.

            -   Selezionare **Autenticazione integrata di Active Directory** per usare un account del servizio federato e consentire a SQL Server di comunicare con il server Azure remoto.

		![Pagina di selezione di un server di Azure esistente della procedura guidata di estensione database][StretchWizardImage5]

## <a name="Credentials"></a>Credenziali protette
Immettere una password complessa per creare una chiave master del database o, se già presente, immettere la relativa password.

È necessario disporre di una chiave master del database per proteggere le credenziali usate da estensione database per la connessione al database remoto.

![Pagina delle credenziali di sicurezza della procedura guidata di estensione database][StretchWizardImage6]

Per altre informazioni sulla chiave master del database, vedere l'istruzione [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e l'articolo [Creazione della chiave master di un database](https://msdn.microsoft.com/library/aa337551.aspx). Per altre informazioni sulle credenziali create dalla procedura guidata, vedere l'istruzione [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selezionare l'indirizzo IP
Usare l'indirizzo IP pubblico di SQL Server o immettere un intervallo di indirizzi IP per creare una regola del firewall in Azure che consenta a SQL Server di comunicare con il server Azure remoto.

L'indirizzo o gli indirizzi IP forniti in questa pagina indicano al server Azure di consentire il passaggio di dati, query e operazioni di gestione in ingresso avviate da SQL Server attraverso il firewall di Azure. La procedura guidata non modifica le impostazioni del firewall in SQL Server.

![Pagina di selezione dell'indirizzo IP della procedura guidata di estensione database][StretchWizardImage7]

## <a name="Summary"></a>Riepilogo
Esaminare i valori immessi dall'utente e le opzioni selezionate nella procedura guidata. Selezionare quindi **Fine** per abilitare l'estensione.

![Pagina di riepilogo della procedura guidata di estensione database][StretchWizardImage8]

## <a name="Results"></a>Risultati
Esaminare i risultati.

Si può scegliere facoltativamente di selezionare **Monitoraggio** per avviare il monitoraggio dello stato della migrazione dei dati in Stretch Database Monitor. Per altre informazioni, vedere l'articolo [Monitoraggio e risoluzione dei problemi di migrazione dei dati (estensione database)](sql-server-stretch-database-monitor.md).

## <a name="KnownIssues"></a>Risoluzione dei problemi della procedura guidata
**La procedura guidata di estensione database non viene eseguita correttamente.** La procedura guidata non viene eseguita correttamente quando l'estensione database non è ancora abilitata a livello di server e non si hanno le necessarie autorizzazioni di amministratore di sistema. Chiedere all'amministratore di sistema di abilitare l'estensione database nell'istanza del server locale, quindi eseguire nuovamente la procedura guidata. Per altre informazioni, vedere la sezione [Prerequisito: Autorizzazioni per abilitare l'estensione database sul server](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Passaggi successivi
Abilitare tabelle aggiuntive per Database Estensione. Monitorare la migrazione dei dati e gestire tabelle e database con estensione abilitata.

-   [Abilitare l'estensione database per una tabella](sql-server-stretch-database-enable-table.md) per abilitare tabelle aggiuntive.

-   [Monitorare l'estensione database](sql-server-stretch-database-monitor.md) per visualizzare lo stato della migrazione dei dati.

-   [Sospendere e riprendere l'estensione database](sql-server-stretch-database-pause.md)

-   [Gestire e risolvere i problemi di estensione database](sql-server-stretch-database-manage.md)

-   [Eseguire backup e ripristino di database con estensione abilitata](sql-server-stretch-database-backup.md)

## Vedere anche

[Abilitare l'estensione database per un database](sql-server-stretch-database-enable-database.md)

[Abilitare l'estensione database per una tabella](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png

<!---HONumber=AcomDC_0406_2016-->