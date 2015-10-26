<properties
	pageTitle="Controllo di accesso in base al ruolo di Azure Active Directory AD | Microsoft Azure"
	description="In questo articolo viene descritto il controllo di accesso in base al ruolo di Azure."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Controllo di accesso in base al ruolo di Azure Active Directory

## Controllo di accesso in base al ruolo
Il controllo di accesso in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Utilizzando RBAC, è possibile separare compiti all'interno del team di sviluppo e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro.

### Nozioni fondamentali della gestione degli accessi in Azure
Ogni sottoscrizione di Azure è associata a un’istanza di Azure Active Directory. Solo utenti, gruppi e applicazioni di tale directory possono ottenere l'accesso per gestire le risorse nella sottoscrizione di Azure, utilizzando il portale di gestione di Azure, gli strumenti da riga di comando di Azure e le API di gestione di Azure.

L’accesso viene concesso assegnando i ruoli RBAC appropriati a utenti, gruppi e applicazioni nell'ambito corretto. Per concedere l'accesso all'intera sottoscrizione, assegnare un ruolo nell'ambito della sottoscrizione. Per concedere l'accesso a un gruppo di risorse specifico all'interno di una sottoscrizione, assegnare un ruolo nell'ambito del gruppo di risorse. È possibile assegnare ruoli anche a risorse specifiche, quali siti Web, macchine virtuali e subnet, per concedere l'accesso solo a una risorsa.

![](./media/role-based-access-control-configure/overview.png)

