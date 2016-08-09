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
	ms.date="06/27/2016"
	ms.author="douglasl"/>

# Introduzione all'esecuzione della procedura guidata Abilitare il database per l'estensione

Per configurare un database per l'estensione database, eseguire la procedura guidata Abilitare il database per l'estensione. Questo argomento illustra le informazioni da immettere e le opzioni da selezionare nella procedura guidata.

Per saperne di più sull'estensione database, vedere l'articolo relativo all'[estensione database](sql-server-stretch-database-overview.md).

## Avviare la procedura guidata

1.  In SQL Server Management Studio, in Esplora oggetti, selezionare il database in cui si desidera abilitare l'estensione.

2.  Fare clic con il pulsante destro del mouse e selezionare **Attività**, quindi **Estensione** e **Abilita** per avviare la procedura guidata.

## <a name="Intro"></a>Introduzione
Esaminare lo scopo della procedura guidata e i prerequisiti.

I prerequisiti importanti includono i seguenti:

-   È necessario essere amministratori per modificare le impostazioni del database.
-   È necessario avere una sottoscrizione di Microsoft Azure.
-   SQL Server deve poter comunicare con il server Azure remoto.

![Pagina introduttiva della procedura guidata di estensione database][StretchWizardImage1]

## <a name="Tables"></a>Selezionare le tabelle
Selezionare le tabelle di cui si desidera abilitare l'estensione.

Le tabelle con molte righe vengono visualizzate all'inizio dell'elenco ordinato. La procedura guidata, prima di visualizzare l'elenco di tabelle, le analizza per cercare i tipi di dati attualmente non supportati da Estensione database.

![Pagina di selezione tabelle della procedura guidata di estensione database][StretchWizardImage2]

