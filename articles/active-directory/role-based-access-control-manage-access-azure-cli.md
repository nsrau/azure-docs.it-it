<properties
	pageTitle="Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure (Azure CLI) | Microsoft Azure"
	description="Informazioni su come gestire il controllo degli accessi in base al ruolo (RBAC) con l'interfaccia della riga di comando di Azure, ad esempio ottenere un elenco dei ruoli e delle relative azioni, nonché assegnare i ruoli nell'ambito della sottoscrizione e dell'applicazione."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>  

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/22/2016"
	ms.author="kgremban"/>  

# Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

È possibile usare il controllo degli accessi in base al ruolo nel portale di Azure e nell'API di Azure Resource Manager per gestire l'accesso alla sottoscrizione e alle risorse a un livello estremamente specifico. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

Per usare l'interfaccia della riga di comando di Azure per gestire il controllo degli accessi in base al ruolo, è necessario disporre di quanto segue:

- Usare la versione 0.8.8 o successiva dell'interfaccia della riga di comando di Azure. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).
- Azure Resource Manager nell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Uso dell'interfaccia della riga di comando di Azure con Resource Manager](../xplat-cli-azure-resource-manager.md)

## Elenco dei ruoli

###	Elencare tutti i ruoli disponibili
Per elencare tutti i ruoli disponibili, usare:

		azure role list

L'esempio seguente mostra l'elenco di *tutti i ruoli disponibili*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Elencare le azioni di un ruolo
Per elencare le azioni di un ruolo, usare:

    azure role show "<role name>"

L'esempio seguente mostra le azioni dei ruoli *Contributor* e *Virtual Machine Contributor*.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Visualizzazione dei ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Elencare l'accesso
###	Elencare le assegnazioni di ruoli valide per un gruppo di risorse
Per elencare le assegnazioni di ruolo in un gruppo di risorse, usare:

    azure role assignment list --resource-group <resource group name>

L'esempio seguente indica le assegnazioni di ruolo nel gruppo *pharma-sales-projecforcast*.

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di assegnazione di ruoli per gruppo - Schermata](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Elencare i ruoli assegnati a un utente
Per avere un elenco dei ruoli assegnati a un determinato utente e delle assegnazioni ai gruppi di utenti, utilizzare:

	azure role assignment list --signInName <user email>

È anche possibile visualizzare le assegnazioni di ruolo ereditate dai gruppi modificando il comando:

	azure role assignment list --expandPrincipalGroups --signInName <user email>

L'esempio seguente indica le assegnazioni di ruolo concesse all'utente *sameert@aaddemo.com*. Include i ruoli assegnati direttamente all'utente, oltre ai ruoli ereditati dai gruppi.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di assegnazione di ruoli per utente - Schermata](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)  

##	Concedere l'accesso
Per concedere l'accesso dopo aver individuato il ruolo da assegnare, usare:

    azure role assignment create

###	Assegnare un ruolo a un gruppo nell'ambito della sottoscrizione
Per assegnare un ruolo a un gruppo nell'ambito della sottoscrizione, usare:

	azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

L'esempio seguente assegna il ruolo *Reader* a *Christine Koch's Team* nell'ambito *subscriptions*.


![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli creata per gruppo - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)  

###	Assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione
Per assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione, usare:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

L'esempio seguente assegna il ruolo *Collaboratore* a un'applicazione *Azure AD* nella sottoscrizione selezionata.

 ![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli creata per applicazione](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)  

###	Assegnare un ruolo a un utente nell'ambito di un gruppo di risorse
Per assegnare un ruolo a un utente nell'ambito di un gruppo di risorse, usare:

	azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

L'esempio seguente assegna il ruolo *Collaboratore Macchina virtuale* all'utente *samert@aaddemo.com* nell'ambito di un gruppo di risorse *Pharma-Sales-ProjectForcast*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli creata per utente - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Assegnare un ruolo a un gruppo nell'ambito delle risorse
Per assegnare un ruolo a un gruppo nell'ambito di un gruppo di risorse, usare:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

L'esempio seguente assegna il ruolo *Collaboratore Macchina virtuale* a un gruppo *Azure AD* in una *subnet*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli creata per gruppo - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Rimuovere un accesso
Per rimuovere un'assegnazione di ruolo, usare:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

L'esempio seguente rimuove l'assegnazione del ruolo *Collaboratore Macchina virtuale* dall'utente *sammert@aaddemo.com* nel gruppo di risorse *Pharma-Sales-ProjectForcast*. Nell'esempio viene quindi rimossa l'assegnazione del ruolo da un gruppo nella sottoscrizione.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Eliminazione dell'assegnazione di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)  

## Creare un ruolo personalizzato
Per creare un ruolo personalizzato, usare:

	azure role create --inputfile <file path>

Nell'esempio seguente viene creato il ruolo personalizzato denominato *Operatore macchina virtuale*. Il ruolo personalizzato concede l'accesso a tutte le operazioni di lettura dei provider di risorse *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede l'accesso per avviare, riavviare e monitorare le macchine virtuali. Il ruolo personalizzato può essere usato in due sottoscrizioni. In questo esempio viene usato un file JSON come input.

![JSON - Definizione di ruolo personalizzato - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)  

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Creazione di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Modificare un ruolo personalizzato

Per modificare un ruolo personalizzato, usare il comando `azure role show` per recuperare la definizione di ruolo. Successivamente, apportare le modifiche desiderate al file di definizione del ruolo. Usare infine `azure role set` per salvare la definizione del ruolo modificata.

	azure role set --inputfile <file path>

L'esempio seguente aggiunge l'operazione *Microsoft.Insights/diagnosticSettings/* alle **Azioni** e una sottoscrizione di Azure alla proprietà **AssignableScopes** del ruolo personalizzato Operatore macchina virtuale.

![JSON - Modifica della definizione di ruolo personalizzata - Schermata](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)  

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Impostazione dei ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)  

## Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato, usare prima di tutto il comando `azure role show` per determinare l'**ID** del ruolo. Usare quindi il comando `azure role delete` per eliminare il ruolo specificando l'**ID**.

Nell'esempio seguente viene rimosso il ruolo personalizzato *Operatore macchina virtuale*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Eliminazione dei ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)  

## Elencare ruoli personalizzati

Per elencare i ruoli disponibili per l'assegnazione a un ambito, usare il comando `azure role list`.

L'esempio seguente elenca tutti i ruoli disponibili per l'assegnazione nella sottoscrizione selezionata.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)  

Nell'esempio seguente il ruolo personalizzato *Operatore macchina virtuale* non è disponibile nella sottoscrizione *Production4* poiché la sottoscrizione non si trova nella proprietà **AssignableScopes** del ruolo.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco dei ruoli di Azure per i ruoli personalizzati - Schermata](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## Argomenti relativi a Controllo degli accessi in base al ruolo
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0810_2016-->