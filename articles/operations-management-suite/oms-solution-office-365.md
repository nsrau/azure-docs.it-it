---
title: Soluzione Office 365 in Operations Management Suite (OMS) | Microsoft Docs
description: In questo articolo vengono fornite informazioni dettagliate sulla configurazione e l'uso della soluzione Office 365 in OMS.  Include una descrizione dettagliata dei record di Office 365 creati in Log Analytics.
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: 711071eaff7ab5e5199793663aa3cbb36a1e8d8a
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2017
---
# <a name="office-365-solution-in-operations-management-suite-oms"></a>Soluzione Office 365 in Operations Management Suite (OMS)

![Logo di Office 365](media/oms-solution-office-365/icon.png)

La soluzione Office 365 per Operations Management Suite (OMS) consente di monitorare l'ambiente Office 365 in Log Analytics.  

- Monitoraggio delle attività degli utenti negli account di Office 365 per analizzare i modelli di utilizzo nonché identificare le tendenze di comportamento. Ad esempio, è possibile estrarre scenari di uso specifici, ad esempio i file condivisi all'esterno dell'organizzazione o i siti di SharePoint più diffusi.
- Monitoraggio delle attività dell'amministratore per tenere traccia delle modifiche alla configurazione o le operazioni con privilegi elevati.
- Rilevamento e analisi del comportamento utente indesiderato, che può essere personalizzato per esigenze organizzative.
- Dimostrazione di conformità e controllo. Ad esempio, è possibile monitorare le operazioni di accesso nei file riservati, favorendo così il processo di conformità e controllo.
- Risoluzione dei problemi operativi usando la ricerca di OMS per i dati di attività di Office 365 dell'organizzazione.

## <a name="prerequisites"></a>Prerequisiti
Prima di installare e configurare la soluzione, è richiesto quanto segue.