|Colonna|Descrizione|
|----------|---------------|
|(nessun titolo)|Selezionare la casella di controllo in questa colonna per abilitare la tabella selezionata per l'estensione.|
|**Nome**|Consente di specificare il nome della colonna nella tabella.|
|(nessun titolo)|Un simbolo in questa colonna può rappresentare un avviso che non impedisce di abilitare la tabella selezionata per l'estensione. Può anche rappresentare un problema di blocco che impedisce di abilitare la tabella selezionata per l'estensione, ad esempio perché la tabella usa un tipo di dati non supportato. Passare il puntatore del mouse sul simbolo per visualizzare altre informazioni in una descrizione comando. Per altre informazioni, vedere [Limitazioni di Estensione database](sql-server-stretch-database-limitations.md).|
|**Con estensione**|Indica se la tabella è già abilitata per l'estensione.|
|**Migrazione**|È possibile eseguire la migrazione di un'intera tabella, usando l'opzione **Intera tabella**, o specificare un filtro in una colonna esistente della tabella. Per usare una funzione di filtro diversa per selezionare le righe per la migrazione, eseguire l'istruzione ALTER TABLE per specificare la funzione di filtro dopo essere usciti dalla procedura guidata. Per altre informazioni sulla funzione di filtro, vedere [Usare una funzione di filtro per selezionare righe di cui eseguire la migrazione](sql-server-stretch-database-predicate-function.md). Per altre informazioni su come applicare la funzione, vedere [Abilitare l'estensione database per una tabella](sql-server-stretch-database-enable-table.md) o [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**prime righe**|Consente di specificare il numero di righe nella tabella.|
|**Dimensione (KB)**|Consente di specificare le dimensioni della tabella in KB.|

## <a name="Filter"></a>Fornire facoltativamente un filtro di riga

Per fornire una funzione di filtro per selezionare le righe per la migrazione, eseguire queste operazioni nella pagina **Seleziona tabelle**.

1.  Nell'elenco **Selezionare le tabelle da estendere** fare clic su **Intera tabella** nella riga per la tabella. Viene aperta la finestra di dialogo **Seleziona righe da estendere**.

    ![Definire una funzione di filtro][StretchWizardImage2a]

2.  Nella finestra di dialogo **Seleziona righe da estendere** selezionare **Scegli righe**.

3.  Nel campo **Nome** immettere un nome per la funzione di filtro.

4.  Per la clausola **Where** selezionare una colonna dalla tabella, selezionare un operatore e specificare un valore.

5. Fare clic su **Controlla** per verificare la funzione. Se la funzione restituisce risultati dalla tabella, ovvero se sono presenti righe per la migrazione che soddisfano la condizione, il test indica **Esito positivo**.

    >   [AZURE.NOTE] La casella di testo che visualizza la query del filtro è di sola lettura. È possibile modificare la query nella casella di testo.

6.  Fare clic su Operazione completata per tornare alla pagina **Selezione tabelle**.

La funzione di filtro viene creata in SQL Server solo al termine della procedura guidata. Fino ad allora, è possibile tornare alla pagina **Selezione tabelle** per modificare o rinominare la funzione di filtro.

![Pagina Seleziona tabelle dopo avere definito una funzione di filtro][StretchWizardImage2b]

Per usare un tipo di funzione di filtro diversa per selezionare le righe per la migrazione, completare una delle operazioni seguenti.

-   Chiudere la procedura guidata ed eseguire l'istruzione ALTER TABLE per abilitare l'estensione per la tabella e specificare una funzione di filtro. Per altre informazioni, vedere [Abilitare l'estensione database per una tabella](sql-server-stretch-database-enable-table.md).

-   Eseguire l'istruzione ALTER TABLE per specificare una funzione di filtro dopo aver chiuso la procedura guidata. Per i passaggi necessari, vedere [Add a filter function after running the Wizard](sql-server-stretch-database-predicate-function.md#addafterwiz) (Aggiungere una funzione di filtro dopo l'esecuzione della procedura guidata).

## <a name="Configure"></a>Configurare la distribuzione di Azure

1.  Accedere a Microsoft Azure con un account Microsoft.

    ![Pagina di accesso ad Azure della procedura guidata di estensione database][StretchWizardImage3]

2.  Selezionare la sottoscrizione di Azure esistente da usare per l'estensione database.

3.  Selezionare un'area di Azure.
    -   Se si crea un nuovo server, il server viene creato in questa area.
    -   Se esistono server nell'area selezionata, la procedura guidata li elenca quando si sceglie **Existing server**.

    Per ridurre al minimo la latenza, selezionare l'area di Azure in cui si trova SQL Server. Per altre informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

4.  Specificare se si desidera usare un server esistente o creare un nuovo server Azure.

    Se Active Directory in SQL Server è federata con Azure Active Directory, si può scegliere di usare un account del servizio federato per consentire a SQL Server di comunicare con il server Azure remoto. Per altre informazioni sui requisiti per questa opzione, vedere [Opzioni ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

	-   **Creare un nuovo server**

        1.  Creare un account di accesso e una password per l'amministratore del server.

        2.  Si può scegliere di usare un account del servizio federato per SQL Server per comunicare con il server Azure remoto.

		![Pagina di creazione di un nuovo server di Azure della procedura guidata di estensione database][StretchWizardImage4]

    -   **Server esistente**

        1.  Selezionare il server Azure esistente.

        2.  Selezionare il metodo di autenticazione.

            -   Se si seleziona **Autenticazione di SQL Server**, specificare l'account di accesso amministratore e la password.

            -   Selezionare **Autenticazione integrata di Active Directory** per usare un account del servizio federato e consentire a SQL Server di comunicare con il server Azure remoto. Se il server selezionato non è integrato con Azure Active Directory, questa opzione non viene visualizzata.

		![Pagina di selezione di un server di Azure esistente della procedura guidata di estensione database][StretchWizardImage5]

## <a name="Credentials"></a>Proteggere le credenziali
È necessario disporre di una chiave master del database per proteggere le credenziali usate da estensione database per la connessione al database remoto.

Se esiste già una chiave master del database, immettere la password.

![Pagina delle credenziali di sicurezza della procedura guidata di estensione database][StretchWizardImage6b]

Se per il database non esiste una chiave master, immettere una password complessa per creare una chiave master del database.

![Pagina delle credenziali di sicurezza della procedura guidata di estensione database][StretchWizardImage6]

Per altre informazioni sulla chiave master del database, vedere l'istruzione [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) e l'articolo [Creare la chiave master di un database](https://msdn.microsoft.com/library/aa337551.aspx). Per altre informazioni sulle credenziali create dalla procedura guidata, vedere l'istruzione [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selezionare l'indirizzo IP
Usare l'intervallo di indirizzi IP della subnet (consigliato) o l'indirizzo IP pubblico di SQL Server per creare una regola del firewall in Azure che consenta a SQL Server di comunicare con il server Azure remoto.

L'indirizzo o gli indirizzi IP forniti in questa pagina indicano al server Azure di consentire il passaggio di dati, query e operazioni di gestione in ingresso avviate da SQL Server attraverso il firewall di Azure. La procedura guidata non modifica le impostazioni del firewall in SQL Server.

![Pagina di selezione dell'indirizzo IP della procedura guidata di estensione database][StretchWizardImage7]

## <a name="Summary"></a>Riepilogo
Esaminare i valori immessi dall'utente e le opzioni selezionate nella procedura guidata e i costi stimati in Azure. Selezionare quindi **Fine** per abilitare l'estensione.

![Pagina di riepilogo della procedura guidata di estensione database][StretchWizardImage8]

## <a name="Results"></a>Risultati
Esaminare i risultati.

Per monitorare lo stato della migrazione dei dati, vedere [Monitorare e risolvere i problemi di migrazione dei dati (estensione database)](sql-server-stretch-database-monitor.md).

![Pagina dei risultati della procedura guidata di estensione database][StretchWizardImage9]

## <a name="KnownIssues"></a>Risoluzione dei problemi della procedura guidata
**La procedura guidata di estensione database non viene eseguita correttamente.** La procedura guidata non viene eseguita correttamente quando l'estensione database non è ancora abilitata a livello di server e non si hanno le necessarie autorizzazioni di amministratore di sistema. Chiedere all'amministratore di sistema di abilitare l'estensione database nell'istanza del server locale, quindi eseguire nuovamente la procedura guidata. Per altre informazioni, vedere la sezione [Prerequisito: Autorizzazioni per abilitare Estensione database sul server](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Passaggi successivi
Abilitare tabelle aggiuntive per Database Estensione. Monitorare la migrazione dei dati e gestire tabelle e database con estensione abilitata.

-   [Abilitare Estensione database per una tabella](sql-server-stretch-database-enable-table.md) per abilitare tabelle aggiuntive.

-   [Monitorare e risolvere i problemi di migrazione dei dati](sql-server-stretch-database-monitor.md) per visualizzare lo stato della migrazione dei dati.

-   [Sospendere e riprendere Database Estensione](sql-server-stretch-database-pause.md)

-   [Gestire e risolvere i problemi di Database Estensione](sql-server-stretch-database-manage.md)

-   [Eseguire il backup di database con estensione abilitata](sql-server-stretch-database-backup.md)

## Vedere anche

[Abilitare l'estensione database per un database](sql-server-stretch-database-enable-database.md)

[Abilitare l'estensione database per una tabella](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png

<!---HONumber=AcomDC_0803_2016-->