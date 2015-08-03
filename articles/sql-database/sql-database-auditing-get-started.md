<properties 
	pageTitle="Introduzione al controllo del database SQL | Azure" 
	description="Introduzione al controllo del database SQL" 
	services="sql-database" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="jeffreyg"/>
 
# Introduzione al controllo del database SQL 
<p> Il servizio di controllo del database SQL di Azure tiene traccia degli eventi di database e scrive gli eventi controllati in un log di controllo nell'account di archiviazione di Azure. Il servizio di controllo in genere è disponibile per i livelli di servizio Basic, Standard e Premium.

Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Gli strumenti di controllo abilitano e facilitano il rispetto degli standard di conformità, ma non garantiscono la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro protezione Azure</a>.

+ [Nozioni di base sul controllo del database SQL di Azure] 
+ [Configurare il controllo per il database]
+ [Analizzare i log di controllo e i report]

##<a id="subheading-1"></a>Nozioni di base sul controllo del database SQL di Azure

Nelle sezioni seguenti è descritta la configurazione del controllo utilizzando il portale di anteprima di Azure. È inoltre possibile [configurare il controllo per il database con il portale di Azure classico].

Il controllo del database SQL consente di:

- **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
- **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l’attività e la segnalazione di eventi.
- **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

È possibile configurare il controllo per le seguenti categorie di eventi:

**SQL normale** e **SQL con parametri** per cui i log di controllo raccolti sono classificati come

- **Accesso ai dati**
- **Modifiche dello schema (DDL)**
- **Modifiche dei dati (DML)**
- **Account, ruoli e autorizzazioni (DCL)**

**Stored procedure**, **accesso** e **gestione delle transazioni**.

Per ogni categoria di eventi, il controllo delle operazioni **riuscite** e **non riuscite** viene configurato separatamente.

Per altre informazioni sulle attività e sugli eventi controllati, vedere il <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">riferimento sul formato dei log di controllo (download di file doc)</a>.

I log di controllo vengono archiviati nell'account di archiviazione di Azure. È possibile definire un periodo di conservazione del log di controllo.

Un criterio di controllo può essere definito per un database specifico o come criterio server predefinito. Un criterio di controllo del server predefinito verrà applicato a tutti i database di un server che non dispone di un criterio di controllo di override del database.

Prima di impostare il controllo verificare che si stia utilizzando un ["Client di livello inferiore"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


##<a id="subheading-2"></a>Configurare il controllo per il database

1. Avviare il <a href="https://portal.azure.com" target="_blank">portale di anteprima di Azure</a> all’indirizzo https://portal.azure.com. In alternativa, è anche possibile avviare il <a href= "https://manage.windowsazure.com/" target="_bank">portale di Azure classico</a> all'indirizzo https://manage.windowsazure.com/. Fare riferimento ai dettagli riportati di seguito.

2. Passare al pannello di configurazione del database SQL / SQL Server che si vuole controllare. Fare clic sul pulsante **Impostazioni** nella parte superiore e quindi nel pannello Impostazioni e selezionare **Controllo**.

	![][1]

3. Nel pannello di configurazione del controllo, selezionare i dettagli di archiviazione per aprire il pannello di archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e il periodo di conservazione. **Suggerimento:** per sfruttare al massimo i modelli di report preconfigurati, usare lo stesso account di archiviazione per tutti i database controllati.

	![][2]

4. In **REGISTRAZIONE PER EVENTO**, fare clic su **OPERAZIONE RIUSCITA** e **OPERAZIONE NON RIUSCITA** per registrare tutti gli eventi oppure scegliere singole categorie di eventi.


5. Se si sta configurando il controllo per un database SQL, fare clic su **Per applicare il controllo fare clic qui...** e in **ACCESSO CON SICUREZZA ABILITATA** selezionare **OBBLIGATORIO**. Se si sta configurando il controllo per un Server SQL sono disponibili due opzioni: (a) dopo il passaggio 6, passare a ciascun database SQL nel server e applicare questo passaggio oppure (2) [modificare il nome di dominio completo del server nella stringa di connessione](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


	![][5]

6. Fare clic su **OK**.



##<a id="subheading-3">Analizzare i log di controllo e i report</a>

I log di controllo vengono aggregati in una raccolta di tabelle di archiviazione con il prefisso **SQLDBAuditLogs** nell'account di archiviazione di Azure scelto durante l'installazione. È possibile visualizzare i file di log con uno strumento come <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Esplora archivi di Azure</a>.

È possibile scaricare un modello di report dashboard preconfigurato in formato <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">foglio di calcolo di Excel</a>, che consente di analizzare rapidamente i dati di log. Per usare il modello nei log di controllo sono necessari Excel 2013 o versione successiva e Power Query, disponibile per il download <a href="http://www.microsoft.com/download/details.aspx?id=39379">qui</a>.

Il modello contiene dati di esempio fittizi ed è possibile configurare Power Query per l'importazione diretta del log di controllo dall'account di archiviazione di Azure.

Per istruzioni dettagliate sull'uso del modello di report, scaricare il documento con la <a href="http://go.microsoft.com/fwlink/?LinkId=506731">procedura</a>.

![][6]


##<a id="subheading-4"></a>Configurare il controllo per il database con il portale di Azure classico

1. Avviare il <a href= "https://manage.windowsazure.com/" target="_bank">portale classico di Azure</a> all’indirizzo https://manage.windowsazure.com/.
 
2.   Fare clic sul Database SQL/SQL Server che si desidera controllare e quindi selezionare la scheda **CONTROLLO E SICUREZZA**.

3.   Se si sta configurando il controllo per un database SQL, fare clic su **ACCESSO CON SICUREZZA ABILITATA** selezionare **OBBLIGATORIO**. Se si sta configurando il controllo per un Server SQL sono disponibili due opzioni: (a) dopo il passaggio 7, passare a ciascun database SQL nel server e applicare questo passaggio oppure (2) [modificare il nome di dominio completo del server nella stringa di connessione](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).

4. Nella sezione controllo fare clic su **ABILITATO**.


	![][7]

5. Modificare l’opzione **REGISTRAZIONE PER EVENTO** in base alle esigenze.

	![][8]

6. Selezionare un **ACCOUNT DI ARCHIVIAZIONE** e configurare il periodo di **CONSERVAZIONE**.

	![][11]

7. Fare clic su **SAVE**.




##<a id="subheading-3">Procedure per l'uso in produzione</a>
La descrizione in questa sezione fa riferimento alle schermate precedenti. È possibile usare il <a href="https://portal.azure.com" target="_blank">portale di anteprima di Azure</a> o il <a href= "https://manage.windowsazure.com/" target="_bank">portale di Azure classico</a>.
 

##<a id="subheading-4"></a>Rigenerazione delle chiavi di archiviazione

Durante la produzione è probabile che si aggiornino periodicamente le chiavi di archiviazione. Quando si aggiornano le chiavi, è necessario salvare nuovamente il criterio. Il processo è il seguente:


1. Nel pannello di configurazione di controllo (descritto sopra nella sezione sulla configurazione del controllo) cambiare **Chiave di accesso alle risorse di archiviazione** da *Primaria* a *Secondaria* e fare clic su **SALVA**. ![][10]
2. Passare al pannello di configurazione di archiviazione e **rigenerare** la *Chiave di accesso primaria*.

3. Tornare al pannello di configurazione di controllo, cambiare **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e premere **SALVA**.

4. Tornare all'interfaccia utente di archiviazione e **rigenerare** la *Chiave di accesso secondaria* (in preparazione al successivo ciclo di aggiornamento delle chiavi).
  
##<a id="subheading-4"></a>Automazione
Sono disponibili numerosi cmdlet di PowerShell che è possibile usare per configurare il controllo nel database SQL di Azure. Per accedere ai cmdlet di controllo, è necessario che PowerShell sia in esecuzione in modalità Gestione risorse di Azure.

> [AZURE.NOTE]Il modulo [Gestione risorse di Azure](https://msdn.microsoft.com/library/dn654592.aspx) è al momento in anteprima. Potrebbe non fornire le stesse funzionalità di gestione del modulo di Azure.

Dopo aver avviato la modalità Gestione risorse di Azure, eseguire `Get-Command *AzureSql*` per visualizzare l'elenco dei cmdlet disponibili.







<!--Anchors-->
[Nozioni di base sul controllo del database SQL di Azure]: #subheading-1
[Configurare il controllo per il database]: #subheading-2
[Analizzare i log di controllo e i report]: #subheading-3
[configurare il controllo per il database con il portale di Azure classico]: #subheading-4


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
[2]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
[3]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
[5]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
[6]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
[7]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-enable.png
[8]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure.png
[9]: ./media/sql-database-auditing-get-started/sql-database-auditing-security-enabled-access.png
[10]: ./media/sql-database-auditing-get-started/sql-database-auditing-storage-account.png
[11]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure-storage.png






<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

 

<!---HONumber=July15_HO4-->