Il ruolo RBAC assegnato a utenti, gruppi e applicazioni determina quali risorse l’utente (o l'applicazione) può gestire in tale ambito.

### Ruoli predefiniti di Azure RBAC
Azure RBAC dispone di tre ruoli di base che si applicano a tutti i tipi di risorsa: Proprietario, Collaboratore e Lettore. Proprietario dispone dell'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti. Collaboratore può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri utenti. Lettore può solo visualizzare le risorse di Azure esistenti. Il resto dei ruoli RBAC in Azure consente la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore di macchina virtuale consente la creazione e la gestione delle macchine virtuali, ma non consente la gestione della rete virtuale o della subnet a cui la macchina virtuale si connette.

[Ruoli predefiniti RBAC](role-based-access-built-in-roles.md) elenca i ruoli RBAC predefiniti disponibili in Azure. Per ogni ruolo, specifica le operazioni a cui un ruolo predefinito consente l'accesso.

### Gerarchia ed ereditarietà dell’accesso delle risorse di Azure
Ogni sottoscrizione in Azure appartiene a un'unica e sola directory, ogni gruppo di risorse appartiene a un’unica e sola sottoscrizione e ogni risorsa appartiene a un unico e solo gruppo di risorse. L’accesso che si concede all’ambito padre viene ereditato dall’ambito figlio. Se si concede il ruolo Lettore a un gruppo di Azure AD nell'ambito della sottoscrizione, i membri di tale gruppo saranno in grado di visualizzare tutti i gruppi di risorse e tutte le risorse nella sottoscrizione. Se si concede il ruolo di Collaboratore a un'applicazione nell'ambito del gruppo di risorse, essa sarà in grado di gestire le risorse di tutti i tipi in tale gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

### Azure RBAC e Amministratore sottoscrizione classico e Coamministratori
Amministratore sottoscrizione classico e Coamministratori hanno accesso completo alla sottoscrizione di Azure. Essi possono gestire le risorse utilizzando sia il portale classico (https://manage.windowsazure.com) e le API di Azure Service Manager), che il nuovo portale di gestione (https://portal.azure.com) e le nuove API di Gestione risorse di Azure. Nel modello RBAC, agli amministratori classici viene assegnato il ruolo di Proprietario nell'ambito della sottoscrizione.

Il modello di autorizzazione specifico (Azure RBAC) è supportato solo dal nuovo portale di gestione (https://portal.azure.com) e dalle API di Gestione risorse di Azure. Gli utenti e le applicazioni a cui vengono assegnati ruoli RBAC (nell'ambito di sottoscrizione/gruppo risorse/risorsa) non possono utilizzare il portale di gestione classico (http://manage.windowsazure.com) e le API di Azure Service Management.

### Autorizzazioni per le operazioni di gestione e per le operazioni di dati
Il modello di autorizzazione specifico (Azure RBAC) è supportato solo per le operazioni di gestione delle risorse di Azure nel portale di Azure e nelle API di Gestione risorse di Azure. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. Ad esempio, le operazioni di creazione, lettura, aggiornamento ed eliminazione degli account di archiviazione possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sugli oggetti BLOB e sulle tabelle negli account di archiviazione non possono essere controllate con tale metodo. Allo stesso modo, le operazioni di creazione, lettura, aggiornamento ed eliminazione di un database SQL possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sulle tabelle del database non possono essere controllate con tale metodo.

## Gestire gli accessi tramite il portale di gestione di Azure
### Visualizzare gli accessi
Selezionare le impostazioni di accesso nella sezione Essentials del pannello del gruppo di risorse. Il pannello **Utenti** elenca tutti gli utenti, gruppi e applicazioni a cui è stato concesso l'accesso al gruppo di risorse. L'accesso viene assegnato nel gruppo di risorse o viene ereditato da un'assegnazione nella sottoscrizione padre.

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE]Gli amministratori di sottoscrizione classici e i Coamministratori sono in effetti proprietari della sottoscrizione nel nuovo modello RBAC.

### Aggiungere un accesso
1. Fare clic sull’icona **Aggiungi** nel pannello **Utenti**. ![](./media/role-based-access-control-configure/grant-access1.png)
2. Selezionare il ruolo che si desidera assegnare.
3. Cercare e selezionare l'utente, gruppo o applicazione a cui si desidera concedere l'accesso.
4. Cercare la directory di utenti, gruppi e applicazioni che utilizzano nomi visualizzati, indirizzi di posta elettronica e identificatori di oggetto.![](./media/role-based-access-control-configure/grant-access2.png)

### Rimuovere un accesso
1. Nel pannello **Utenti**, selezionare l'assegnazione di ruolo che si desidera rimuovere.
2. Scegliere l’icona **Rimuovi** nel pannello Dettagli assegnazione.
3. Fare clic su **Sì** per confermare la rimozione.

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE]Le assegnazioni ereditate non possono essere rimosse dagli ambiti figlio. Passare all'ambito padre e rimuovere tali assegnazioni.


![](./media/role-based-access-control-configure/remove-access2.png)

## Gestire l'accesso tramite Azure PowerShell
L'accesso può essere gestito con i comandi di Azure RBAC negli strumenti di Azure PowerShell.

-	Utilizzare `Get-AzureRoleDefinition` per elencare i ruoli RBAC disponibili per l'assegnazione e per controllare le operazioni a cui concedono l'accesso.

-	Usare `Get-AzureRoleAssignment` per elencare le assegnazioni dell’accesso RBAC in uso nella sottoscrizione o nel gruppo di risorse o nella risorsa specificata. Usare il parametro `ExpandPrincipalGroups` per elencare le assegnazioni dell’accesso all'utente specificato e ai gruppi di cui l'utente è membro. Usare il parametro `IncludeClassicAdministrators` per elencare anche gli Amministratore di sottoscrizione classici e i Coamministratori.

-	Usare `New-AzureRoleAssignment` per concedere l'accesso a utenti, gruppi e applicazioni.

-	Usare `Remove-AzureRoleAssignment` per rimuovere l'accesso.

Vedere [Gestire l'accesso tramite Azure PowerShell](role-based-access-control-manage-access-powershell.md) per esempi più dettagliati della gestione dell'accesso tramite Azure PowerShell.

## Gestire l’accesso usando l'interfaccia della riga di comando di Azure
L'accesso può essere gestito con i comandi di Azure RBAC nell'interfaccia della riga di comando di Azure.

-	Usare `azure role list` per elencare i ruoli RBAC disponibili per l'assegnazione. Usare la visualizzazione del ruolo di Azure per controllare le operazioni a cui concedono l'accesso.

-	Usare `azure role assignment list` per elencare le assegnazioni dell’accesso RBAC in uso nella sottoscrizione o nel gruppo di risorse o nella risorsa specificata. Usare l’opzione `expandPrincipalGroups` per elencare le assegnazioni dell’accesso all'utente specificato e ai gruppi di cui l'utente è membro. Usare il parametro `includeClassicAdministrators` per elencare anche gli Amministratore di sottoscrizione classici e i Coamministratori.

-	Usare `azure role assignment create` per concedere l'accesso a utenti, gruppi e applicazioni.

-	Usare `azure role assignment delete` per rimuovere l'accesso.

Vedere [Gestire l'accesso tramite l’interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md) per esempi più dettagliati di gestione dell'accesso tramite l’interfaccia di riga di comando di Azure.

## Usare il Rapporto della cronologia di modifica degli accessi
Tutte le modifiche dell’accesso in corso nelle sottoscrizioni di Azure vengono registrate negli eventi di Azure.

### Creare un rapporto con Azure PowerShell
Per creare un rapporto di chi ha concesso o revocato quale tipo di accesso da e verso chi, in quale ambito all'interno delle sottoscrizioni di Azure, usare il comando PowerShell seguente:

    Get-AzureAuthorizationChangeLog

### Creare un rapporto con l’interfaccia di riga di comando di Azure
Per creare un rapporto di chi ha concesso o revocato quale tipo di accesso da e verso chi, in quale ambito all'interno delle sottoscrizioni di Azure, usare il comando dell’interfaccia di riga di comando di Azure (CLI) seguente:

    azure authorization changelog

> [AZURE.NOTE]Le modifiche dell’accesso possono essere interrogate per gli ultimi 90 giorni (in batch di 15 giorni).

Nell'esempio seguente vengono elencate tutte le modifiche dell’accesso nella sottoscrizione per gli ultimi 7 giorni.

![](./media/role-based-access-control-configure/access-change-history.png)

### Esportare le modifiche dell'accesso in un foglio di calcolo
È utile esportare le modifiche dell’accesso in un foglio di calcolo per la revisione.

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

<!---HONumber=Oct15_HO3-->