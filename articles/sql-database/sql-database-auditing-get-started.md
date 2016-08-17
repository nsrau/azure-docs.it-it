<properties
	pageTitle="Introduzione al controllo del database SQL | Microsoft Azure"
	description="Introduzione al controllo del database SQL"
	services="sql-database"
	documentationCenter=""
	authors="ronitr"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/10/2016"
	ms.author="CarlRabeler; ronitr"/>

# Introduzione al controllo del database SQL
Il servizio di controllo del database SQL di Azure tiene traccia degli eventi di database e scrive gli eventi controllati in un log di controllo nell'account di archiviazione di Azure. Il servizio di controllo in genere è disponibile per i livelli di servizio Basic, Standard e Premium.

Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Gli strumenti di controllo abilitano e facilitano il rispetto degli standard di conformità, ma non garantiscono la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il [Centro protezione Azure](https://azure.microsoft.com/support/trust-center/compliance/).

+ [Nozioni di base sul controllo del database SQL di Azure]
+ [Configurare il controllo per il database]
+ [Analizzare i log di controllo e i report]

##<a id="subheading-1"></a>Nozioni di base sul controllo del database SQL di Azure

Nella sezione seguente è descritta la configurazione del controllo utilizzando il portale di Azure. È anche possibile [configurare il controllo per il database con il portale di Azure classico].

Il controllo del database SQL consente di:

- **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
- **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l’attività e la segnalazione di eventi.
- **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

> [AZURE.NOTE] È possibile ricevere avvisi proattivi sulle attività del database anomale che possono indicare potenziali minacce alla protezione utilizzando la nuova funzionalità**Threat Detection**, ora in anteprima. Threat Detection può essere attivato e configurate all'interno del pannello di configurazione del controllo. Vedere l'[introduzione a Threat Detection](sql-database-threat-detection-get-started.md) per altri dettagli.

È possibile configurare il controllo per le seguenti categorie di eventi:

**SQL normale** e **SQL con parametri** per cui i log di controllo raccolti sono classificati come

- **Accesso ai dati**
- **Modifiche dello schema (DDL)**
- **Modifiche dei dati (DML)**
- **Account, ruoli e autorizzazioni (DCL)**
- **Stored procedure**, **accesso** e **gestione delle transazioni**.

Per ogni categoria di eventi, il controllo delle operazioni **Riuscite** e **Non riuscite** viene configurato separatamente.

Per altre informazioni sulle attività e sugli eventi controllati, vedere il [riferimento sul formato dei log di controllo (download di file doc)](http://go.microsoft.com/fwlink/?LinkId=506733).

I log di controllo vengono archiviati nell'account di archiviazione di Azure. È possibile definire un periodo di memorizzazione dei log di controllo, dopo il quale verranno eliminati i log precedenti.

Un criterio di controllo può essere definito per un database specifico o come criterio server predefinito. Un criterio di controllo del server predefinito verrà applicato a tutti i database di un server che non dispone di un criterio di controllo di override del database.

Prima di impostare il controllo verificare che si stia utilizzando un ["Client di livello inferiore"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md). Se si dispone di impostazioni del firewall restrittive, tenere presente che l'[endpoint dell'IP del database verrà modificato](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) quando si abilita il Controllo.


##<a id="subheading-2"></a>Configurare il controllo per il database

1. Avviare il [portale di Azure](https://portal.azure.com) in https://portal.azure.com. In alternativa, è anche possibile avviare il [portale di Azure classico](https://manage.windowsazure.com/) all'indirizzo https://manage.windowsazure.com/. Fare riferimento ai dettagli riportati di seguito.

2. Passare al pannello di configurazione del database SQL / SQL Server che si vuole controllare. Nel pannello Impostazioni selezionare **Controllo e rilevamento delle minacce**.

	![Riquadro di spostamento][1]

3. Nel pannello di configurazione del controllo, attivare il controllo impostandolo su**ON**.

4. Selezionare i **Dettagli di archiviazione** per aprire il pannello di archiviazione dei log di controllo. Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e il periodo di conservazione. **Suggerimento:** per sfruttare al massimo i modelli di report preconfigurati, usare lo stesso account di archiviazione per tutti i database controllati.

	![Riquadro di spostamento][2]

5. Fare clic su **Eventi controllati** per personalizzare gli eventi da controllare. Nel pannello **Registrazione per evento**, fare clic su **Operazione riuscita** e **Operazione non riuscita** per registrare tutti gli eventi oppure scegliere singole categorie di eventi.


6. È possibile spuntare la casella di controllo **Impostazioni di controllo ereditato dal server** per indicare che il database sarà controllato in base alle impostazioni del proprio server. Una volta selezionata questa opzione, verrà visualizzato un collegamento che consente di visualizzare o modificare le impostazioni di controllo del server da questo contesto.

	![Riquadro di spostamento][3]

7. Dopo aver configurato le impostazioni di controllo, è possibile attivare Threat Detection impostandolo su **ON** e configurare gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. Vedere la pagina[Introduzione a Threat Detection](sql-database-threat-detection-get-started.md) per altri dettagli.

8. Fare clic su **Save**.



##<a id="subheading-3"></a>Analizzare i log di controllo e i report

I log di controllo vengono aggregati in una raccolta di tabelle di archiviazione con il prefisso **SQLDBAuditLogs** nell'account di archiviazione di Azure scelto durante l'installazione. È possibile visualizzare i file di log con uno strumento come [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/).

È possibile scaricare un modello di rapporto preconfigurato in formato [foglio di calcolo di Excel scaricabile](http://go.microsoft.com/fwlink/?LinkId=403540), che consente di analizzare rapidamente i dati di log. Per usare il modello nei log di controllo sono necessari Excel 2013 o versione successiva e Power Query, disponibile per il download [qui](http://www.microsoft.com/download/details.aspx?id=39379).

È possibile importare i registri di controllo nel modello di Excel direttamente dall'account di archiviazione di Azure utilizzando Power Query. È quindi possibile esplorare i record di controllo e creare dashboard e report sui dati di log.


![Riquadro di navigazione][4]


##<a id="subheading-4"></a>Configurare il controllo per il database con il portale di Azure classico

1. Avviare il [portale di Azure classico](https://manage.windowsazure.com/) all'indirizzo https://manage.windowsazure.com/.

2. Fare clic sul Database SQL/SQL Server che si desidera controllare e quindi selezionare la scheda **CONTROLLO E SICUREZZA**.

3. Impostare il controllo su **ABILITATO**.

	![Riquadro di spostamento][5]

4. Modificare **REGISTRAZIONE PER EVENTO** in base alle esigenze, per personalizzare gli eventi da controllare.

	![Riquadro di spostamento][6]

5. Selezionare un **ACCOUNT DI ARCHIVIAZIONE** e configurare il periodo di **CONSERVAZIONE**.

	![Riquadro di spostamento][7]

6. Fare clic su **SAVE**.




##<a id="subheading-5">Procedure per l'uso in produzione</a>
La descrizione in questa sezione fa riferimento alle schermate precedenti. È possibile usare il [portale di Azure](https://portal.azure.com) o il [portale di Azure classico](https://manage.windowsazure.com/).


##<a id="subheading-6"></a>Rigenerazione delle chiavi di archiviazione

Durante la produzione è probabile che si aggiornino periodicamente le chiavi di archiviazione. Quando si aggiornano le chiavi, è necessario salvare nuovamente il criterio di controllo. Il processo è il seguente:


1. Nel pannello di configurazione di controllo, cambiare **Chiave di accesso alle risorse di archiviazione**da *Primaria* a *Secondaria* e premere **SALVA**.

	![][8]

2. Passare al pannello di configurazione di archiviazione e **rigenerare** la *Chiave di accesso primaria*.

3. Tornare al pannello di configurazione di controllo, cambiare **Chiave di accesso alle risorse di archiviazione** da *Secondaria* a *Primaria* e premere **SALVA**.

4. Tornare all'interfaccia utente di archiviazione e **rigenerare** il *Tasto di scelta secondario* (in preparazione al successivo ciclo di aggiornamento delle chiavi).

##<a id="subheading-7"></a>Automazione
Sono disponibili numerosi cmdlet di PowerShell che è possibile usare per configurare il controllo nel database SQL di Azure.

- [Get-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603731.aspx)
- [Get-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619329.aspx)
- [Remove-AzureRMSqlDatabaseAuditing](https://msdn.microsoft.com/library/azure/mt603796.aspx)
- [Remove-AzureRMSqlServerAuditing](https://msdn.microsoft.com/library/azure/mt603574.aspx)
- [Set-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603531.aspx)
- [Set-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603794.aspx)
- [Use-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619353.aspx)




<!--Anchors-->
[Nozioni di base sul controllo del database SQL di Azure]: #subheading-1
[Configurare il controllo per il database]: #subheading-2
[Analizzare i log di controllo e i report]: #subheading-3
[configurare il controllo per il database con il portale di Azure classico]: #subheading-4
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_storage_account.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_inherit_from_server.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_report_template.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_classic_portal_enable.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_classic_portal_events.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_classic_portal_storage.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_storage_key_rotation.png

<!---HONumber=AcomDC_0803_2016-->