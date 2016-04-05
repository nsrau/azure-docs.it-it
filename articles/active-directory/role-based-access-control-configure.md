<properties
	pageTitle="Controllo di accesso in base al ruolo di Azure Active Directory AD | Microsoft Azure"
	description="Introduzione alla gestione degli accessi con il Controllo degli accessi in base al ruolo di Azure nel portale di Azure. Usare le assegnazioni di ruolo per assegnare autorizzazioni nella directory."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/22/2016"
	ms.author="kgremban"/>

# Controllo degli accessi in base al ruolo di Azure

## Controllo degli accessi in base al ruolo
Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando il Controllo degli accessi in base al ruolo di Azure, è possibile separare compiti all'interno del team DevOps e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro. Questo articolo illustra le nozioni di base della gestione degli accessi, quindi consente di iniziare a usare subito il Controllo degli accessi in base al ruolo nel portale di Azure.

### Nozioni fondamentali della gestione degli accessi in Azure
Ogni sottoscrizione di Azure è associata a un'istanza di Azure Active Directory. Gli utenti, i gruppi e le applicazioni da tale directory possono gestire le risorse nella sottoscrizione di Azure. Questi diritti di accesso vengono concessi tramite il portale di Azure, gli strumenti da riga di comando di Azure o le API di gestione di Azure.

L'accesso viene concesso assegnando i ruoli Controllo degli accessi in base al ruolo appropriati a utenti, gruppi e applicazioni in un ambito specifico. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa. Un ruolo assegnato a un ambito padre concede anche l'accesso agli elementi figlio contenuti al suo interno. Ad esempio, un utente con accesso a un gruppo di risorse può gestire tutte le risorse che contiene, come siti Web, macchine virtuali e subnet.

![Diagramma della relazione tra gli elementi di Azure Active Directory](./media/role-based-access-control-configure/rbac_aad.png)

Il ruolo Controllo degli accessi in base al ruolo assegnato a utenti, gruppi e applicazioni determina quali risorse possono essere gestire in tale ambito.

### Ruoli predefiniti
Il Controllo degli accessi in base al ruolo di Azure include di tre ruoli di base che si applicano a tutti i tipi di risorsa:

- Proprietario dispone dell'accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti.
- Collaboratore può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri utenti.
- Lettore può visualizzare le risorse di Azure esistenti.

Il resto dei ruoli RBAC in Azure consente la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore Macchina virtuale consente all'utente di creare e gestire delle macchine virtuali, ma non di accedere alla rete virtuale o alla subnet a cui la macchina virtuale si connette.

[Controllo degli accessi in base al ruolo: ruoli predefiniti](role-based-access-built-in-roles.md) elenca i ruoli disponibili in Azure. Specifica le operazioni e l'ambito che ogni ruolo predefinito concede agli utenti. Se si vogliono definire ruoli personalizzati per un maggiore controllo, vedere come creare [ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md).

### Gerarchia delle risorse ed ereditarietà dell'accesso
- Ogni sottoscrizione in Azure appartiene a una sola directory.
- Ogni gruppo di risorse appartiene a una sola sottoscrizione.
- Ogni risorsa appartiene a un solo gruppo di risorse.

L’accesso che si concede all’ambito padre viene ereditato dall’ambito figlio. Se si assegna il ruolo Lettore a un gruppo di Azure AD nell'ambito della sottoscrizione, i membri di tale gruppo potranno visualizzare tutti i gruppi di risorse e tutte le risorse nella sottoscrizione. Se si concede il ruolo Collaboratore a un'applicazione nell'ambito del gruppo di risorse, questa potrà gestire risorse di tutti i tipi in tale gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

### Controllo degli accessi in base al ruolo di Azure e Amministratore sottoscrizione classico
I ruoli Amministratore sottoscrizione classico e Coamministratore hanno l'accesso completo alla sottoscrizione di Azure. Possono gestire le risorse tramite il [portale di Azure](https://portal.azure.com) e le API di Azure Resource Manager o il [portale di Azure classico](https://manage.windowsazure.com) e le API di gestione del servizio Microsoft Azure. Nel modello RBAC, agli amministratori classici viene assegnato il ruolo di Proprietario nell'ambito della sottoscrizione.

Il Controllo degli accessi in base al ruolo di Azure è supportato solo dal portale di Azure e dalle API di Azure Resource Manager. Gli utenti e le applicazioni a cui vengono assegnati ruoli Controllo degli accessi in base al ruolo di Azure non possono usare il portale di gestione classico e le API di gestione del servizio Microsoft Azure.

