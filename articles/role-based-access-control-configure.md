<properties title="Role Based Access Control in Azure Preview Portal" pageTitle="Role Based Access Control in Azure Preview Portal" description="Describes how role based access control works and how to set it up" metaKeywords="" services="multiple" solutions="" documentationCenter="" authors="justinha" videoId="" scriptId="" />

<tags ms.service="multiple" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="Ibiza" ms.workload="infrastructure-services" ms.date="09/12/2014" ms.author="justinha;Justinha@microsoft.com"></tags>

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.--> <!--Properties section (above): this is required in all topics. Please fill it out!--> <!--The next line, with one pound sign at the beginning, is the page title-->

# Controllo degli accessi in base al ruolo nel Portale di anteprima Azure

Il Portale di anteprima Azure offre ora il supporto del Controllo degli accessi in base al ruolo (RBAC), che aiuta le organizzazioni a gestire gli accessi in maniera più semplice e precisa. Questo [post di blog][] include una rapida introduzione a tale funzionalità. In questo argomento vengono descritti i concetti nei dettagli e i casi di utilizzo aggiuntivi.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

## Sommario

-   [Controllo degli accessi in base al ruolo (RBAC) in Azure][]
-   [Coesistenza tra il controllo degli accessi in base al ruolo (RBAC) e le attività dei coamministratori delle sottoscrizioni][]
-   [Autorizzazioni per le operazioni di gestione e per le operazioni di dati][]
-   [Aggiunta e rimozione degli accessi][]
-   [Problemi noti del controllo degli accessi in base al ruolo][]
-   [Invio di commenti][]
-   [Passaggi successivi][]

## <span id="whatisrbac"></span></a>Controllo degli accessi in base al ruolo (RBAC) in Azure

Ogni sottoscrizione di Azure è associata a una Azure Active Directory. Gli utenti e i servizi che accedono alle risorse della sottoscrizione utilizzando il portale di gestione di Azure o l'API di Gestione risorse di Azure devono prima eseguire l'autenticazione con tale Azure Active Directory.

![][]

Il controllo degli accessi in base al ruolo di Azure consente di concedere gli accessi pertinenti agli utenti, ai gruppi e ai servizi di Azure AD, assegnando loro i ruoli in qualità di sottoscrizione, di gruppo di risorse o di risorsa indipendente. Il ruolo assegnato definisce il livello di accesso alla risorsa di Azure assegnato agli utenti, ai gruppi e ai servizi.

### Ruolo

Un ruolo è un insieme di azioni che possono essere effettuate sulle risorse di Azure. Un utente o un servizio è autorizzato a svolgere un'azione su una risorsa di Azure se il ruolo assegnato prevede tale azione.

#### Ruoli predefiniti

Alla prima anteprima, il controllo degli accessi in base al ruolo di Azure è associato a tre ruoli predefiniti che possono essere assegnati agli utenti, ai gruppi e ai servizi.

-   **Proprietario**: dispone di un controllo completo delle risorse di Azure. Il proprietario può svolgere tutte le operazioni di gestione su una risorsa, incluse quelle relative agli accessi.
-   **Collaboratore**: può svolgere tutte le attività di gestione, tranne quelle relative agli accessi. Pertanto, un collaboratore non può concedere l'accesso ad altri utenti.
-   **Lettore**: può unicamente consultare le risorse. Il lettore non può visualizzare i segreti associati a una risorsa.

I ruoli predefiniti non possono essere modificati. In una release futura di Azure, sarà possibile definire ruoli personalizzati selezionando le azioni che possono essere effettuate sulle risorse di Azure da un elenco di azioni disponibili.

#### Azioni e non azioni

La definizione del ruolo delle **azioni** specifica le azioni consentite sulle risorse di Azure. Nelle stringhe delle azioni è possibile utilizzare caratteri jolly. La definizione del ruolo delle **non azioni** specifica le azioni non consentite.

<table>
<thead>
<tr class="header">
<th align="left">
**Ruolo predefinito**

</th>
<th align="left">
Azioni

