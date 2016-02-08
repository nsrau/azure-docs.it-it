<properties
	pageTitle="Controllo di accesso in base al ruolo di Azure Active Directory AD | Microsoft Azure"
	description="In questo articolo viene descritto il Controllo degli accessi in base al ruolo di Azure."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/21/2016"
	ms.author="kgremban"/>

# Controllo degli accessi in base al ruolo di Azure

## Controllo degli accessi in base al ruolo
Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando il Controllo degli accessi in base al ruolo di Azure, è possibile separare compiti all'interno del team DevOps e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro.

### Nozioni fondamentali della gestione degli accessi in Azure
Ogni sottoscrizione di Azure è associata a un'istanza di Azure Active Directory. Solo utenti, gruppi e applicazioni di tale directory possono ottenere l'accesso per gestire le risorse nella sottoscrizione di Azure, usando il portale di Azure, gli strumenti da riga di comando di Azure e le API di gestione di Azure.

L’accesso viene concesso assegnando i ruoli RBAC appropriati a utenti, gruppi e applicazioni nell'ambito corretto. Per concedere l'accesso all'intera sottoscrizione, assegnare un ruolo nell'ambito della sottoscrizione. Per concedere l'accesso a un gruppo di risorse specifico all'interno di una sottoscrizione, assegnare un ruolo nell'ambito del gruppo di risorse. È possibile assegnare ruoli anche a risorse specifiche, quali siti Web, macchine virtuali e subnet, per concedere l'accesso solo a una risorsa.

![Risorse di Azure Active Directory e strumenti di gestione delle risorse - Diagramma](./media/role-based-access-control-configure/overview.png)

Il ruolo Controllo degli accessi in base al ruolo assegnato a utenti, gruppi e applicazioni determina quali risorse possono essere gestire in tale ambito.

### Ruoli predefiniti del Controllo degli accessi in base al ruolo di Azure
Il Controllo degli accessi in base al ruolo di Azure include di tre ruoli di base che si applicano a tutti i tipi di risorsa: Proprietario, Collaboratore e Lettore. Proprietario dispone dell'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Collaboratore può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri utenti. Lettore può solo visualizzare le risorse di Azure esistenti. Il resto dei ruoli RBAC in Azure consente la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore di macchina virtuale consente la creazione e la gestione delle macchine virtuali, ma non consente la gestione della rete virtuale o della subnet a cui la macchina virtuale si connette.

[Ruoli predefiniti RBAC](role-based-access-built-in-roles.md) elenca i ruoli RBAC predefiniti disponibili in Azure. Per ogni ruolo, specifica le operazioni a cui un ruolo predefinito consente l'accesso.

### Gerarchia ed ereditarietà dell'accesso delle risorse di Azure
Ogni sottoscrizione in Azure appartiene a un'unica e sola directory, ogni gruppo di risorse appartiene a un’unica e sola sottoscrizione e ogni risorsa appartiene a un unico e solo gruppo di risorse. L’accesso che si concede all’ambito padre viene ereditato dall’ambito figlio. Se si concede il ruolo Lettore a un gruppo di Azure AD nell'ambito della sottoscrizione, i membri di tale gruppo potranno visualizzare tutti i gruppi di risorse e tutte le risorse nella sottoscrizione. Se si concede il ruolo di Collaboratore a un'applicazione nell'ambito del gruppo di risorse, essa sarà in grado di gestire le risorse di tutti i tipi in tale gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

