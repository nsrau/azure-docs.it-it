<properties 
	pageTitle="Controllo di accesso basato sui ruoli nel portale di anteprima di Azure" 
	description="Descrive le modalità di configurazione e utilizzo del controllo di accesso basato sui ruoli" 
	services="" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="Ibiza" 
	ms.workload="infrastructure-services" 
	ms.date="02/20/2015" 
	ms.author="justinha"/>

<!--Si tratta di un modello di base che illustra come usare il markdown per creare un argomento che include un sommario, sezioni con sottotitoli, collegamenti ad altri argomenti azure.microsoft.com, collegamenti ad altri siti, testo in grassetto, testo in corsivo, elenchi puntati e numerati, frammenti di codice e immagini. Per un markdown più interessante, trovare un argomento pubblicato e copiare il markdown o l'HTML desiderato. Per ulteriori dettagli sull'utilizzo del markdown, vedere http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Controllo degli accessi in base al ruolo nel portale di anteprima di Azure 

<p> Il Portale di anteprima Azure offre ora il supporto del Controllo degli accessi in base al ruolo (RBAC), che aiuta le organizzazioni a gestire gli accessi in maniera più semplice e precisa. Il post del blog <a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank"></a>offrirà una breve introduzione della funzionalità per iniziare. In questo argomento vengono descritti i concetti nei dettagli e i casi di utilizzo aggiuntivi. </p>

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## RBAC in Azure
                                                                   
Ogni sottoscrizione Azure è associata a un'istanza di Azure Active Directory. Gli utenti e i servizi che accedono alle risorse della sottoscrizione utilizzando il portale di gestione di Azure o l'API di Gestione risorse di Azure devono prima eseguire l'autenticazione con tale Azure Active Directory.

![][1] 

Il controllo degli accessi in base al ruolo di Azure consente di concedere l'accesso appropriato a utenti, gruppi e servizi di Azure AD, assegnando loro i ruoli a livello di sottoscrizione, di gruppo di risorse o di risorsa indipendente. Il ruolo assegnato definisce il livello di accesso alla risorsa di Azure assegnato agli utenti, ai gruppi e ai servizi. 

### Ruolo

Un ruolo è una raccolta di azioni che possono essere effettuate sulle risorse di Azure. Un utente o un servizio è autorizzato a svolgere un'azione su una risorsa di Azure se il ruolo assegnato prevede tale azione. Per un elenco dei ruoli predefiniti e delle **relative** proprietà actions e **not actions**, vedere [Ruoli predefiniti](#builtinroles).

### Assegnazione dei ruoli

L'accesso viene concesso agli utenti e ai servizi di Azure AD assegnando loro il ruolo appropriato per una risorsa di Azure. 

#### Entità di sicurezza di Azure AD

I ruoli possono essere assegnati ai seguenti tipi di entità di sicurezza di Azure AD:

+ **Utenti**: i ruoli possono essere assegnati a utenti aziendali presenti in Azure AD a cui è associata la sottoscrizione Azure. I ruoli possono essere assegnati anche a utenti di account Microsoft esterni (ad esempio giovanni@outlook.com), usando l'azione di invito per assegnare l'utente a un ruolo nel portale di anteprima di Azure. L'assegnazione di un ruolo a un utente di un account Microsoft esterno provoca la creazione di un account guest in Azure AD. Se l'account guest viene disabilitato nella directory, l'utente esterno non potrà utilizzare alcuna risorsa di Azure a cui è autorizzato ad accedere.
+ **Gruppi**: i ruoli possono essere assegnati ai gruppi di sicurezza di Azure AD. Se l'utente diventa un membro che dispone dell'accesso a una risorsa, l'accesso gli viene assegnato automaticamente. Allo stesso modo, se viene rimosso dal gruppo, l'utente perde automaticamente l'accesso alla risorsa. La gestione degli accessi attraverso i gruppi (assegnando i ruoli ai gruppi e aggiungendo gli utenti a tali gruppi) è la prassi consigliata, piuttosto che l'assegnazione diretta dei ruoli agli utenti. Azure RBAC non consente di assegnare ruoli alle liste di distribuzione.
	La possibilità di assegnare ruoli ai gruppi consente a un'organizzazione di estendere il modello di controllo degli accessi dalla directory locale al cloud. Pertanto, i gruppi di sicurezza già definiti per controllare l'accesso locale possono essere usati per controllare l'accesso alle risorse nel portale di anteprima di Azure. Per ulteriori informazioni sulle diverse opzioni per la sincronizzazione di utenti e gruppi da una directory locale, vedere [Integrazione Directory](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium offre inoltre una [funzionalità di gestione dei gruppi delegata](http://msdn.microsoft.com/library/azure/dn641267.aspx) che consente di delegare la capacità di creare e gestire gruppi a utenti non amministratori in Azure AD.