- Sottoscrizione a Office 365 dell'organizzazione.
- Credenziali per un account utente che rappresenta un Amministratore globale.
- Per ricevere i dati di controllo, è necessario [configurare il controllo](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) nella sottoscrizione di Office 365.  Si noti che [controllo delle cassette postali](https://technet.microsoft.com/library/dn879651.aspx) viene configurato separatamente.  È comunque possibile installare la soluzione e raccogliere altri dati se il controllo non è configurato.
 


## <a name="management-packs"></a>Management Pack
Questa soluzione non installa alcun Management Pack nei gruppi di gestione connessi.
  

## <a name="configuration"></a>Configurazione
Dopo aver [aggiunto la soluzione Office 365 alla sottoscrizione](../log-analytics/log-analytics-add-solutions.md), è necessario connetterla alla sottoscrizione di Office 365.

1. Aggiungere la soluzione Alert Management all'area di lavoro di OMS usando la procedura descritta in [Aggiungere soluzioni](../log-analytics/log-analytics-add-solutions.md).
2. Passare a **Impostazioni** nel portale OMS.
3. In **Origini connesse**selezionare **Office 365**.
4. Fare clic su **Connetti Office 365**.<br>![Connetti Office 365](media/oms-solution-office-365/configure.png)
5. Accedere a Office 365 con un account di amministratore globale per la sottoscrizione. 
6. La sottoscrizione verrà elencata con i carichi di lavoro che verranno monitorati dalla soluzione.<br>![Connettere Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>Raccolta dei dati
### <a name="supported-agents"></a>Agenti supportati
La soluzione Office 365 non recupera i dati dagli [agenti OMS](../log-analytics/log-analytics-data-sources.md).  Recupera dati direttamente da Office 365.

### <a name="collection-frequency"></a>Frequenza della raccolta
Office 365 invia una [notifica webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) con dati dettagliati per Log Analytics ogni volta che viene creato un record.

## <a name="using-the-solution"></a>Uso della soluzione
Quando si aggiunge la soluzione Office 365 all'area di lavoro di OMS, il riquadro **Office 365** verrà aggiunto alla dashboard OMS. Il riquadro visualizza un conteggio e la rappresentazione grafica del numero di computer nell'ambiente con la relativa conformità degli aggiornamenti.<br><br>
![Riquadro di riepilogo di Office 365](media/oms-solution-office-365/tile.png)  

Fare clic sul riquadro **Office 365** per aprire la dashboard di **Office 365**.

![Dashboard di Office 365](media/oms-solution-office-365/dashboard.png)  

Il dashboard include le colonne nella tabella seguente. Ogni colonna elenca i primi dieci avvisi per numero corrispondente ai criteri della colonna per l'ambito e l'intervallo di tempo specificati. È possibile eseguire una ricerca di log che fornisce l'intero elenco facendo clic su Visualizza tutto nella parte inferiore della colonna o facendo clic sull'intestazione di colonna.

| Colonna | Descrizione |
|:--|:--|
| Operazioni | Fornisce informazioni sugli utenti attivi da tutte le sottoscrizioni Office 365 monitorate. Sarà inoltre possibile visualizzare il numero di attività che si verificano nel corso del tempo.
| Exchange | Mostra i dettagli delle attività di Exchange Server, ad esempio Add-Mailbox Permission o Set-Mailbox. |
| SharePoint | Mostra le prime attività che gli utenti eseguono nei documenti di SharePoint. Quando si visualizzano i dettagli da questo riquadro, nella pagina di ricerca vengono visualizzati i dettagli di queste attività, ad esempio il documento di destinazione e il percorso di questa attività. Ad esempio, per un evento di accesso al file sarà possibile visualizzare il documento a cui si accede, il nome account associato e l'indirizzo IP. |
| Azure Active Directory | Include le attività degli utenti superiori, ad esempio i tentativi di accesso e di reimpostazione password utente. È possibile visualizzare i dettagli di queste attività, ad esempio lo stato dei risultati. Questa funzionalità è particolarmente utile se si desidera monitorare le attività sospette in Azure Active Directory. |




## <a name="log-analytics-records"></a>Record di Log Analytics

Tutti i record creati nell'area di lavoro Log Analytics dalla soluzione Office 365 dispongono di un **tipo** di **OfficeActivity**.  La proprietà **OfficeWorkload** determina a quale servizio di Office 365 fa riferimento il record: Exchange, AzureActiveDirectory, SharePoint o OneDrive.  La proprietà **RecordType** specifica il tipo di operazione.  Le proprietà variano per ogni tipo di operazione e vengono visualizzate nelle tabelle seguenti.

### <a name="common-properties"></a>Proprietà comuni
Le proprietà seguenti sono comuni a tutti i record di Office 365.

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo | *OfficeActivity* |
| ClientIP | L'indirizzo IP del dispositivo usato quando l'attività è stata registrata. L'indirizzo IP viene visualizzato in formato di indirizzo IPv4 o IPv6. |
| OfficeWorkload | Servizio di Office 365 a cui il record fa riferimento.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operazione | Il nome dell'attività utente o l'attività dell'amministratore.  |
| OrganizationId | GUID per il tenant di Office 365 dell'organizzazione. Questo valore sarà sempre lo stesso per l'organizzazione, indipendentemente dal servizio Office 365 in cui si verifica. |
| RecordType | Tipo di operazione eseguita. |
| ResultStatus | Indica se l'azione (specificata nella proprietà Operation) è andata a buon fine o meno. I possibili valori sono Succeeded, PartiallySucceded o Failed. Per le attività dell'amministratore di Exchange, il valore è True o False. |
| UserId | Il nome UPN (User Principal Name) dell'utente che ha eseguito l'azione ha generato la registrazione del record, ad esempio my_name@my_domain_name. Si noti che sono inclusi anche i record per l'attività eseguita dall'account di sistema (ad esempio SHAREPOINT\system o NTAUTHORITY\SYSTEM). | 
| UserKey | Un ID alternativo per l'utente identificato con la proprietà UserId.  Ad esempio, questa proprietà viene popolata con l'ID univoco passport (PUID) per gli eventi eseguiti dagli utenti in SharePoint, OneDrive for Business ed Exchange. Questa proprietà può inoltre specificare lo stesso valore della proprietà UserID per gli eventi che si verificano in altri servizi ed eventi eseguiti dall'account di sistema|
| UserType | Il tipo di utente che ha eseguito l'operazione.<br><br>Admin<br>Applicazione<br>DcAdmin<br>Regolare <br>Riservato<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base di Azure Active Directory
Le proprietà seguenti sono comuni a tutti i record di Azure Active Directory.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Il tipo di evento di Azure AD. |
| ExtendedProperties | Le proprietà estese dell'evento di Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Accesso all'account di Azure Active Directory
Questi record vengono creati quando un utente di Active Directory tenta di accedere.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Applicazione | L'applicazione che attiva l'evento di accesso all'account, ad esempio Office 15. |
| Client | Dettagli relativi al dispositivo del client, al sistema operativo del dispositivo e al browser del dispositivo usato per l'evento di accesso all'account. |
| LoginStatus | Questa proprietà deriva direttamente da OrgIdLogon.LoginStatus. Il mapping di diversi errori di accesso interessanti potrebbe essere eseguito da algoritmi di avviso. |
| UserDomain | Le informazioni sull'identità del tenant (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Questi record vengono creati quando vengono apportate modifiche o aggiunte agli oggetti di Azure Active Directory.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | L'utente su cui è stata eseguita l'azione (identificato dalla proprietà Operation). |
| Attore | Utente o entità servizio che ha eseguito l'azione. |
| ActorContextId | Il GUID dell'organizzazione a cui appartiene l'attore. |
| ActorIpAddress | Indirizzo IP dell'attore in formato di indirizzo IPv4 o IPv6. |
| InterSystemsId | GUID che rileva le azioni nei vari componenti all'interno del servizio di Office 365. |
| IntraSystemId |   Il GUID generato da Azure Active Directory per tenere traccia dell'azione. |
| SupportTicketId | L'ID del ticket di supporto cliente per l'azione in situazioni in cui si agisce per conto di qualcuno. |
| TargetContextId | Il GUID dell'organizzazione a cui appartiene l'utente di destinazione. |


### <a name="data-center-security"></a>Sicurezza del centro dati
Questi record vengono creati dai dati di controllo della sicurezza del centro dati.  

| Proprietà | Descrizione |
|:--- |:--- |
| EffectiveOrganization | Il nome del tenant a cui l'elevazione/cmdlet sono destinati. |
| ElevationApprovedTime | Il timestamp di quando è stata approvata l'elevazione. |
| ElevationApprover | Il nome di una gestione di Microsoft. |
| ElevationDuration | La durata per cui è stata attiva l'elevazione. |
| ElevationRequestId |  Identificatore univoco per la richiesta di elevazione. |
| ElevationRole | Il ruolo per cui è stata richiesta l'elevazione. |
| ElevationTime | L'ora di inizio dell'elevazione. |
| Start_Time | L'ora di inizio dell'esecuzione del cmdlet. |


### <a name="exchange-admin"></a>Amministratore di Exchange
Questi record vengono creati quando vengono apportate modifiche alla configurazione di Exchange.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Specifica se il cmdlet è stato eseguito da un utente nell'organizzazione, dal personale del centro dati di Microsoft, da un account di servizio del centro dati o da un amministratore delegato. Il valore False indica che il cmdlet è stato eseguito da un utente nell'organizzazione. Il valore True indica che il cmdlet è stato eseguito dal personale del centro dati, un account del servizio del centro dati o un amministratore delegato. |
| ModifiedObjectResolvedName |  Questo è il nome descrittivo dell'oggetto modificato dal cmdlet. Viene registrato solo se il cmdlet modifica l'oggetto. |
| OrganizationName | Nome del tenant. |
| OriginatingServer | Il nome del server da cui è stato eseguito il cmdlet. |
| parameters | Nome e valore per tutti i parametri usati con il cmdlet identificato nella proprietà Operations. |


### <a name="exchange-mailbox"></a>Cassetta postale di Exchange
Questi record vengono creati quando vengono apportate modifiche o aggiunte alle cassette postali di Exchange.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informazioni sul client di posta elettronica usato per eseguire l'operazione, ad esempio una versione del browser, la versione di Outlook e le informazioni sul dispositivo mobile. |
| Client_IPAddress | L'indirizzo IP del dispositivo usato quando l'operazione è stata registrata. L'indirizzo IP viene visualizzato in formato di indirizzo IPv4 o IPv6. |
| ClientMachineName | Nome del computer che ospita il client di Outlook. |
| ClientProcessName | Il client di posta elettronica usato per accedere alla cassetta postale. |
| ClientVersion | Versione del client di posta elettronica. |
| InternalLogonType | Riservato per uso interno. |
| Logon_Type | Indica il tipo di utente che ha eseguito l'accesso alla cassetta postale e ha compiuto l'operazione che è stata registrata. |
| LogonUserDisplayName |    Il nome descrittivo dell'utente che ha eseguito l'operazione. |
| LogonUserSid | L'ID di sicurezza dell'utente che ha eseguito l'operazione. |
| MailboxGuid | Il GUID di Exchange della cassetta postale a cui è stato effettuato l'accesso. |
| MailboxOwnerMasterAccountSid | ID di sicurezza dell'account proprietario della cassetta postale. |
| MailboxOwnerSid | L'ID di sicurezza del proprietario della cassetta postale. |
| MailboxOwnerUPN | L'indirizzo e-mail della persona a cui appartiene la cassetta postale che ha effettuato l'accesso. |


### <a name="exchange-mailbox-audit"></a>Controllo della cassetta postale di Exchange
Questi record vengono creati quando viene creata una voce di controllo delle cassette postali.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Elemento | Rappresenta l'elemento su cui è stata eseguita l'operazione | 
| SendAsUserMailboxGuid | Il GUID di Exchange della cassetta postale a cui è stato effettuato l'accesso per inviare e-mail. |
| SendAsUserSmtp | Indirizzo SMTP dell'utente che viene rappresentato. |
| SendonBehalfOfUserMailboxGuid | Il GUID di Exchange della cassetta postale a cui è stato effettuato l'accesso per inviare e-mail per conto di. |
| SendOnBehalfOfUserSmtp | Indirizzo SMTP dell'utente per conto del quale viene inviata l'e-mail. |


### <a name="exchange-mailbox-audit-group"></a>Gruppo di controllo della cassetta postale di Exchange
Questi record vengono creati quando vengono apportate modifiche o aggiunte ai gruppi di Exchange.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informazioni su ogni elemento nel gruppo. |
| CrossMailboxOperations | Indica se nell'operazione è coinvolta più di una cassetta postale. |
| DestMailboxId | Impostare solo se il parametro CrossMailboxOperations è True. Specifica il GUID cassetta postale di destinazione. |
| DestMailboxOwnerMasterAccountSid | Impostare solo se il parametro CrossMailboxOperations è True. Specifica l'ID di sicurezza per l'account principale del proprietario della cassetta postale di destinazione. |
| DestMailboxOwnerSid | Impostare solo se il parametro CrossMailboxOperations è True. Specifica l'ID di sicurezza della cassetta postale di destinazione. |
| DestMailboxOwnerUPN | Impostare solo se il parametro CrossMailboxOperations è True. Specifica il nome UPN del proprietario della cassetta postale di destinazione. |
| DestFolder | La cartella di destinazione per operazioni quali Sposta. |
| Cartella | La cartella in cui si trova un gruppo di elementi. |
| Cartelle |     Informazioni sulle cartelle di origine coinvolte in un'operazione; ad esempio, se le cartelle vengono selezionate e quindi eliminate. |


### <a name="sharepoint-base"></a>Base SharePoint
Queste proprietà sono comuni a tutti i record di SharePoint.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica un evento che si è verificato in SharePoint. I valori possibili sono ObjectModel o SharePoint. |
| ItemType | Il tipo dell'oggetto a cui è stato effettuato l'accesso o che è stato modificato. Vedere la tabella ItemType per informazioni dettagliate sui tipi di oggetti. |
| MachineDomainInfo | Informazioni sulle operazioni di sincronizzazione dei dispositivi. Questa informazione viene segnalata solo se è presente nella richiesta. |
| MachineId |   Informazioni sulle operazioni di sincronizzazione dei dispositivi. Questa informazione viene segnalata solo se è presente nella richiesta. |
| Site_ | Il GUID del sito in cui si trova il file o la cartella a cui l'utente ha effettuato l'accesso. |
| Source_Name | L'entità che ha attivato le operazioni di controllo. I valori possibili sono ObjectModel o SharePoint. |
| UserAgent | Informazioni sul client o browser dell'utente. Queste informazioni vengono fornite dal client o dal browser. |


### <a name="sharepoint-schema"></a>Schema di SharePoint
Questi record vengono creati quando vengono apportate modifiche alla configurazione di SharePoint.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Stringa facoltativa per gli eventi personalizzati. |
| Event_Data |  Payload facoltativo per gli eventi personalizzati. |
| ModifiedProperties | La proprietà è inclusa per gli eventi amministrativi, ad esempio l'aggiunta di un utente come membro di un sito o un gruppo di amministrazione raccolta siti. La proprietà include il nome della proprietà che è stata modificata (ad esempio, il gruppo amministratore del sito), il nuovo valore della proprietà modificata (come l'utente che è stato aggiunto come amministratore del sito) e il valore precedente dell'oggetto modificato. |


### <a name="sharepoint-file-operations"></a>Operazioni sui file di SharePoint
Questi record vengono creati in risposta alle operazioni sui file in SharePoint.

| Proprietà | Descrizione |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Estensione di un file che viene copiato o spostato. Questa proprietà viene visualizzata solo per gli eventi FileCopied e FileMoved. |
| DestinationFileName | Il nome del file che viene copiato o spostato. Questa proprietà viene visualizzata solo per gli eventi FileCopied e FileMoved. |
| DestinationRelativeUrl | L'URL della cartella di destinazione in cui un file viene copiato o spostato. La combinazione dei valori dei parametri SiteURL, DestinationRelativeURL e DestinationFileName è lo stesso del valore della proprietà ObjectID, ovvero il nome percorso completo del file che è stato copiato. Questa proprietà viene visualizzata solo per gli eventi FileCopied e FileMoved. |
| SharingType | Il tipo di autorizzazioni di condivisione che sono state assegnate all'utente con cui è stata condivisa la risorsa. Questo utente viene identificato dal parametro UserSharedWith. |
| Site_Url | L'URL del sito in cui si trova il file o la cartella a cui l'utente ha effettuato l'accesso. |
| SourceFileExtension | L'estensione del file a cui l'utente ha effettuato l'accesso. Questa proprietà è vuota se l'oggetto a cui è stato effettuato l'accesso è una cartella. |
| SourceFileName |  Il nome del file o della cartella a cui l'utente ha effettuato l'accesso. |
| SourceRelativeUrl | L'URL della cartella che contiene il file a cui l'utente ha effettuato l'accesso. La combinazione dei valori dei parametri SiteURL, SourceRelativeURL e SourceFileName è lo stesso del valore della proprietà ObjectID, ovvero il nome percorso completo del file a cui l'utente ha effettuato l'accesso. |
| UserSharedWith |  L'utente con cui è stata condivisa una risorsa. |




## <a name="sample-log-searches"></a>Ricerche di log di esempio
La tabella seguente contiene esempi di ricerche log per i record di aggiornamento raccolti da questa soluzione.

| Query | Descrizione |
| --- | --- |
|Conteggio di tutte le operazioni per la sottoscrizione di Office 365 |Type = OfficeActivity &#124; measure count() by Operation |
|Uso di siti di SharePoint|Type=OfficeActivity OfficeWorkload=sharepoint &#124; measure count() as Count by SiteUrl &#124; sort Count asc|
|Operazioni di accesso ai file per tipo di utente|Type=OfficeActivity OfficeWorkload=sharepoint Operation=FileAccessed &#124; measure count() by UserType|
|Ricerca con una parola chiave specifica|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Azioni esterne di monitoraggio di Exchange|Type=OfficeActivity OfficeWorkload=exchange ExternalAccess = true|



## <a name="troubleshooting"></a>Risoluzione dei problemi

Se la soluzione Office 365 non raccoglie i dati come previsto, controllare lo stato nel portale di OMS in **Impostazioni** -> **Origini connesse** -> **Office 365**. Nella tabella seguente sono descritti i singoli stati.

| Stato | Descrizione |
|:--|:--|
| Attivo | La sottoscrizione di Office 365 è attiva e il carico di lavoro è connesso correttamente all'area di lavoro di OMS. |
| In sospeso | La sottoscrizione di Office 365 è attiva ma il carico di lavoro non è ancora connesso correttamente all'area di lavoro di OMS. La prima volta che si connette la sottoscrizione di Office 365, tutti i carichi di lavoro passeranno in questo stato fino a quando non sono connessi correttamente. Attendere 24 ore perché tutti i carichi di lavoro passino allo stato Attivo. |
| Inactive | La sottoscrizione di Office 365 è in stato inattivo. Controllare la pagina di amministrazione di Office 365 per i dettagli. Dopo aver attivato la sottoscrizione di Office 365, scollegarla dall'area di lavoro di OMS e collegarla nuovamente per avviare la ricezione dei dati. |



## <a name="next-steps"></a>Passaggi successivi
* Usare le ricerche log in [Log Analytics](../log-analytics/log-analytics-log-searches.md) per visualizzare dati dettagliati sugli aggiornamenti.
* [Creare dashboard personalizzati](../log-analytics/log-analytics-dashboards.md) per visualizzare le query di ricerca di Office 365 preferite.
* [Creare avvisi](../log-analytics/log-analytics-alerts.md) per essere notificati in modo proattivo delle attività importanti di Office 365.  