### Autorizzazioni per le operazioni di gestione e per le operazioni sui dati
Il Controllo degli accessi in base al ruolo di Azure è supportato solo per le operazioni di gestione delle risorse di Azure nel portale di Azure e nelle API di Azure Resource Manager. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. Ad esempio, le operazioni di creazione, lettura, aggiornamento ed eliminazione degli account di archiviazione possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sugli oggetti BLOB e sulle tabelle negli account di archiviazione non possono essere controllate con tale metodo. Allo stesso modo, le operazioni di creazione, lettura, aggiornamento ed eliminazione di un database SQL possono essere gestite tramite RBAC, mentre le stesse operazioni svolte sulle tabelle del database non possono essere controllate con tale metodo.

## Gestione dell'accesso tramite il portale di Azure
### Visualizzare l'accesso
È possibile visualizzare chi ha accesso a una risorsa, un gruppo di risorse o una sottoscrizione dal relativo pannello principale nel [portale di Azure](https://portal.azure.com). Ad esempio, si vuole vedere chi ha accesso a uno dei gruppi di risorse:

1. Selezionare l'icona **Gruppo di risorse** icona nella barra di spostamento a sinistra.
2. Selezionare il nome del gruppo di risorse che si vuole esaminare nel pannello **Gruppi di risorse**.
3. Selezionare l'icona **Utenti** in alto a destra del pannello del gruppo di risorse.
4. Il pannello **Utenti** elenca tutti gli utenti, i gruppi e le applicazioni a cui è stato consentito l'accesso al gruppo di risorse.

![Screenshot del pannello Utenti: accesso ereditato e assegnato](./media/role-based-access-control-configure/view-access.png)

Si noti che per alcuni utenti l'accesso è stato **Assegnato**, mentre per altri è stato **Ereditato**. L'accesso viene assegnato in modo specifico al gruppo di risorse oppure ereditato da un'assegnazione nella sottoscrizione padre.

> [AZURE.NOTE] Gli utenti con i ruoli Amministratore sottoscrizione classico e Coamministratore sono considerati proprietari della sottoscrizione nel nuovo modello Controllo degli accessi in base al ruolo.


### Aggiungere un accesso
Si concede l'accesso dall'interno della risorsa, del gruppo di risorse o della sottoscrizione. Questo sarà l'ambito dell'assegnazione di ruolo.

1. Fare clic sull'icona **Aggiungi** nel pannello **Utenti**. ![Screenshot del pannello Aggiungi accesso: Selezionare un ruolo](./media/role-based-access-control-configure/grant-access1.png)
2. Selezionare il ruolo che si desidera assegnare.
3. Selezionare l'utente, il gruppo o l'applicazione nella directory a cui si vuole concedere l'accesso. È possibile cercare nella directory usando nomi visualizzati, indirizzi di posta elettronica e identificatori di oggetto.![Screenshot del pannello Aggiungi utenti: casella di ricerca](./media/role-based-access-control-configure/grant-access2.png)

### Rimuovere un accesso

1. Nel pannello **Utenti**, selezionare l'assegnazione di ruolo che si desidera rimuovere.
2. Scegliere l’icona **Rimuovi** nel pannello Dettagli assegnazione.
3. Fare clic su **Sì** per confermare la rimozione.

![Screenshot del pannello Utenti: Rimuovi dal ruolo](./media/role-based-access-control-configure/remove-access1.png)

Le assegnazioni ereditate non possono essere rimosse dagli ambiti figlio. È necessario rimuovere l'assegnazione di ruolo dell'ambito padre.

![Screenshot del pannello Utenti: l'accesso ereditato disabilita il pulsante Rimuovi](./media/role-based-access-control-configure/remove-access2.png)

## Altri strumenti per gestire l'accesso
È possibile assegnare i ruoli e gestire l'accesso con i comandi del Controllo degli accessi in base al ruolo di Azure in strumenti diversi dal portale di Azure. Per altre informazioni sui prerequisiti e iniziare a usare i comandi del Controllo degli accessi in base al ruolo di Azure, usare i collegamenti seguenti.

- [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

## Passaggi successivi
- [Creare un report della cronologia delle modifiche relative all'accesso](role-based-access-control-access-change-history-report.md)
- Vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](role-based-access-built-in-roles.md)
- Definire i [ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0330_2016-->