+ **Entità del servizio**: le identità del servizio sono rappresentate come entità del servizio nella directory. consentono di eseguire le autenticazioni con Azure AD e possono comunicare in maniera sicura tra loro. I servizi possono ottenere l'accesso alle risorse di Azure assegnando i ruoli all'entità Azure AD che rappresenta il servizio pertinente tramite il modulo Azure per Windows PowerShell. 

#### Ambito delle risorse

Non è necessario assegnare l'accesso all'intera sottoscrizione. I ruoli possono essere assegnati sia a gruppi di risorse sia a singole risorse. In Azure, una risorsa eredita le assegnazioni dei ruoli dalle sue risorse principali. Pertanto, se un utente, un gruppo o un servizio è autorizzato ad accedere solo a un gruppo di risorse di sottoscrizione, potrà accedere solo a tale gruppo e alle sue risorse e non agli altri gruppi di risorse della sottoscrizione. Per fare un altro esempio, un gruppo di sicurezza può essere aggiunto al ruolo di lettore per un gruppo di risorse e al ruolo di collaboratore per un database all'interno di tale gruppo.

![][2]

## Coesistenza tra il controllo degli accessi in base al ruolo (RBAC) e i coamministratori delle sottoscrizioni

L'amministratore e i coamministratori delle sottoscrizioni continueranno a disporre di un accesso completo ai portali e alle API di gestione di Azure. Nel modello di controllo di accesso basato sui ruoli, vengono associati a un ruolo di proprietario a livello di sottoscrizione.  
Tuttavia, il nuovo modello di controllo di accesso basato sui ruoli è supportato solo dal portale di anteprima di Azure e dalle API di Gestione risorse di Azure. Gli utenti e servizi associati a ruoli RBAC non possono accedere al portale di gestione di Azure e alle API di Gestione servizi di Azure. L'aggiunta di un utente al ruolo di proprietario di una sottoscrizione nel Portale di anteprima Azure non rende tale utente un coamministratore della sottoscrizione nel portale Azure completo.

Per concedere a un utente l'accesso a una risorsa di Azure che non può ancora essere gestita nel portale di anteprima di Azure, è necessario aggiungerlo ai coamministratori della sottoscrizione usando il portale di gestione di Azure completo. Il bus di servizio e i servizi cloud sono due esempi di risorse che non possono essere attualmente gestite con il modello RBAC.

## Autorizzazioni per le operazioni di gestione e per le operazioni di dati

Il controllo degli accessi in base al ruolo è supportato solo per le operazioni di gestione delle risorse di Azure nel portale di anteprima di Azure e nelle API di Gestione risorse di Azure. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. Ad esempio, le operazioni di creazione, lettura, aggiornamento ed eliminazione degli account di archiviazione possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sugli oggetti BLOB e sulle tabelle negli account di archiviazione non possono essere controllate con tale metodo. Allo stesso modo, le operazioni di creazione, lettura, aggiornamento ed eliminazione di un database SQL possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sulle tabelle del database non possono essere controllate con tale metodo.

## Aggiunta e rimozione degli accessi