</th>
<th align="left">
Non azioni

</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">
Proprietario (tutte le azioni consentite)

</td>
<td align="left">
\*

</td>
</tr>
<tr class="even">
<td align="left">
Collaboratore (tutte le azioni consentite, tranne la scrittura e l'eliminazione delle assegnazioni di ruoli)

</td>
<td align="left">
\*

</td>
<td align="left">
Microsoft.Authorization/ \* /Write, Microsoft.Authorization/ \* /Delete

</td>
</tr>
<tr class="odd">
<td align="left">
Lettore (tutte le azioni di lettura consentite)

</td>
<td align="left">
\*/Read |

</td>
</tr>
</tbody>
</table>
### Assegnazione dei ruoli

L'accesso viene concesso agli utenti e ai servizi di Azure AD assegnando loro il ruolo pertinente per una risorsa di Azure.

#### Entità di sicurezza di Azure AD

I ruoli possono essere assegnati ai seguenti tipi di entità di sicurezza di Azure AD:

-   **Utenti**: i ruoli possono essere assegnati agli utenti organizzativi presenti nell'Azure AD a cui è associata la sottoscrizione di Azure. I ruoli possono essere assegnati anche a utenti di account Microsoft esterni (ad esempio [giovanni@outlook.com][]), utilizzando l'azione di invito per assegnare l'utente a un ruolo nel Portale di anteprima Azure. L'assegnazione di un ruolo a un utente di un account Microsoft esterno provoca la creazione di un account guest in Azure AD. Se l'account guest viene disabilitato nella directory, l'utente esterno non potrà utilizzare alcuna risorsa di Azure a cui è autorizzato ad accedere.
-   **Gruppi**: i ruoli possono essere assegnati ai gruppi di sicurezza di Azure AD. Se l'utente diventa un membro che dispone dell'accesso a una risorsa, l'accesso gli viene assegnato automaticamente. Allo stesso modo, se viene rimosso dal gruppo, l'utente perde automaticamente l'accesso alla risorsa. La gestione degli accessi attraverso i gruppi (assegnando i ruoli ai gruppi e aggiungendo gli utenti a tali gruppi) è la prassi consigliata, piuttosto che l'assegnazione diretta dei ruoli agli utenti. In Azure non è possibile assegnare ruoli alle liste di distribuzione.
    La possibilità di assegnare ruoli ai gruppi consente a un'organizzazione di estendere il controllo degli accessi dalla directory in sede al cloud. Pertanto, i gruppi di sicurezza già composti per controllare l'accesso in sede possono essere utilizzati per controllare l'accesso alle risorse nel Portale di anteprima Azure. Per maggiori informazioni sulle diverse opzioni di sincronizzazione degli utenti e dei gruppi da una directory in sede, vedere [Panoramica sull'integrazione della directory][]. Azure AD Premium offre una [funzionalità di delega della gestione dei gruppi][] che consente di delegare le attività di creazione e gestione dei gruppi a utenti non amministratori in Azure AD.
-   **Entità di servizio**: le identità di servizio, rappresentate dalle entità di servizio presenti nella directory, consentono di eseguire le autenticazioni con Azure AD e possono comunicare in maniera sicura tra loro. I servizi possono ottenere l'accesso alle risorse di Azure assegnando i ruoli all'entità Azure AD che rappresenta il servizio pertinente tramite il modulo Azure per Windows PowerShell.

#### Ambito delle risorse

Non è necessario assegnare l'accesso all'intera sottoscrizione. I ruoli possono essere assegnati sia a gruppi di risorse sia a singole risorse. In Azure, una risorsa eredita le assegnazioni dei ruoli dalle sue risorse principali. Pertanto, se un utente, un gruppo o un servizio è autorizzato ad accedere solo a un gruppo di risorse di sottoscrizione, potrà accedere solo a tale gruppo e alle sue risorse e non agli altri gruppi di risorse della sottoscrizione. Per fare un altro esempio, un gruppo di sicurezza può essere aggiunto al ruolo di lettore per un gruppo di risorse e al ruolo di collaboratore per un database all'interno di tale gruppo.

