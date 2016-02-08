<properties
	pageTitle="Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure (Azure CLI) | Microsoft Azure"
	description="Informazioni su come gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure, ad esempio ottenere un elenco dei ruoli e delle relative azioni, nonché assegnare i ruoli nell'ambito della sottoscrizione e dell'applicazione."
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
	ms.date="01/22/2016"
	ms.author="kgremban"/>

# Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Elencare i ruoli Controllo degli accessi in base al ruolo
###	Elencare tutti i ruoli disponibili
Per elencare tutti i ruoli disponibili, usare

		azure role list

L'esempio seguente mostra l'elenco di *tutti i ruoli disponibili*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Elencare le azioni di un ruolo
Per elencare le azioni di un ruolo, usare:

    azure role show <role in quotes>

L'esempio seguente mostra le azioni dei ruoli *Contributor* e *Virtual Machine Contributor*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Visualizzazione dei ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Elencare l'accesso
###	Elencare le assegnazioni di ruoli valide per un gruppo di risorse
Per elencare le assegnazioni di ruoli valide per un gruppo di risorse, usare:

    azure role assignment list --resource-group <resource group name>

L'esempio seguente mostra le assegnazioni di ruoli valide per il gruppo *pharma-sales-projecforcast*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di assegnazione di ruoli per gruppo - Schermata](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Elencare le assegnazioni di ruoli per un utente, inclusi quelli assegnati ai gruppi di un utente

L'esempio seguente mostra le assegnazioni di ruoli valide per l'utente **sameert@aaddemo.com*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di assegnazione di ruoli per utente - Schermata](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	Concedere l'accesso
Dopo aver individuato il ruolo che si desidera assegnare, per concedere l'accesso usare:

    azure role assignment create

###	Assegnare un ruolo a un gruppo nell'ambito della sottoscrizione
Per assegnare un ruolo a un gruppo nell'ambito della sottoscrizione, usare:

	azure role assignment create --objId  <group's object id> --role <name of role> --scope <subscription/subscription id>

L'esempio seguente assegna il ruolo *Reader* a *Christine Koch's Team* nell'ambito *subscriptions*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli creata per gruppo - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	Assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione
Per assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione, usare:

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

L'esempio seguente assegna il ruolo *Contributor* a un'applicazione *Azure AD* nella sottoscrizione selezionata.

 ![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli creata per applicazione](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	Assegnare un ruolo a un utente nell'ambito di un gruppo di risorse
Per assegnare un ruolo a un utente nell'ambito di un gruppo di risorse, usare:

	azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

L'esempio seguente assegna il ruolo *Virtual Machine Contributor* all'utente **samert@aaddemo.com* nell'ambito di un gruppo di risorse *Pharma-Sales-ProjectForcast*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli creata per utente - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Assegnare un ruolo a un gruppo nell'ambito delle risorse
Per assegnare un ruolo a un gruppo nell'ambito di un gruppo di risorse, usare:

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

L'esempio seguente assegna il ruolo *Virtual Machine Contributor* a un gruppo *Azure AD* in una *subnet*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli creata per gruppo - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Rimuovere un accesso
Per rimuovere un'assegnazione di ruolo, usare:

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

L'esempio seguente rimuove l'assegnazione del ruolo *Virtual Machine Contributor* da **sammert@aaddemo.com* nel gruppo di risorse *Pharma-Sales-ProjectForcast*. Rimuove quindi l'assegnazione del ruolo da un gruppo nella sottoscrizione.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Eliminazione dell'assegnazione di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Creare un ruolo personalizzato
Per creare un ruolo personalizzato, usare il comando `azure role create`.

Nell'esempio seguente viene creato un ruolo personalizzato denominato *Operatore macchina virtuale* che concede l'accesso a tutte le operazioni di lettura dei provider di risorse *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede l'accesso per avviare, riavviare e monitorare le macchine virtuali. Il ruolo personalizzato può essere usato in due sottoscrizioni. In questo esempio viene usato un file json come input.

![JSON - Definizione di ruolo personalizzato - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Creazione di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Modificare un ruolo personalizzato

Per modificare un ruolo personalizzato, usare il comando `azure role show` per recuperare la definizione di ruolo. Apportare quindi le modifiche desiderate alla definizione del ruolo. Infine, usare `azure role set` per salvare la definizione del ruolo modificata.

L'esempio seguente aggiunge l'operazione Microsoft.Insights/diagnosticSettings/* alle **Azioni** e una sottoscrizione di Azure alla proprietà **AssignableScopes** del ruolo personalizzato Operatore macchina virtuale.

![JSON - Modifica della definizione di ruolo personalizzata - Schermata](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Impostazione dei ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato, usare prima di tutto il comando `azure role show` per determinare l'**ID** del ruolo. Usare quindi il comando `azure role delete` per eliminare il ruolo specificando l'**ID**.

Nell'esempio seguente viene rimosso il ruolo personalizzato *Operatore macchina virtuale*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Eliminazione dei ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## Elencare ruoli personalizzati

Per elencare i ruoli disponibili per l'assegnazione a un ambito, usare il comando `azure role list`.

Nell'esempio seguente vengono elencati tutti i ruoli disponibili per l'assegnazione nella sottoscrizione selezionata.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

Nell'esempio seguente il ruolo personalizzato *Operatore macchina virtuale* non è disponibile nella sottoscrizione *Production4* poiché la sottoscrizione non si trova nella proprietà **AssignableScopes** del ruolo.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco dei ruoli di Azure per i ruoli personalizzati - Schermata](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## Argomenti relativi a Controllo degli accessi in base al ruolo
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0128_2016-->