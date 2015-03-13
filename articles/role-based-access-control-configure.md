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
	ms.date="02/06/2015" 
	ms.author="justinha"/>

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Controllo di accesso basato sui ruoli nel portale di anteprima di Azure 

<p> Nel portale di anteprima di Azure è stato aggiunto il supporto del controllo di accesso basato sui ruoli per consentire alle organizzazioni di soddisfare in modo semplice e preciso i requisiti di gestione degli accessi. Questo <a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank">post di blog</a> include una rapida introduzione a tale funzionalità. In questo argomento vengono descritti i concetti nei dettagli e i casi di utilizzo aggiuntivi. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##Sommario##

* [Controllo di accesso basato sui ruoli in Azure](#whatisrbac) 
* [Coesistenza tra il controllo di accesso basato sui ruoli (RBAC) e le attività dei coamministratori delle sottoscrizioni](#coexist)
* [Autorizzazioni per le operazioni di gestione e per le operazioni di dati](#authmgmt)
* [Aggiunta e rimozione degli accessi](#addremoveaccess)
* [Problemi noti del controllo di accesso basato sui ruoli](#knownissues)
* [Invio di commenti](#feedback)
* [Passaggi successivi](#next)

<h2><a id="whatisrbac"></a>Controllo di accesso basato sui ruoli (RBAC) in Azure</h2>
                                                                   
Ogni sottoscrizione di Azure è associata a un'istanza di Azure Active Directory. Gli utenti e i servizi che accedono alle risorse della sottoscrizione usando il portale di gestione di Azure o l'API di Gestione risorse di Azure devono prima eseguire l'autenticazione con tale istanza di Azure Active Directory.

![][1] 

Il controllo di accesso basato sui ruoli di Azure consente di concedere gli accessi appropriati agli utenti, ai gruppi e ai servizi di Azure AD, assegnando loro i ruoli a livello di sottoscrizione, di gruppo di risorse o di risorsa indipendente. Il ruolo assegnato definisce il livello di accesso alla risorsa di Azure assegnato agli utenti, ai gruppi e ai servizi. 

### Ruolo

Un ruolo è un raccolta di azioni che possono essere effettuate sulle risorse di Azure. Un utente o un servizio è autorizzato a svolgere un'azione su una risorsa di Azure se il ruolo assegnato prevede tale azione.

#### Ruoli predefiniti

Alla prima anteprima, il controllo di accesso basato sui ruoli di Azure è associato a tre ruoli predefiniti che possono essere assegnati agli utenti, ai gruppi e ai servizi.

+ **Proprietario**: dispone di un controllo completo delle risorse di Azure. Il proprietario può svolgere tutte le operazioni di gestione su una risorsa, incluse quelle relative agli accessi. 
+ **Collaboratore**: può svolgere tutte le attività di gestione, tranne quelle relative agli accessi. Pertanto, un collaboratore non può concedere l'accesso ad altri utenti.
+ **Lettore**: può unicamente consultare le risorse. Il lettore non può visualizzare i segreti associati a una risorsa. 

I ruoli predefiniti non possono essere modificati. In una release futura di Azure, sarà possibile definire ruoli personalizzati selezionando le azioni che possono essere effettuate sulle risorse di Azure da un elenco di azioni disponibili.

#### Azioni e non azioni

La definizione del ruolo delle azioni specifica le **azioni** consentite sulle risorse di Azure. Nelle stringhe delle azioni è possibile usare caratteri jolly. La definizione del ruolo delle **non azioni** specifica le azioni non consentite. 

**Ruolo predefinito**  | Azioni | Non azioni	
------------- | -------------  | ------------
Proprietario (tutte le azioni consentite)  | *  | 
Collaboratore (tutte le azioni consentite, tranne la scrittura o l'eliminazione delle assegnazioni di ruoli)  | *  | Microsoft.Authorization/ * /Write, Microsoft.Authorization/ * /Delete
Lettore (tutte le azioni di lettura consentite)  | */Read  | 

### Assegnazione dei ruoli

L'accesso viene concesso agli utenti e ai servizi di Azure AD assegnando loro il ruolo appropriato per una risorsa di Azure. 

#### Entità di sicurezza di Azure AD

I ruoli possono essere assegnati ai seguenti tipi di entità di sicurezza di Azure AD:

+ **Utenti**: i ruoli possono essere assegnati agli utenti organizzativi presenti nell'Azure AD a cui è associata la sottoscrizione di Azure. I ruoli possono essere assegnati anche a utenti di account Microsoft esterni (ad esempio giovanni@outlook.com), usando l'azione di invito per assegnare l'utente a un ruolo nel portale di anteprima di Azure. L'assegnazione di un ruolo a un utente di un account Microsoft esterno provoca la creazione di un account guest in Azure AD. Se l'account guest viene disabilitato nella directory, l'utente esterno non potrà usare alcuna risorsa di Azure a cui è autorizzato ad accedere.
+ **Gruppi**: i ruoli possono essere assegnati ai gruppi di sicurezza di Azure AD. Se l'utente diventa un membro che dispone dell'accesso a una risorsa, l'accesso gli viene assegnato automaticamente. Allo stesso modo, se viene rimosso dal gruppo, l'utente perde automaticamente l'accesso alla risorsa. La gestione degli accessi attraverso i gruppi (assegnando i ruoli ai gruppi e aggiungendo gli utenti a tali gruppi) è la prassi consigliata, piuttosto che l'assegnazione diretta dei ruoli agli utenti. In Azure non è possibile assegnare ruoli alle liste di distribuzione.
	La possibilità di assegnare ruoli ai gruppi consente a un'organizzazione di estendere il modello di controllo degli accessi dalla directory locale al cloud. Pertanto, i gruppi di sicurezza già definiti per controllare l'accesso locale possono essere usati per controllare l'accesso alle risorse nel portale di anteprima di Azure. Per maggiori informazioni sulle diverse opzioni di sincronizzazione degli utenti e dei gruppi da una directory in sede, vedere [Panoramica sull'integrazione della directory](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium offre una [funzionalità di delega della gestione dei gruppi](http://msdn.microsoft.com/library/azure/dn641267.aspx) che consente di delegare le attività di creazione e gestione dei gruppi a utenti non amministratori in Azure AD.
+ **Entità di servizio**: le identità di servizio, rappresentate dalle entità di servizio presenti nella directory, consentono di eseguire le autenticazioni con Azure AD e possono comunicare in maniera sicura tra loro. I servizi possono ottenere l'accesso alle risorse di Azure assegnando i ruoli all'entità Azure AD che rappresenta il servizio pertinente tramite il modulo Azure per Windows PowerShell. 

#### Ambito delle risorse

Non è necessario assegnare l'accesso all'intera sottoscrizione. I ruoli possono essere assegnati sia a gruppi di risorse sia a singole risorse. In Azure, una risorsa eredita le assegnazioni dei ruoli dalle sue risorse principali. Pertanto, se un utente, un gruppo o un servizio è autorizzato ad accedere solo a un gruppo di risorse di sottoscrizione, potrà accedere solo a tale gruppo e alle sue risorse e non agli altri gruppi di risorse della sottoscrizione. Per fare un altro esempio, un gruppo di sicurezza può essere aggiunto al ruolo di lettore per un gruppo di risorse e al ruolo di collaboratore per un database all'interno di tale gruppo.

![][2]

<h2><a id="coexist"></a>Coesistenza tra il controllo di accesso basato sui ruoli e le attività dei coamministratori delle sottoscrizioni</h2>

L'amministratore e i coamministratori delle sottoscrizioni continueranno a disporre di un accesso completo ai portali e alle API di gestione di Azure. Nel modello di controllo di accesso basato sui ruoli, vengono associati a un ruolo di proprietario a livello di sottoscrizione.  
Tuttavia, il nuovo modello di controllo di accesso basato sui ruoli è supportato solo dal portale di anteprima di Azure e dalle API di Gestione risorse di Azure. Gli utenti e servizi associati a ruoli RBAC non possono accedere al portale di gestione di Azure e alle API di Gestione servizi di Azure. L'aggiunta di un utente al ruolo di proprietario di una sottoscrizione nel portale di anteprima di Azure non rende tale utente un coamministratore della sottoscrizione nel portale di Azure completo.

Per concedere a un utente l'accesso a una risorsa di Azure non ancora disponibile per la gestione nel portale di anteprima di Azure, è necessario aggiungere tale utente agli amministratori delle sottoscrizioni usando il portale di gestione di Azure completo. Il bus di servizio e i servizi cloud sono due esempi di risorse che non possono essere attualmente gestite con il modello di controllo di accesso basato sui ruoli.

<h2><a id="authmgmt"></a>Autorizzazioni per le operazioni di gestione e per le operazioni di dati</h2>

Il controllo di accesso basato sui ruoli è supportato solo per le operazioni di gestione delle risorse di Azure nel portale di anteprima di Azure e nelle API di Gestione risorse di Azure. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. Ad esempio, le operazioni di creazione, lettura, aggiornamento ed eliminazione degli account di archiviazione possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sugli oggetti BLOB e sulle tabelle negli account di archiviazione non possono essere controllate con tale metodo. Allo stesso modo, le operazioni di creazione, lettura, aggiornamento ed eliminazione di un database SQL possono essere gestite tramite il controllo di accesso basato sui ruoli, mentre le stesse operazioni svolte sulle tabelle del database non possono essere controllate con tale metodo.

<h2><a id="addremoveaccess"></a>Aggiunta e rimozione degli accessi</h2>

Nel prossimo esempio viene illustrata la gestione degli accessi da parte di un proprietario di risorse di un'organizzazione. In questo scenario, più persone lavorano a diversi progetti di collaudo e produzione creati usando risorse di Azure. Uno degli obiettivi consiste nel concedere gli accessi nel modo più efficace. In effetti, è necessario permettere agli utenti di accedere a tutte le risorse necessarie, ma non a ulteriori elementi. Un altro obiettivo consiste nello sfruttamento degli investimenti stanziati per i processi e gli strumenti, al fine di usare i gruppi di sicurezza controllati in un'Active Directory in sede. Nelle sezioni successive viene spiegato come impostare l'accesso a tali risorse:

* [Aggiunta dell'accesso](#add)
* [Rimozione dell'accesso](#remove)
* [Aggiunta e rimozione dell'accesso per un utente esterno](#addremoveext)

<h3><a id="add"></a>Aggiunta dell'accesso</h2>

Di seguito è riportato un riepilogo dei requisiti di accesso e di come vengono configurati in Azure.

Utente/Gruppo  | Requisiti di accesso  | Ruolo e ambito dell'accesso	
------------- | -------------  | ------------
Tutto il team di Jill Santos  | Lettura di tutte le risorse di Azure  | Aggiunta del gruppo di AD che rappresenta il team di Jill Santos al ruolo di lettore per la sottoscrizione di Azure
Tutto il team di Jill Santos  | Creazione e gestione di tutte le risorse nel gruppo di risorse Test  | Aggiunta del gruppo di AD che rappresenta il team di Jill Santos al ruolo di collaboratore per il gruppo di risorse Test
Brock  | Creazione e gestione di tutte le risorse nel gruppo di risorse Prod  | Aggiunta di Brock al ruolo di collaboratore per il gruppo di risorse Prod


Aggiungere innanzitutto l'accesso in lettura a tutte le risorse della sottoscrizione. Fare clic su **Browse > Everything > Subscriptions**.

![][3] 

Fare clic su *nome sottoscrizione* ** > Reader > Add**. Nell'elenco di utenti e gruppi, selezionare o digitare il nome del gruppo di Active Directory.

![][4]

Aggiungere lo stesso team al ruolo di collaboratore del gruppo di risorse Test. Fare clic sul gruppo di risorse per aprire il pannello delle proprietà. In **Roles** fare clic su **Contributor > Add** e digitare il nome del team.

![][5]

Per aggiungere Brock al ruolo di collaboratore del gruppo di risorse Prod, fare clic sul gruppo di risorse, selezionare **Contributor > Add** e digitare il nome di Brock. 

![][6]

Le assegnazioni dei ruoli possono essere gestite anche usando il modulo Microsoft Azure per Windows PowerShell. Segue un esempio dell'aggiunta di un account di Brock usando il cmdlet New-AzureRoleAssignment anziché il portale:

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Per altre informazioni sull'aggiunta e sulla rimozione degli accessi tramite Windows PowerShell, vedere [Gestione del controllo di accesso basato sui ruoli con Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/). 

<h3><a id="remove"></a>Rimozione dell'accesso</h2>

Le assegnazioni possono essere rimosse facilmente. Prendiamo l'esempio della rimozione di un utente chiamato Brad Adams dal ruolo di lettore per il gruppo di risorse TestDB. Aprire il pannello del gruppo di risorse e fare clic su **Reader > Brad Adams > Remove**.

![][7]

Segue un esempio di come rimuovere Brad Adams usando il cmdlet Remove-AzureRoleAssignment:

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

<h3><a id="addremoveext"></a>Aggiunta e rimozione dell'accesso per un utente esterno</h2>

La scheda **Configure** di una directory include le opzioni per il controllo degli accessi per gli utenti esterni. Tali opzioni possono essere modificate unicamente da un amministratore globale di directory all'interno dell'interfaccia utente (non sono disponibili i metodi Windows PowerShell e API) del portale di Azure completo. 
Per aprire la scheda **Configure** nel portale Azure di completo, fare clic su **Active Directory**, quindi selezionare il nome della directory.

![][10]

In seguito è possibile modificare le opzioni di controllo degli accessi per gli utenti esterni. 

![][8]

Per impostazione predefinita, gli utenti guest non possono enumerare i contenuti della directory. Pertanto non possono visualizzare gli utenti e i gruppi presenti nell'area **Member List**. Possono cercare un utente digitandone l'indirizzo e-mail completo e, in seguito, concedere l'accesso. Segue una descrizione delle restrizioni predefinite per gli utenti guest:

- Non possono enumerare gli utenti e i gruppi nella directory.
- Possono visualizzare solo alcuni dati di un utente, se ne conoscono l'indirizzo e-mail.
- Possono visualizzare solo alcuni dati di un gruppo, se ne conoscono il nome.

La possibilità di visualizzare alcuni dati di un utente o di un gruppo consente agli utenti guest di invitare altre persone a visualizzare alcuni dati delle persone con cui collaborano.  

Ora verrà illustrata la procedura di aggiunta dell'accesso per un utente esterno. Verrà aggiunto un utente esterno allo stesso ruolo di lettore per il gruppo di risorse TestDB, per consentire all'utente di eseguire il debug di un errore. Aprire il pannello del gruppo di risorse, fare clic su **Reader > Add > Invite** e digitare l'indirizzo e-mail dell'utente da aggiungere. 

![][9]

Quando si aggiunge un utente esterno, nella directory viene creato un utente guest. In seguito, tale ospite può essere aggiunto o rimosso da un gruppo. Inoltre, l'ospite può essere aggiunto o rimosso singolarmente da un ruolo come con qualsiasi altro utente della directory. 

È possibile rimuovere un utente guest da qualsiasi ruolo, come con qualsiasi altro utente. La rimozione di un ospite da ruolo su una risorsa non rimuove l'ospite dalla directory. 
 
<h2><a id="knownissues"></a>Problemi noti del controllo di accesso basato sui ruoli</h2>

In caso di problemi durante l'uso della funzionalità di controllo di accesso basato sui ruoli in modalità di anteprima, vedere la sezione [Risoluzione dei problemi di controllo di accesso basato sui ruoli](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/) per consultare tutti i problemi noti in tale ambito.


<h2><a id="feedback"></a>Invio di commenti</h2>

Provare il controllo di accesso basato sui ruoli in Azure e inviare [feedback](http://aka.ms/azurerbacfeedback). 


<h2><a id="next"></a>Passaggi successivi</h2>

Di seguito sono riportate altre risorse utili per l'uso del controllo di accesso basato sui ruoli: 

+ [Gestione del controllo di accesso basato sui ruoli con Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/)
+ [Gestione del controllo di accesso basato sui ruoli con XPLAT CLI](http://azure.microsoft.com/documentation/articles/role-based-access-control-xplat-cli/)
+ [Risoluzione dei problemi di controllo di accesso basato sui ruoli](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)
+ [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
+ [Azure Active Directory Premium e Basic](http://msdn.microsoft.com/library/azure/dn532272.aspx)
+ [Come vengono associate le sottoscrizioni di Azure e Azure AD](http://msdn.microsoft.com/library/azure/dn629581.aspx)
+ Per un'introduzione alla gestione self-service dei gruppi di sicurezza, vedere il [blog del team di Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)



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



<!--HONumber=46--> 
