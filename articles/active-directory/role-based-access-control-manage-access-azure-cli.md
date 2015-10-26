<properties
	pageTitle="Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure (Azure CLI) | Microsoft Azure"
	description="Informazioni su come gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure, ad esempio ottenere un elenco dei ruoli e delle relative azioni, nonché assegnare i ruoli nell'ambito della sottoscrizione e dell'applicazione."
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

# Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure (Azure CLI)
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Elencare i ruoli Controllo degli accessi in base al ruolo
###	Elencare tutti i ruoli disponibili
Per elencare tutti i ruoli disponibili, usare

		azure role list

L'esempio seguente mostra l'elenco di *tutti i ruoli disponibili*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Elencare le azioni di un ruolo
Per elencare le azioni di un ruolo, usare:

    azure role show <role in quotes>

L'esempio seguente mostra le azioni dei ruoli *Contributor* e *Virtual Machine Contributor*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Elencare l'accesso
###	Elencare le assegnazioni di ruoli valide per un gruppo di risorse
Per elencare le assegnazioni di ruoli valide per un gruppo di risorse, usare:

    azure role assignment list --resource-group <resource group name>

L'esempio seguente mostra le assegnazioni di ruoli valide per il gruppo *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Elencare le assegnazioni di ruoli per un utente, inclusi quelli assegnati ai gruppi di un utente

L'esempio seguente mostra le assegnazioni di ruoli valide per il gruppo *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	Concedere l'accesso
Dopo aver individuato il ruolo che si desidera assegnare, per concedere l'accesso usare:

    azure role assignment create

###	Assegnare un ruolo a un gruppo nell'ambito della sottoscrizione
Per assegnare un ruolo a un gruppo nell'ambito della sottoscrizione, usare:

   azure role assignment create --objId <ID oggetto gruppo> --role <name of role> --scope <sottoscrizione/ID sottoscrizione>

L'esempio seguente assegna il ruolo *Reader* a *Christine Koch's Team* nell'ambito *subscriptions*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	Assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione
Per assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione, usare:

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

L'esempio seguente assegna il ruolo *Contributor* a un'applicazione *Azure AD* nella sottoscrizione selezionata.

 ![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	Assegnare un ruolo a un utente nell'ambito di un gruppo di risorse
Per assegnare un ruolo a un utente nell'ambito di un gruppo di risorse, usare:

    azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

L'esempio seguente assegna il ruolo *Virtual Machine Contributor* all'utente **samert@aaddemo.com* nell'ambito di un gruppo di risorse *Pharma-Sales-ProjectForcast*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Assegnare un ruolo a un gruppo nell'ambito delle risorse
Per assegnare un ruolo a un gruppo nell'ambito di un gruppo di risorse, usare:

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

L'esempio seguente assegna il ruolo *Virtual Machine Contributor* a un gruppo *Azure AD* in una *subnet*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Rimuovere un accesso
Per rimuovere un'assegnazione di ruolo, usare:

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

L'esempio seguente rimuove l'assegnazione del ruolo *Virtual Machine Contributor* da **sammert@aaddemo.com* nel gruppo di risorse *Pharma-Sales-ProjectForcast*. Rimuove quindi l'assegnazione del ruolo da un gruppo nella sottoscrizione.

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Argomenti relativi a Controllo degli accessi in base al ruolo
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->