![][1]

## <span id="coexist"></span></a>Coesistenza tra il controllo degli accessi in base al ruolo (RBAC) e le attività dei coamministratori delle sottoscrizioni

L'amministratore e i coamministratori delle sottoscrizioni continueranno a disporre di un accesso completo ai portali e alle API di gestione di Azure. Nel modello RBAC, vengono associati a un ruolo di proprietario a livello di sottoscrizione.
Tuttavia, il nuovo modello RBAC è supportato solo dal Portale di anteprima Azure e dalle API di Gestione risorse di Azure. Gli utenti e servizi associati a ruoli RBAC non possono accedere al portale di gestione di Azure e alle API di Gestione servizi di Azure. L'aggiunta di un utente al ruolo di proprietario di una sottoscrizione nel Portale di anteprima Azure non rende tale utente un coamministratore della sottoscrizione nel portale Azure completo.

Per concedere a un utente l'accesso a una risorsa di Azure non ancora disponibile per la gestione nel Portale di anteprima Azure, è necessario aggiungere tale utente agli amministratori delle sottoscrizioni utilizzando il portale di gestione di Azure completo. Il bus di servizio e i servizi cloud sono due esempi di risorse che non possono essere attualmente gestite con il modello RBAC.

## <span id="authmgmt"></span></a>Autorizzazioni per le operazioni di gestione e per le operazioni di dati

Il controllo degli accessi in base al ruolo (RBAC) è supportato solo per le operazioni di gestione delle risorse di Azure nel Portale di anteprima Azure e nelle API di Gestione risorse di Azure. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. Ad esempio, le operazioni di creazione, lettura, aggiornamento ed eliminazione degli account di archiviazione possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sugli oggetti BLOB e sulle tabelle negli account di archiviazione non possono essere controllate con tale metodo. Allo stesso modo, le operazioni di creazione, lettura, aggiornamento ed eliminazione di un database SQL possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sulle tabelle del database non possono essere controllate con tale metodo.

## <span id="addremoveaccess"></span></a>Aggiunta e rimozione degli accessi

Nel prossimo esempio viene illustrata la gestione degli accessi da parte di un proprietario di risorse di un'organizzazione. In questo scenario, più persone lavorano a diversi progetti di collaudo e produzione creati utilizzando risorse di Azure. Uno degli obiettivi consiste nel concedere gli accessi nel modo più efficace. In effetti, è necessario permettere agli utenti di accedere a tutte le risorse necessarie, ma non a ulteriori elementi. Un altro obiettivo consiste nello sfruttamento degli investimenti stanziati per i processi e gli strumenti, al fine di utilizzare i gruppi di sicurezza controllati in un'Active Directory in sede. Nelle sezioni successive viene spiegato come impostare l'accesso a tali risorse:

-   [Aggiunta dell'accesso][]
-   [Rimozione dell'accesso][]
-   [Aggiunta e rimozione dell'accesso per un utente esterno][]

### 

<p>
<span id="add"></span></a>Aggiunta dell'accesso

</h2>
</p>
Di seguito è riportato un riepilogo dei requisiti di accesso e di come vengono configurati in Azure.

| Utente/Gruppo                | Requisiti di accesso                                                | Ruolo e ambito dell'accesso                                                                                              |
|------------------------------|---------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| Tutto il team di Jill Santos | Lettura di tutte le risorse di Azure                                | Aggiunta del gruppo di AD che rappresenta il team di Jill Santos al ruolo di lettore per la sottoscrizione di Azure      |
| Tutto il team di Jill Santos | Creazione e gestione di tutte le risorse nel gruppo di risorse Test | Aggiunta del gruppo di AD che rappresenta il team di Jill Santos al ruolo di collaboratore per il gruppo di risorse Test |
| Brock                        | Creazione e gestione di tutte le risorse nel gruppo di risorse Prod | Aggiunta di Brock al ruolo di collaboratore per il gruppo di risorse Prod                                                |

Aggiungere innanzitutto l'accesso in lettura a tutte le risorse della sottoscrizione. Fare clic su **Browse \> Everything \> Subscriptions**.

![][2]

Fare clic su *nome sottoscrizione* \*\* \> Reader \> Add\*\*. Nell'elenco di utenti e gruppi, selezionare o digitare il nome del gruppo di Active Directory.

![][3]

Aggiungere lo stesso team al ruolo di collaboratore del gruppo di risorse Test. Fare clic sul gruppo di risorse per aprire il blade delle proprietà. In **Roles** fare clic su **Contributor \> Add** e digitare il nome del team.

![][4]

Per aggiungere Brock al ruolo di collaboratore del gruppo di risorse Prod, fare clic sul gruppo di risorse, selezionare **Contributor \> Add** e digitare il nome di Brock.

![][5]

Le assegnazioni dei ruoli possono essere gestite anche utilizzando il modulo Microsoft Azure per Windows PowerShell. Segue un esempio dell'aggiunta di un account di Brock utilizzando il cmdlet New-AzureRoleAssignment al posto del portale:

    PS C:PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDBgt; New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Per ulteriori informazioni sull'aggiunta e sulla rimozione degli accessi tramite Windows PowerShell, vedere [Gestione del controllo degli accessi in base al ruolo con Windows PowerShell][].

### 

<p>
<span id="remove"></span></a>Rimozione dell'accesso

</h2>
</p>
Le assegnazioni possono essere rimosse facilmente. Prendiamo l'esempio della rimozione di un utente chiamato Brad Adams dal ruolo di lettore per il gruppo di risorse TestDB. Aprire il blade del gruppo di risorse e fare clic su **Reader \> Brad Adams \> Remove**.

![][6]

Segue un esempio di come rimuovere Brad Adams utilizzando il cmdlet Remove-AzureRoleAssignment:

    PS C:PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDBgt; Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### 

<p>
<span id="addremoveext"></span></a>Aggiunta e rimozione dell'accesso per un utente esterno

</h2>
</p>
La scheda **Configure** di una directory include le opzioni per il controllo degli accessi per gli utenti esterni. Tali opzioni possono essere modificate unicamente da un amministratore globale di directory all'interno dell'interfaccia utente (non sono disponibili i metodi Windows PowerShell e API) del portale Azure completo.
Aprire la scheda **Configure** del portale Azure completo facendo clic su **Active Directory**, quindi selezionare il nome della directory.

![][7]

In seguito è possibile modificare le opzioni di controllo degli accessi per gli utenti esterni.

![][8]

Per impostazione predefinita, gli ospiti non possono enumerare i contenuti della directory. Pertanto non possono visualizzare gli utenti e i gruppi presenti nell'area **Member List**. Possono cercare un utente digitandone l'indirizzo e-mail completo e, in seguito, concedere l'accesso. Segue una descrizione delle restrizioni predefinite per gli ospiti:

-   Non possono enumerare gli utenti e i gruppi nella directory.
-   Possono visualizzare solo alcuni dati di un utente, se ne conoscono l'indirizzo e-mail.
-   Possono visualizzare solo alcuni dati di un gruppo, se ne conoscono il nome.

La possibilità di visualizzare alcuni dati di un utente o di un gruppo consente agli ospiti di invitare altre persone a visualizzare alcuni dati delle persone con cui collaborano.

Ora verrà illustrata la procedura di aggiunta dell'accesso per un utente esterno. Verrà aggiunto un utente esterno allo stesso ruolo di lettore per il gruppo di risorse TestDB, per consentire all'utente di eseguire il debug di un errore. Aprire il blade del gruppo di risorse, fare clic su **Reader \> Add \> Invite** e digitare l'indirizzo e-mail dell'utente da aggiungere.

![][9]

Durante l'aggiunta di un utente esterno, un ospite viene creato nella directory. In seguito, tale ospite può essere aggiunto o rimosso da un gruppo. Inoltre, l'ospite può essere aggiunto o rimosso singolarmente da un ruolo come con qualsiasi altro utente della directory.

È possibile rimuovere un ospite da qualsiasi ruolo, come con qualsiasi altro utente. La rimozione di un ospite da ruolo su una risorsa non rimuove l'ospite dalla directory.

## <span id="knownissues"></span></a>Problemi noti del controllo degli accessi in base al ruolo

In caso di problemi durante l'uso della funzionalità di controllo degli accessi in base al ruolo in modalità di anteprima, vedere la sezione [Risoluzione dei problemi di controllo degli accessi in base al ruolo][] per consultare tutti i problemi noti in tale ambito.

## <span id="feedback"></span></a>Invio di commenti

È possibile inviare commenti sul controllo degli accessi in base al ruolo in Azure facendo clic [qui][].

## <span id="next"></span></a>Passaggi successivi

Di seguito sono riportate ulteriori risorse utili per l'uso del controllo degli accessi in base al ruolo:

-   [Gestione del controllo degli accessi in base al ruolo con Windows PowerShell][]
-   [Gestione del controllo degli accessi in base al ruolo con XPLAT CLI][]
-   [Risoluzione dei problemi di controllo degli accessi in base al ruolo][]
-   [Azure Active Directory][]
-   [Azure Active Directory Premium e Basic][]
-   [Come vengono associate le sottoscrizioni di Azure e Azure AD][]
-   Per un'introduzione alla gestione self-service dei gruppi di sicurezza, consultare il [blog del team di Active Directory][]

<!--Image references-->

  [post di blog]: http://go.microsoft.com/fwlink/?LinkId=511576
  [Controllo degli accessi in base al ruolo (RBAC) in Azure]: #whatisrbac
  [Coesistenza tra il controllo degli accessi in base al ruolo (RBAC) e le attività dei coamministratori delle sottoscrizioni]: #coexist
  [Autorizzazioni per le operazioni di gestione e per le operazioni di dati]: #authmgmt
  [Aggiunta e rimozione degli accessi]: #addremoveaccess
  [Problemi noti del controllo degli accessi in base al ruolo]: #knownissues
  [Invio di commenti]: #feedback
  [Passaggi successivi]: #next
  []: ./media/role-based-access-control-configure/RBACSubAuthDir.png
  [giovanni@outlook.com]: mailto:joe@outlook.com
  [Panoramica sull'integrazione della directory]: http://technet.microsoft.com/library/jj573653.aspx
  [funzionalità di delega della gestione dei gruppi]: http://msdn.microsoft.com/library/azure/dn641267.aspx
  [1]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
  [Aggiunta dell'accesso]: #add
  [Rimozione dell'accesso]: #remove
  [Aggiunta e rimozione dell'accesso per un utente esterno]: #addremoveext
  [2]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
  [3]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
  [4]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
  [5]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
  [Gestione del controllo degli accessi in base al ruolo con Windows PowerShell]: http://azure.microsoft.com/it-it/documentation/articles/role-based-access-control-powershell/
  [6]: ./media/role-based-access-control-configure/RBACRemoveRole.png
  [7]: ./media/role-based-access-control-configure/RBACDirConfigTab.png
  [8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
  [9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
  [Risoluzione dei problemi di controllo degli accessi in base al ruolo]: http://azure.microsoft.com/it-it/documentation/articles/role-based-access-control-troubleshooting/
  [qui]: http://aka.ms/azurerbacfeedback
  [Gestione del controllo degli accessi in base al ruolo con XPLAT CLI]: http://azure.microsoft.com/it-it/documentation/articles/role-based-access-control-xplat-cli/
  [Azure Active Directory]: http://msdn.microsoft.com/library/azure/jj673460.aspx
  [Azure Active Directory Premium e Basic]: http://msdn.microsoft.com/en-us/library/azure/dn532272.aspx
  [Come vengono associate le sottoscrizioni di Azure e Azure AD]: http://msdn.microsoft.com/en-us/library/azure/dn629581.aspx
  [blog del team di Active Directory]: http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx
