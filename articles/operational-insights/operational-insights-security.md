<properties
	pageTitle="Sicurezza di Operational Insights"
	description="Informazioni su come Operational Insights garantisce la privacy degli utenti e ne protegge i dati."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="dev-center-name"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/07/2015"
	ms.author="banders"/>

# Sicurezza di Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft si impegna a proteggere la privacy e i dati degli utenti, mettendo a disposizione software e servizi che aiutino a gestire l'infrastruttura IT dell'organizzazione. Microsoft è a conoscenza della necessità di applicare rigorose misure di sicurezza quando si affidano i dati a terzi. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio.

La sicurezza e la protezione dei dati sono altamente prioritarie per Microsoft. Contattare Microsoft per qualsiasi domanda, suggerimento o problema riguardo a qualsiasi aspetto delle informazioni riportate in questo articolo, compresi i criteri di sicurezza. A questo scopo, visitare la pagina relativa alle [opzioni di supporto di Azure](http://azure.microsoft.com/support/options/).

Questo articolo illustra il modo in cui i dati vengono raccolti, elaborati e protetti in Microsoft Azure Operational Insights. È possibile usare degli agenti per connettersi direttamente al servizio Web oppure è possibile usare System Center Operations Manager per raccogliere i dati operativi per il servizio Operational Insights. I dati raccolti vengono inviati tramite Internet al servizio Operational Insights, che è ospitato in Microsoft Azure.

Il servizio Operational Insights gestisce i dati in modo sicuro usando i metodi seguenti:

**Separazione dei dati:** i dati dei clienti vengono tenuti separati logicamente in ogni componente del servizio Operational Insights. Tutti i dati vengono contrassegnati in base all'organizzazione. Tale contrassegno persiste per tutto il ciclo di vita dei dati e viene applicato a ogni livello del servizio.

Ogni cliente ha un BLOB di Azure dedicato che ospita i dati a lungo termine. Il BLOB viene crittografato con chiavi univoche per ogni cliente, che vengono modificate ogni 90 giorni.

**Conservazione dei dati:** le metriche aggregate per ciascuna delle soluzioni (in precedenza denominate Intelligence Pack) vengono archiviate in un database SQL ospitato in Microsoft Azure. Questi dati vengono archiviati per 390 giorni. I dati per la ricerca indicizzata vengono archiviati in media per 10 giorni prima di essere eliminati. Se il limite massimo di 20 milioni di record per ogni tipo di dati viene raggiunto prima, Operational Insights eseguirà la pulitura dei dati prima che siano trascorsi i 10 giorni. Se invece il limite non viene raggiunto entro 10 giorni, Operational Insights attende fino al raggiungimento di tale limite prima di procedere alla pulitura.

**Sicurezza fisica:** il servizio Operational Insights è gestito da personale Microsoft e tutte le attività vengono registrate e possono essere controllate. Il servizio Operational Insights viene eseguito interamente in Azure e soddisfa i criteri di progettazione comuni di Azure. È possibile verificare i dettagli sulla sicurezza fisica delle risorse di Azure a pagina 18 della [panoramica della sicurezza in Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf).

**Conformità e certificazioni:** il team dedicato allo sviluppo software e al servizio Operational Insights è impegnato attivamente nella collaborazione con il team legale di Microsoft, nonché con altri team preposti alla gestione della conformità e con altri partner di settore, per acquisire un'ampia gamma di certificazioni, tra cui ISO, prima che il servizio Operational Insights venga reso disponibile a livello generale.

Attualmente, il servizio è già conforme ai seguenti standard di sicurezza:

- Criteri di progettazione comuni di Windows
- Certificazione Microsoft Trustworthy Computing


## Sicurezza del flusso di dati
Il diagramma seguente mostra il flusso di informazioni in uscita dall'azienda, come tale flusso viene protetto durante il percorso verso il servizio Operational Insights e infine come viene visualizzato in Operational Insights. In diagramma riporta anche informazioni aggiuntive su ciascun passaggio.

![Immagine delle operazioni di raccolta dati e protezione in Operational Insights](./media/operational-insights-security/security.png)

### 1. Iscrizione a Operational Insights e raccolta dei dati

Per poter consentire all'organizzazione di inviare dati al servizio Operational Insights, è necessario configurare gli agenti di Microsoft Monitoring durante la connessione diretta al servizio Web oppure usare una procedura guidata di configurazione nella console operatore di Operations Manager. Gli utenti (che possono essere singoli utenti o gruppi di utenti) devono creare uno o più account di Operational Insights e registrare ogni agente connesso direttamente oppure l'ambiente di Operations Manager in uso tramite uno dei seguenti account:


- [ID aziendale](../sign-up-organization.md)

- [Account Microsoft - Outlook, Office Live, MSN](../sign-up-organization.md)

Un account di Operational Insights viene usato per raccogliere, aggregare, analizzare e presentare i dati. Gli account di Operational Insights sono univoci e vengono usati principalmente per partizionare i dati. Si possono ad esempio gestire i dati di produzione con un account di Operational Insights e i dati di test con un altro account. Gli account vengono inoltre usati da un amministratore per controllare gli accessi utente ai dati. A ogni account di Operational Insights possono essere associati più account utente, ognuno dei quali può disporre di più account di Operational Insights.

Quando la procedura guidata di configurazione è completata, ogni gruppo di gestione di Operations Manager stabilisce una connessione con il servizio Operational Insights. Viene quindi usata la procedura guidata di aggiunta computer per scegliere i computer del gruppo di gestione autorizzati a inviare dati al servizio.

Entrambi i tipi di agenti raccolgono dati per Operational Insights. Il tipo di dati raccolti dipende dai tipi di soluzioni usati. Una soluzione è costituita da un bundle di visualizzazioni, query di ricerca, regole di raccolta dati e logica di elaborazione predefinite. Soltanto gli amministratori di Operational Insights possono usare tale servizio per importare una soluzione. Dopo l'importazione, la soluzione viene spostata nei server di gestione di Operations Manager (se usati) e quindi negli agenti di Operations Manager scelti. Gli agenti raccoglieranno quindi i dati.

La tabella seguente elenca le soluzioni disponibili in Operational Insights e i tipi di dati raccolti da tali soluzioni.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Soluzione</b></td>
		<td><b>Tipi di dati</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Configuration Assessment</td>
		<td>Dati di configurazione, metadati e dati di stato</td>
    </tr>
    <tr align="left" valign="top">
		<td>Capacity Planning</td>
		<td>Dati sulle prestazioni, metadati e dati di stato</td>
    </tr>
	<tr align="left" valign="top">
		<td>Antimalware</td>
		<td>Dati di configurazione, metadati e dati di stato</td>
    </tr>
	    <tr align="left" valign="top">
		<td>System Update Assessment</td>
		<td>Metadati e dati di stato</td>
    </tr>
    <tr align="left" valign="top">
		<td>Log Management</td>
		<td>Log di eventi definiti dall'utente</td>
    </tr>
    <tr align="left" valign="top">
		<td>Change Tracking</td>
		<td>Inventario software e metadati dei servizi di Windows</td>
    </tr>
    <tr align="left" valign="top">
		<td>SQL and Active Directory Assessment</td>
		<td>Dati WMI, dati del Registro di sistema, dati sulle prestazioni e risultati delle visualizzazioni a gestione dinamica di SQL Server</td>
    </tr>
    </tbody>
    </table>


La tabella seguente mostra esempi di tipi di dati:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Tipo di dati</b></td>
		<td><b>Campi </b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Avviso</td>
		<td>Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount</td>
    </tr>
    <tr align="left" valign="top">
		<td>Configurazione</td>
		<td>CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate</td>
    </tr>
	<tr align="left" valign="top">
		<td>Evento</td>
		<td>EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded<p><b>Nota:</b> Operational Insights raccoglie anche gli eventuali eventi registrati con campi personalizzati nel registro eventi di Windows. </td>
    </tr>
	    <tr align="left" valign="top">
		<td>Metadati</td>
		<td>BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime</td>
    </tr>
    <tr align="left" valign="top">
		<td>Prestazioni</td>
		<td>ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded</td>
    </tr>
    <tr align="left" valign="top">
		<td>Stato</td>
		<td>StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified</td>
    </tr>
    </tbody>
    </table>


### 2. Invio dei dati dagli agenti

Se si usano agenti che si connettono direttamente al servizio Web, questi vengono registrati con una chiave e viene stabilita una connessione sicura tra l'agente e il servizio Operational Insights usando la porta 443.

Se si usa Operations Manager, viene invece registrato un account con il servizio Operational Insights e viene stabilita una connessione HTTPS sicura tra il server di gestione di Operations Manager e il servizio Operational Insights usando la porta 443. Se Operations Manager non è in grado di comunicare con il servizio per un qualsiasi motivo, i dati raccolti vengono memorizzati in una cache temporanea e il server di gestione proverà a mandarli nuovamente ogni 8 minuti per 2 ore. I dati raccolti vengono compressi e inviati al servizio Operational Insights ignorando i database locali, che in questo modo non vengono sovraccaricati. Dopo l'invio, i dati raccolti vengono rimossi dalla cache.

### 3. Ricezione ed elaborazione dei dati nel servizio Operational Insights

Per garantire che i dati in arrivo provengano da un'origine attendibile, il servizio Operational Insights convalida i certificati e l'integrità dei dati. I dati non elaborati vengono quindi archiviati come BLOB in [Archiviazione di Microsoft Azure](http://azure.microsoft.com/documentation/services/storage/). Ogni utente di Operational Insights dispone di un BLOB di Azure dedicato, accessibile solo a tale utente. Il tipo dei dati archiviati dipende dai tipi di soluzioni importati e usati per raccogliere i dati.

Il servizio Operational Insights elabora i dati non elaborati. I dati elaborati aggregati vengono quindi archiviati in un database SQL. Le comunicazioni tra il servizio Operational Insights e il database SQL si basano sull'autenticazione di quest'ultimo.

### 4. Uso di Operational Insights per l'accesso ai dati

Per accedere a Operational Insights è possibile usare l'account creato in precedenza. Tutto il traffico tra Operational Insights e il servizio Operational Insights viene inviato tramite un canale HTTPS sicuro.
 

<!---HONumber=July15_HO1-->