Nel prossimo esempio viene illustrata la gestione degli accessi da parte di un proprietario di risorse di un'organizzazione. In questo scenario, più persone lavorano a diversi progetti di collaudo e produzione creati usando risorse di Azure. Uno degli obiettivi consiste nel concedere gli accessi nel modo più efficace. In effetti, è necessario permettere agli utenti di accedere a tutte le risorse necessarie, ma non a ulteriori elementi. Un altro obiettivo consiste nello sfruttamento degli investimenti stanziati per i processi e gli strumenti, al fine di usare i gruppi di sicurezza controllati in un'Active Directory in sede. Nelle sezioni successive viene spiegato come impostare l'accesso a tali risorse:

* [Aggiungere un accesso](#add)
* [Rimuovere un accesso](#remove)
* [Aggiungere o rimuovere l'accesso per utenti esterni](#addremoveext)

<h3><a id="add"></a>Aggiungere un accesso</h3>

Di seguito è riportato un riepilogo dei requisiti di accesso e di come vengono configurati in Azure.

Utente/Gruppo  | Requisiti di accesso  | Ruolo e ambito dell'accesso	
------------- | -------------  | ------------
Tutto il team di Jill Santos  | Lettura di tutte le risorse di Azure  | Aggiunta del gruppo di AD che rappresenta il team di Jill Santos al ruolo di lettore per la sottoscrizione di Azure
Tutto il team di Jill Santos  | Creazione e gestione di tutte le risorse nel gruppo di risorse Test  | Aggiunta del gruppo di AD che rappresenta il team di Jill Santos al ruolo di collaboratore per il gruppo di risorse Test
Brock  | Creazione e gestione di tutte le risorse nel gruppo di risorse Prod  | Aggiunta di Brock al ruolo di collaboratore per il gruppo di risorse Prod


Aggiungere innanzitutto l'accesso in lettura a tutte le risorse della sottoscrizione. Fare clic su **Sfoglia > Tutto > Sottoscrizioni**.

![][3] 

Fare clic su *name of your subscription* ** > Lettore > Aggiungi**. Nell'elenco di utenti e gruppi, selezionare o digitare il nome del gruppo di Active Directory.

![][4]

Aggiungere lo stesso team al ruolo di collaboratore del gruppo di risorse Test. Fare clic sul gruppo di risorse per aprire il pannello delle proprietà. In **Ruoli**, fare clic su **Collaboratore > Aggiungi** e digitare il nome del team.

![][5]

Per aggiungere Brock al ruolo di collaboratore del gruppo di risorse Prod, fare clic su **Collaboratore > Aggiungi** e digitare il nome di Brock. 

![][6]

Le assegnazioni dei ruoli possono inoltre essere gestite utilizzando il modulo di Microsoft Azure per Windows PowerShell. Segue un esempio dell'aggiunta di un account di Brock utilizzando il cmdlet New-AzureRoleAssignment al posto del portale:

	PS C:> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Per ulteriori informazioni sull'aggiunta e sulla rimozione degli accessi tramite Windows PowerShell, vedere [Gestione del controllo degli accessi in base al ruolo con Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/). 

<h3><a id="remove"></a>Rimuovere un accesso</h3>

Le assegnazioni possono essere rimosse facilmente. Prendiamo l'esempio della rimozione di un utente chiamato Brad Adams dal ruolo di lettore per il gruppo di risorse TestDB. Aprire il pannello del gruppo di risorse e fare clic su **Lettore > Brad Adams > Rimuovi**.

![][7]

Segue un esempio di come rimuovere Brad Adams usando il cmdlet Remove-AzureRoleAssignment:

	PS C:> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

<h3><a id="addremoveext"></a>Aggiungere o rimuovere l'accesso per utenti esterni</h3>

La scheda **Configura** di una directory include opzioni per il controllo degli accessi per gli utenti esterni. Tali opzioni possono essere modificate unicamente da un amministratore globale di directory all'interno dell'interfaccia utente (non sono disponibili i metodi Windows PowerShell e API) del portale di Azure completo. 
Per aprire la scheda **Configura** nel portale di Azure completo, fare clic su **Active Directory**, quindi selezionare il nome della directory.

![][10]

In seguito è possibile modificare le opzioni di controllo degli accessi per gli utenti esterni. 

![][8]

Per impostazione predefinita, gli ospiti non possono enumerare i contenuti della directory, pertanto non possono visualizzare gli utenti e i gruppi in **Elenco membri**. Possono cercare un utente digitandone l'indirizzo e-mail completo e, in seguito, concedere l'accesso. Segue una descrizione delle restrizioni predefinite per gli ospiti:

- Non possono enumerare gli utenti e i gruppi nella directory.
- Possono visualizzare solo alcuni dati di un utente, se ne conoscono l'indirizzo e-mail.
- Possono visualizzare solo alcuni dati di un gruppo, se ne conoscono il nome.

La possibilità per gli ospiti di visualizzare dettagli limitati di un utente o gruppo consente loro di invitare altre persone e visualizzare alcuni dettagli relativi alle persone con cui collaborano.  

Ora verrà illustrata la procedura di aggiunta dell'accesso per un utente esterno. Verrà aggiunto un utente esterno allo stesso ruolo di lettore per il gruppo di risorse TestDB, per consentire all'utente di eseguire il debug di un errore. Aprire il pannello del gruppo di risorse, fare clic su **Lettore > Aggiungi > Invita** e digitare l'indirizzo e-mail dell'utente da aggiungere. 

![][9]

Quando si aggiunge un utente esterno, nella directory viene creato un ospite. In seguito, tale ospite può essere aggiunto o rimosso da un gruppo. Inoltre, l'ospite può essere aggiunto o rimosso singolarmente da un ruolo come con qualsiasi altro utente della directory. 

È inoltre possibile rimuovere un ospite da qualsiasi ruolo, come con qualsiasi altro utente. La rimozione di un ospite da ruolo su una risorsa non rimuove l'ospite dalla directory. 
 
## Problemi noti relativi al controllo degli accessi in base al ruolo

In caso di problemi quando si utilizza la funzionalità di controllo degli accessi in base al ruolo in modalità di anteprima, vedere [Risoluzione dei problemi relativi al controllo degli accessi in base al ruolo](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/) per consultare tutti i problemi noti in tale ambito.


## Ruoli predefiniti

Il controllo degli accessi in base al ruolo di Azure presenta i seguenti ruoli predefiniti che possono essere assegnati a utenti, gruppi e servizi. I ruoli predefiniti non possono essere modificati. In una futura release di Azure RBAC, sarà possibile definire ruoli personalizzati componendo un set di azioni da un elenco di azioni disponibili che possono essere effettuate sulle risorse di Azure.

Fare clic sul collegamento corrispondente per visualizzare le proprietà **actions** e **not actions** di una definizione del ruolo. La proprietà **actions** specifica le azioni consentite sulle risorse di Azure. Nelle stringhe delle azioni è possibile usare caratteri jolly. La proprietà **not actions** di una definizione del ruolo specifica le azioni che devono essere escluse dalle azioni consentite. 


Nome del ruolo | Descrizione  	
------------- | -------------  
[Collaboratore servizio Gestione API](#APIMgmt) | Consente di gestire il servizio Gestione API, ma non di accedervi.
[Collaboratore componente Application Insights](#AppInsights) | Consente di gestire i componenti di Application Insights, ma non di accedervi.
[Collaboratore BizTalk](#BizTalk) | Consente di gestire i servizi BizTalk, ma non di accedervi.
[Collaboratore database ClearDB MySQL](#ClearDB) | Consente di gestire il database ClearDB MySQL, ma non di accedervi.
[Collaboratore](#Contributor) | I collaboratori possono gestire tutto ad eccezione degli accessi.
[Collaboratore Data Factory](#DataFactory) | Consente di gestire data factory, ma non di accedervi.
[Collaboratore account Document DB](#DocDBContrib) | Consente di gestire gli account di DocumentDB, ma non di accedervi.
[Collaboratore account Intelligent Systems](#IntelliSysContrib) | Consente di gestire gli account di Intelligent Systems, ma non di accedervi.
[Collaboratore account NewRelic APM](#NewRelicContrib) | Consente di gestire gli account e le applicazioni di New Relic Application Performance Management, ma non di accedervi.
[Proprietario](#Owner) | Il proprietario può gestire tutto, compresi gli accessi.
[Lettore](#Reader) | I lettori possono visualizzare tutto, ma non possono apportare modifiche.
[Collaboratore cache Redis](#Redis) | Consente di gestire le cache di Redis, ma non di accedervi.
[Collaboratore database SQL](#SQLDBContrib) | Consente di gestire i database SQL, ma non di accedervi. Inoltre, non è possibile gestire i criteri relativi alla sicurezza o i server SQL padre.
[Gestione della sicurezza SQL](#SQLSecMgr) | Consente di gestire i criteri di sicurezza dei server e database SQL, ma non di accedervi.
[Collaboratore SQL Server](#SQLSrvContrib) | Consente di gestire server e database SQL, ma non di accedervi e non consente di gestire i criteri di sicurezza relativi.
[Collaboratore raccolte di processi dell'unità di pianificazione](#SchedContrib) | Consente di gestire le raccolte di processi dell'unità di pianificazione, ma non di accedervi.
[Collaboratore servizi di ricerca](#SearchContrib) | Consente di gestire i servizi di ricerca, ma non di accedervi.
[Collaboratore account di archiviazione](#StorageContrib) | Consente di gestire gli account di archiviazione, ma non di accedervi.
[Amministratore accessi utente](#UserAccessAdmin) | Consente di gestire gli accessi utente alle risorse di Azure.
[Collaboratore macchine virtuali](#VMContrib) | Consente di gestire le macchine virtuali, ma non di accedervi e non consente di gestire l'account di archiviazione o la rete virtuale a cui sono collegate.
[Collaboratore reti virtuali](#VNetContrib) | Consente di gestire le reti virtuali, ma non di accedervi.
[Collaboratore piani Web](#WebPlanContrib) | Consente di gestire i piani Web per i siti Web, ma non di accedervi.
[Collaboratore siti Web](#WebsiteContrib) | Consente di gestire i siti Web (non i piani Web), ma non di accedervi.


<h3><a id="APIMgmt"></a>Collaboratore servizio Gestione API</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>È in grado di creare e gestire i servizi Gestione API</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="AppInsights"></a>Collaboratore componente Application Insights</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>È in grado di creare e gestire i componenti di Insights</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>È in grado di creare e gestire i test Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="BizTalk"></a>Collaboratore BizTalk</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>È in grado di creare e gestire i servizi BizTalk</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="ClearDB"></a>Collaboratore database ClearDB MySQL</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>È in grado di creare e gestire i database ClearDB MySQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="Contributor"></a>Collaboratore</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>*</td>
<td>È in grado di creare e gestire ogni tipo di risorsa</td>
</tr>
<tr>
<th colspan="2">Non azioni</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>Non è in grado di creare ruoli e assegnazioni di ruoli </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>Non è in grado di eliminare ruoli e assegnazioni di ruoli</td>
</tr>
</table>

<h3><a id="DataFactory"></a>Collaboratore Data Factory</h3>

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>È in grado di creare e gestire data factory</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="DocDBContrib"></a>Collaboratore account Document DB</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>È in grado di creare e gestire account DocumentDB</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="IntelliSysContrib"></a>Collaboratore account Intelligent Systems</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>È in grado di creare e gestire account di Intelligent Systems</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="NewRelicContrib"></a>Collaboratore account NewRelic APM</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>È in grado di creare e gestire account di NewRelic Application Performance Management</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="Owner"></a>Proprietario</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>*</td>
<td>È in grado di creare e gestire ogni tipo di risorsa</td>
</tr>
</table>

<h3><a id="Reader"></a>Lettore</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>*/lettura</td>
<td>È in grado di leggere ogni tipo di risorsa. È in grado tuttavia di leggere i segreti.</td>
</tr>
</table>

<h3><a id="Redis"></a>Collaboratore cache Redis</h3>

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Creare e gestire le cache Redis</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="SQLDBContrib"></a>Collaboratore database SQL</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>È in grado di leggere i server SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>È in grado di creare e gestire i database SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
<tr>
<th colspan="2">Non azioni</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Non è in grado di gestire i criteri di controllo dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Non è in grado di gestire i criteri di connessione dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Non è in grado di gestire i criteri della maschera dei dati dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Non è in grado di gestire le metriche di sicurezza dei database SQL</td>
</tr>
</table>

<h3><a id="SQLSecMgr"></a>Gestione della sicurezza SQL</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>È in grado di leggere i server SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>È in grado di creare e gestire criteri di controllo di SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>È in grado di leggere i database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>È in grado di creare e gestire i criteri di controllo dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>È in grado di creare e gestire i criteri di connessione dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>È in grado di creare e gestire i criteri della maschera dei dati dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>È in grado di creare e gestire le metriche di sicurezza dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="SQLSrvContrib"></a>Collaboratore SQL Server</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>È in grado di creare e gestire i servizi Gestione API</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
<tr>
<th colspan="2">Non azioni</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Non è in grado di gestire i criteri di controllo di SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Non è in grado di gestire i criteri di controllo dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Non è in grado di gestire i criteri di connessione dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Non è in grado di gestire i criteri della maschera dei dati dei database SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Non è in grado di gestire le metriche di sicurezza dei database SQL</td>
</tr>
</table>

<h3><a id="SchedContrib"></a>Collaboratore raccolte di processi dell'unità di pianificazione</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>È in grado di creare e gestire raccolte di processi dell'unità di pianificazione</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="SearchContrib"></a>Collaboratore servizi di ricerca</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>È in grado di creare e gestire i servizi di ricerca</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="StorageContrib"></a>Collaboratore account di archiviazione</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>È in grado di creare e gestire account di archiviazione</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="UserAccessAdmin"></a>Amministratore accessi utente</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>*/lettura</td>
<td>È in grado di leggere ogni tipo di risorsa</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>È in grado di creare e gestire i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="VMContrib"></a>Collaboratore macchine virtuali</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>È in grado di leggere gli account di archiviazione</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>È in grado di recuperare le chiavi dell'account di archiviazione</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>È in grado di leggere reti virtuali</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>È in grado di aggiungere reti virtuali</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>È in grado di leggere indirizzi IP riservati</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>È in grado di collegare agli indirizzi IP riservati</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>È in grado di creare e gestire servizi cloud</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>È in grado di creare e gestire macchine virtuali</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="VNetContrib"></a>Collaboratore reti virtuali</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>È in grado di creare e gestire reti virtuali</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="WebPlanContrib"></a>Collaboratore piani Web</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>È in grado di creare e gestire piani Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>

<h3><a id="WebsiteContrib"></a>Collaboratore siti Web</h3>

<table style=width:100%">
<tr>
<th colspan="2">Azioni</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>È in grado di leggere piani Web</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>È in grado di aggiungere piani Web</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>È in grado di creare e gestire siti Web</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>È in grado di creare e gestire certificati dei siti Web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>È in grado di leggere i ruoli e le assegnazioni di ruoli</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>È in grado di leggere i gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>È in grado di creare e gestire le distribuzioni di gruppi di risorse</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>È in grado di creare e gestire le regole di avviso</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>È in grado di creare e gestire i ticket di supporto</td>
</tr>
</table>


## Invio di commenti

Provare il controllo degli accessi in base al ruolo di Azure e inviare un [feedback](http://aka.ms/azurerbacfeedback). 


## Passaggi successivi

Di seguito sono riportate altre risorse utili per l'uso del controllo degli accessi in base al ruolo: 

+ [Gestione del controllo di accesso basato sui ruoli con Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/)
+ [Gestione del controllo di accesso basato sui ruoli con XPLAT CLI](http://azure.microsoft.com/documentation/articles/role-based-access-control-xplat-cli/)
+ [Risoluzione dei problemi relativi al controllo degli accessi in base al ruolo](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)
+ [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
+ [Azure Active Directory Premium e Basic](http://msdn.microsoft.com/library/azure/dn532272.aspx)
+ [Come vengono associate le sottoscrizioni Azure ad Azure AD](http://msdn.microsoft.com/library/azure/dn629581.aspx)
+ Per un'introduzione sulla gestione self-service dei gruppi di sicurezza, vedere il [blog del team di Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)



<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png



<!--HONumber=47-->