### Controllo degli accessi in base al ruolo di Azure rispetto ad Amministratore sottoscrizione classico e Coamministratori
Amministratore sottoscrizione classico e Coamministratori hanno accesso completo alla sottoscrizione di Azure. Possono gestire le risorse usando il [portale di Azure](https://portal.azure.com) e le API di Gestione risorse di Azure, oltre al [portale di Azure classico](https://manage.windowsazure.com) e alle API di gestione del servizio Microsoft Azure. Nel modello RBAC, agli amministratori classici viene assegnato il ruolo di Proprietario nell'ambito della sottoscrizione.

Il modello di autorizzazione più specifico (Controllo degli accessi in base al ruolo di Azure) è supportato solo dal portale di Azure e dalle API di Gestione risorse di Azure. Gli utenti e le applicazioni a cui vengono assegnati ruoli Controllo degli accessi in base al ruolo di Azure, nell'ambito di sottoscrizione/gruppo di risorse/risorsa, non possono usare il portale di gestione classico e le API di gestione del servizio Microsoft Azure.

### Autorizzazioni per le operazioni di gestione e per le operazioni sui dati
Il modello di autorizzazione specifico (Azure RBAC) è supportato solo per le operazioni di gestione delle risorse di Azure nel portale di Azure e nelle API di Gestione risorse di Azure. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. Ad esempio, le operazioni di creazione, lettura, aggiornamento ed eliminazione degli account di archiviazione possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sugli oggetti BLOB e sulle tabelle negli account di archiviazione non possono essere controllate con tale metodo. Allo stesso modo, le operazioni di creazione, lettura, aggiornamento ed eliminazione di un database SQL possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sulle tabelle del database non possono essere controllate con tale metodo.

## Gestione dell'accesso tramite il portale di Azure
### Visualizzare l'accesso
Selezionare le impostazioni di accesso nella sezione Informazioni di base del pannello **Gruppo di risorse**. Il pannello **Utenti** elenca tutti gli utenti, i gruppi e le applicazioni a cui è stato consentito l'accesso al gruppo di risorse. L'accesso viene assegnato nel gruppo di risorse o viene ereditato da un'assegnazione nella sottoscrizione padre.

![Pannello Utenti - Accesso ereditato e assegnato - Schermata](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE] Gli amministratori di sottoscrizione classici e i Coamministratori sono in effetti proprietari della sottoscrizione nel nuovo modello RBAC.


### Aggiungere un accesso
1. Fare clic sull’icona **Aggiungi** nel pannello **Utenti**. ![Pannello Aggiungi accesso - Selezionare un ruolo - Schermata](./media/role-based-access-control-configure/grant-access1.png)
2. Selezionare il ruolo che si desidera assegnare.
3. Cercare e selezionare l'utente, il gruppo o l'applicazione a cui si vuole concedere l'accesso.
4. Cercare nella directory gli utenti, i gruppi e le applicazioni che usano nomi visualizzati, indirizzi di posta elettronica e identificatori di oggetto.![Pannello Aggiungi utenti - Cerca - Schermata](./media/role-based-access-control-configure/grant-access2.png)

### Rimuovere un accesso
1. Nel pannello **Utenti**, selezionare l'assegnazione di ruolo che si desidera rimuovere.
2. Scegliere l’icona **Rimuovi** nel pannello Dettagli assegnazione.
3. Fare clic su **Sì** per confermare la rimozione.

![Pannello Utenti - Rimuovi dal ruolo - Schermata](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE] Le assegnazioni ereditate non possono essere rimosse dagli ambiti figlio. Passare all'ambito padre e rimuovere tali assegnazioni.

![Pannello Utenti - Ereditato Accesso Disabilita pulsante Rimuovi - Schermata](./media/role-based-access-control-configure/remove-access2.png)

## Gestire l'accesso tramite Azure PowerShell
L'accesso può essere gestito con i comandi di Azure RBAC negli strumenti di Azure PowerShell.

-	Usare `Get-AzureRmRoleDefinition` per elencare i ruoli RBAC disponibili per l'assegnazione e per controllare le operazioni a cui concedono l'accesso.

-	Usare `Get-AzureRmRoleAssignment` per elencare le assegnazioni di accesso del Controllo degli accessi in base al ruolo in uso nella sottoscrizione, nel gruppo di risorse o nella risorsa specificata. Usare il parametro `ExpandPrincipalGroups` per elencare le assegnazioni dell’accesso all'utente specificato e ai gruppi di cui l'utente è membro. Usare il parametro `IncludeClassicAdministrators` per elencare anche gli Amministratore di sottoscrizione classici e i Coamministratori.

-	Usare `New-AzureRmRoleAssignment` per concedere l'accesso a utenti, gruppi e applicazioni.

-	Usare `Remove-AzureRmRoleAssignment` per rimuovere l'accesso.

Vedere [Gestire l'accesso tramite Azure PowerShell](role-based-access-control-manage-access-powershell.md) per esempi più dettagliati della gestione dell'accesso tramite Azure PowerShell.

## Gestire l’accesso usando l'interfaccia della riga di comando di Azure
L'accesso può essere gestito con i comandi di Azure RBAC nell'interfaccia della riga di comando di Azure.

-	Usare `azure role list` per elencare i ruoli RBAC disponibili per l'assegnazione. Usare `azure role show` per controllare le operazioni a cui concedono l'accesso.

-	Usare `azure role assignment list` per elencare le assegnazioni di accesso del Controllo degli accessi in base al ruolo in uso nella sottoscrizione, nel gruppo di risorse o nella risorsa specificata. Usare l’opzione `expandPrincipalGroups` per elencare le assegnazioni dell’accesso all'utente specificato e ai gruppi di cui l'utente è membro. Usare il parametro `includeClassicAdministrators` per elencare anche gli Amministratore di sottoscrizione classici e i Coamministratori.

-	Usare `azure role assignment create` per concedere l'accesso a utenti, gruppi e applicazioni.

-	Usare `azure role assignment delete` per rimuovere l'accesso.

Vedere [Gestire l'accesso tramite l’interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md) per esempi più dettagliati di gestione dell'accesso tramite l’interfaccia di riga di comando di Azure.

## Gestire l'accesso tramite l'API REST
Per esempi più dettagliati della gestione dell'accesso con l'API REST, vedere [Gestione del controllo degli accessi in base al ruolo con l'API REST](role-based-access-control-manage-access-rest.md).

## Usare il Rapporto della cronologia di modifica degli accessi
Tutte le modifiche dell’accesso in corso nelle sottoscrizioni di Azure vengono registrate negli eventi di Azure.

### Creare un rapporto con Azure PowerShell
Per creare un report di chi ha concesso o revocato un tipo di accesso e a chi, in quale ambito all'interno delle sottoscrizioni di Azure, usare il comando di PowerShell seguente:

    `Get-AzureRMAuthorizationChangeLog`

### Creare un rapporto con l’interfaccia di riga di comando di Azure
Per creare un report di chi ha concesso o revocato un tipo di accesso e a chi, in quale ambito all'interno delle sottoscrizioni di Azure, usare il comando dell'interfaccia della riga di comando di Azure seguente:

    `azure authorization changelog`

> [AZURE.NOTE] Le modifiche dell’accesso possono essere interrogate per gli ultimi 90 giorni (in batch di 15 giorni).

L'esempio seguente elenca tutte le modifiche dell'accesso nella sottoscrizione per gli ultimi 7 giorni.

![PowerShell Get-AzureRMAuthorizationChangeLog - Schermata](./media/role-based-access-control-configure/access-change-history.png)

### Esportare le modifiche dell'accesso in un foglio di calcolo
È utile esportare le modifiche dell’accesso in un foglio di calcolo per la revisione.

![Log delle modifiche visualizzato come foglio di calcolo - Schermata](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure
Creare un ruolo personalizzato nel Controllo degli accessi in base al ruolo di Azure, se nessuno dei ruoli predefiniti soddisfa le esigenze di accesso specifiche. I ruoli personalizzati possono essere creati usando gli strumenti da riga di comando del Controllo degli accessi in base al ruolo in Azure PowerShell e l'interfaccia da riga di comando di Azure. Analogamente ai ruoli predefiniti, i ruoli personalizzati possono essere assegnati a utenti, gruppi e applicazioni nell'ambito della sottoscrizione, del gruppo di risorse e delle risorse.

Ecco un esempio di definizione di ruolo personalizzato, che consente il monitoraggio e il riavvio di macchine virtuali:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
### Actions
La proprietà Actions di un ruolo personalizzato specifica le operazioni di Azure a cui il ruolo concede l'accesso. Si tratta di una raccolta di stringhe di operazione che identificano operazioni a protezione diretta dei provider di risorse di Azure. Le stringhe di operazione che contengono caratteri jolly (*) concedono l'accesso a tutte le operazioni corrispondenti alla stringa di operazione. Ad esempio:

-	`*/read` concede l'accesso a operazioni di lettura per tutti i tipi di risorsa di tutti i provider di risorse di Azure.
-	`Microsoft.Network/*/read` concede l'accesso a operazioni di lettura per tutti i tipi di risorsa nel provider di risorse Microsoft.Network di Azure.
-	`Microsoft.Compute/virtualMachines/*` concede l'accesso a tutte le operazioni delle macchine virtuali e ai relativi tipi di risorse figlio.
-	`Microsoft.Web/sites/restart/Action` concede l'accesso per il riavvio dei siti Web.

Usare i comandi `Get-AzureRmProviderOperation` o `azure provider operations show` per elencare le operazioni dei provider di risorse di Azure. È anche possibile usare questi comandi per verificare la validità di una stringa di operazione e per espandere le stringhe di operazione con caratteri jolly.

![PowerShell Get-AzureRMProviderOperation - Schermata](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![Operazioni del provider di Azure nell'interfaccia della riga di comando di Azure - Schermata](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

### NotActions
Se il set di operazioni da consentire può essere espresso facilmente escludendo operazioni specifiche, invece di includere tutte le operazioni ad eccezione di quelle da escludere, usare la proprietà **NotActions** di un ruolo personalizzato. L'accesso effettivo concesso da un ruolo personalizzato viene calcolato escludendo le operazioni **NotActions** dalle operazioni di tipo **Actions**.

> [AZURE.NOTE] Se a un utente a cui è assegnato un ruolo che esclude un'operazione in **NotActions** viene assegnato un secondo ruolo che concede l'accesso alla stessa operazione, l'utente sarà autorizzato a eseguire l'operazione. **NotActions** non è una regola di negazione. È semplicemente un modo semplice per creare un set di operazioni consentite quando è necessario escludere operazioni specifiche.

### AssignableScopes
La proprietà **AssignableScopes** di un ruolo personalizzato specifica gli ambiti, ovvero sottoscrizioni, gruppi di risorse o risorse, entro i quali il ruolo personalizzato è disponibile per l'assegnazione a utenti, gruppi e applicazioni. Usando **AssignableScopes** è possibile rendere disponibile il ruolo personalizzato per l'assegnazione solo nelle sottoscrizioni o nei gruppi di risorse che lo richiedono, in modo da non complicare l'esperienza utente per le altre sottoscrizioni o gli altri gruppi di risorse.

> [AZURE.NOTE] È necessario usare almeno una sottoscrizione, un gruppo di risorse o un ID della risorsa.

La proprietà **AssignableScopes** di un ruolo personalizzato controlla anche chi può visualizzare, aggiornare ed eliminare il ruolo. Ecco alcuni ambiti assegnabili validi:

-	"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624": rende disponibile il ruolo per l'assegnazione in due sottoscrizioni.
-	"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e": rende disponibile il ruolo per l'assegnazione in una singola sottoscrizione.
-	"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network": rende disponibile il ruolo per l'assegnazione solo nel gruppo di risorse Network.

### Controllo dell'accesso ai ruoli personalizzati
La proprietà **AssignableScopes** dei ruoli personalizzati indica gli utenti autorizzati a visualizzare, modificare ed eliminare il ruolo.

**Chi può creare un ruolo personalizzato**: la creazione di un ruolo personalizzato riesce solo se l'utente che crea il ruolo è autorizzato a creare un ruolo personalizzato per tutti i valori **AssignableScopes** specificati. La creazione del ruolo personalizzato ha esito positivo solo se l'utente che crea il ruolo può eseguire un'operazione di tipo `Microsoft.Authorization/roleDefinition/write operation` su tutti i valori **AssignableScopes** del ruolo. I proprietari e gli amministratori dell'accesso utente delle sottoscrizioni, dei gruppi di risorse e delle risorse possono quindi creare ruoli personalizzati da usare in questi ambiti.

**Chi può modificare un ruolo personalizzato**: gli utenti autorizzati ad aggiornare i ruoli personalizzati per tutti i valori **AssignableScopes** di un ruolo possono modificare quel ruolo personalizzato. Gli utenti che possono eseguire un'operazione `Microsoft.Authorization/roleDefinition/write` su tutti i valori **AssignableScopes** di un ruolo personalizzato possono modificare quel ruolo personalizzato. Ad esempio, se un ruolo personalizzato può essere assegnato in due sottoscrizioni di Azure, ovvero nella relativa proprietà **AssignableScopes** sono specificate due sottoscrizioni, un utente deve essere proprietario o amministratore dell'accesso utente di entrambe le sottoscrizioni per potere modificare il ruolo personalizzato.

**Chi può visualizzare i ruoli personalizzati disponibili per l'assegnazione in un ambito**: gli utenti che possono eseguire l'operazione `Microsoft.Authorization/roleDefinition/read` a livello di ambito possono visualizzare i ruoli Controllo degli accessi in base al ruolo disponibili per l'assegnazione in tale ambito. Tutti i ruoli predefiniti nel Controllo degli accessi in base al ruolo di Azure consentono la visualizzazione dei ruoli disponibili per l'assegnazione.

<!---HONumber=AcomDC_0128_2016-->