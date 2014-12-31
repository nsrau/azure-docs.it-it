<properties title="Get started with SQL database auditing" pageTitle="Introduzione al controllo del database SQL | Azure" description="Introduzione al controllo del database SQL" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId="" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/31/2015" ms.author="jeffreyg" />
 
# Introduzione al controllo del database SQL 
<p> Il servizio di controllo del database SQL di Azure tiene traccia degli eventi di database e scrive gli eventi controllati in un log di controllo nell'account di archiviazione di Azure. Il servizio di controllo è disponibile in anteprima per i livelli di servizio Basic, Standard e Premium.

Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. 

Gli strumenti di controllo abilitano e facilitano il rispetto degli standard di conformità, ma non garantiscono la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il <a href="http://azure.microsoft.com/it-it/support/trust-center/compliance/" target="_blank">Centro protezione Azure</a>.

+ [Nozioni di base sul controllo del database SQL di Azure] 
+ [Configurare il controllo per il database]
+ [Analizzare i log di controllo e i report]

##<a id="subheading-1">Nozioni di base sul controllo del database SQL di Azure</a>

Il controllo si configura nel portale di anteprima di Azure e non fa differenza se il database è stato creato usando il portale di Azure o il portale di anteprima di Azure. Il controllo del database SQL consente di:

- **Conservare** un audit trail di eventi selezionati. Definire categorie di azioni ed eventi di database da registrare.
- **Creare report** sulle attività del database. I report preconfigurati e il dashboard consentono di iniziare rapidamente a usare il monitoraggio degli eventi e delle attività.
- **Analizzare** i report. Individuare eventi sospetti, attività insolite e tendenze.

È possibile controllare le attività e gli eventi seguenti:

- **Accesso ai dati**
- **Modifiche dello schema (DDL)**
- **Modifiche dei dati (DML)**
- **Account, ruoli e autorizzazioni (DCL)**
- **Eccezioni di sicurezza**

Per altri dettagli sulle attività e gli eventi registrati, <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">scaricare il file di riferimento sul formato dei log di controllo</a>. 

È anche possibile scegliere l'account di archiviazione in cui salvare i log di controllo.

###Stringa di connessione con sicurezza abilitata
Quando si configura il controllo, Azure fornisce una stringa di connessione con sicurezza abilitata per il database. Verranno registrati solo gli eventi e le attività delle applicazioni client che usano questa stringa di connessione, quindi è necessario aggiornare le applicazioni client esistenti in modo che usino il nuovo formato di stringa.

Formato tradizionale della stringa di connessione: <*nome server*>.database.windows.net

Stringa di connessione con sicurezza abilitata: <*nome server*>.database.**secure**.windows.net


##<a id="subheading-2"></a>Configurare il controllo per il database

1. Avviare il <a href="https://portal.azure.com" target="_blank">portale di anteprima di Azure</a> all'indirizzo https://portal.azure.com. In alternativa, è anche possibile avviare il <a href= "https://manage.windowsazure.com/" target="_bank">portale di Azure classico</a> all'indirizzo https://manage.windowsazure.com/. Fare riferimento ai dettagli sotto.
2. Passare al pannello di configurazione del database che si vuole controllare. Scorrere verso il basso fino alla sezione **Operazioni** e quindi fare clic su **Controllo** per abilitare il controllo e avviare il pannello di configurazione del controllo.

	![][1]

3. Nel pannello di configurazione del controllo selezionare l'account di archiviazione di Azure in cui salvare i log. **Suggerimento:** per sfruttare al massimo i modelli di report preconfigurati, usare lo stesso account di archiviazione per tutti i database controllati.

	![][2]

4. In **Opzioni di controllo** fare clic su **Tutto** per registrare tutti gli eventi oppure scegliere singoli tipi di evento.

	![][3]

5. Selezionare **Salvare queste impostazioni del server come predefinite** per applicare le impostazioni a tutti i futuri database nel server e a qualsiasi database per cui non è stato ancora configurato il controllo. In seguito sarà possibile eseguire l'override delle impostazioni per qualsiasi database seguendo la stessa procedura. 

6. Fare clic su **Mostra stringhe di connessione database** e quindi copiare o prendere nota della stringa di connessione con sicurezza abilitata appropriata per l'applicazione. Usare questa stringa per qualsiasi applicazione client di cui si vogliono monitorare le attività.

	![][5]

7. Fare clic su **OK**.



##<a id="subheading-3">Analizzare i log di controllo e i report</a>

I log di controllo vengono aggregati in un'unica tabella di Azure Store denominata **AuditLogs** nell'account di archiviazione di Azure selezionato durante la configurazione. È possibile visualizzare i file di log con uno strumento come <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Storage Explorer</a>.

È possibile scaricare un modello di report dashboard preconfigurato in formato <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">foglio di calcolo di Excel</a>, che consente di analizzare rapidamente i dati di log. Per usare il modello nei log di controllo sono necessari Excel 2013 o versione successiva e Power Query, disponibile per il download <a href="http://www.microsoft.com/it-it/download/details.aspx?id=39379">qui</a>. 

Il modello contiene dati di esempio fittizi ed è possibile configurare Power Query per l'importazione diretta del log di controllo dall'account di archiviazione di Azure. 

Per istruzioni dettagliate sull'uso del modello di report, scaricare il <a href="http://go.microsoft.com/fwlink/?LinkId=506731">documento con la procedura</a>.

![][6]


##<a id="subheading-4"></a>Configurare il controllo per il database con il portale di Azure classico

1. Avviare il <a href= "https://manage.windowsazure.com/" target="_bank">portale di Azure classico</a> all'indirizzo https://manage.windowsazure.com/. 
2. Fare clic sul database che si vuole controllare e quindi sulla scheda **Anteprima controllo e sicurezza**.
3. Nella sezione controllo fare clic su "Abilita".

![][7]

4. Modificare **TIPO EVENTO** se necessario.

![][8]

5. Selezionare un **ACCOUNT DI ARCHIVIAZIONE**.
6. Fare clic su **SALVA**.
7. Fare clic su **Mostra stringa di connessione sicura** per la stringa di connessione.


##<a id="subheading-3">Procedure per l'uso in produzione</a>
La descrizione in questa sezione fa riferimento alle schermate precedenti. È possibile usare il <a href="https://portal.azure.com" target="_blank">portale di anteprima di Azure</a> o il <a href= "https://manage.windowsazure.com/" target="_bank">portale di Azure classico</a>.
 

##<a id="subheading-4"></a>Accesso con sicurezza abilitata

Durante la produzione è probabile che si renda necessario controllare tutto il traffico verso il database da tutte le applicazioni e gli strumenti. Modificare quindi **Accesso con sicurezza abilitata** da *Facoltativo* a *Obbligatorio* e salvare il criterio. Dopo aver configurato *Obbligatorio*, non è possibile accedere al database con la stringa di connessione originale, ma solo con la stringa di connessione con sicurezza abilitata.


![][9]


##<a id="subheading-4"></a>Rigenerazione delle chiavi di archiviazione

Durante la produzione è probabile che si aggiornino periodicamente le chiavi di archiviazione. Il servizio di controllo non mantiene le chiavi dell'account di archiviazione. Quando si salva, viene prodotta una chiave di firma di accesso condiviso di sola scrittura per la tabella di controllo. Solo il cliente può leggere i log di controllo. A tale scopo, quando si aggiornano le chiavi, è necessario salvare nuovamente il criterio. Il processo è il seguente:


1. Nel pannello di configurazione di controllo (descritto sopra nella sezione sulla configurazione del controllo) cambiare **Chiave di accesso alle risorse di archiviazione** da *Primaria* a *Secondaria* e fare clic su **SALVA**.
![][10]
2. Passare al pannello di configurazione di archiviazione e **rigenerare** la *Chiave di accesso primaria*.

3. Tornare al pannello di configurazione di controllo, cambiare **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e premere **SALVA**.

4. Tornare all'interfaccia utente di archiviazione e **rigenerare** la *Chiave di accesso secondaria* (in preparazione al successivo ciclo di aggiornamento delle chiavi).
  
##<a id="subheading-4"></a>Automazione
Per PowerShell fare riferimento a <a href="https://github.com/Azure/azure-powershell" target="_blank">PowerShell SDK</a>.

Per l'API REST fare riferimento al documento relativo all'<a href="http://download.microsoft.com/download/D/8/D/D8D90BA1-977F-466B-A839-7823FF37FD02/04-Azure SQL DB Auditing REST API.docx">API REST del database SQL di Azure</a>





<!--Anchors-->
[Nozioni di base sul controllo del database SQL di Azure] : #subheading-1
[Configurare il controllo per il database]: #subheading-2
[Analizzare i log di controllo e i report]: #subheading-3
[Configurare il controllo per il database con il portale di Azure classico]: #subheading-4


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






<!--Link references-->
[Collegamento 1 a un altro argomento della documentazione di azure.microsoft.com]: ../virtual-machines-windows-tutorial/
[Collegamento 2 a un altro argomento della documentazione di azure.microsoft.com]: ../web-sites-custom-domain-name/
[Collegamento 3 a un altro argomento della documentazione di azure.microsoft.com]: ../storage-whatis-account/


<!--HONumber=35_1-->
