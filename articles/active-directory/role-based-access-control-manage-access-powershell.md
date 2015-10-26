<properties
	pageTitle="Gestire il controllo degli accessi in base al ruolo con Azure PowerShell | Microsoft Azure"
	description="Come gestire il controllo degli accessi in base al ruolo con Azure PowerShell, incluso come ottenere elenchi dei ruoli, assegnare i ruoli ed eliminare le assegnazioni di ruoli."
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

# Gestire il controllo degli accessi in base al ruolo con Azure PowerShell
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Elencare i ruoli Controllo degli accessi in base al ruolo
### Elencare tutti i ruoli disponibili
Per elencare i ruoli Controllo degli accessi in base al ruolo disponibili per l'assegnazione e controllare le operazioni a cui concedono l'accesso, usare:

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Elencare le azioni di un ruolo
Per elencare le azioni per un ruolo specifico, usare:

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Elencare l'accesso
### Elencare tutte assegnazioni di ruoli nella sottoscrizione selezionata.
Per elencare le assegnazioni dell'accesso per il controllo degli accessi in base al ruolo valide per la sottoscrizione, la risorsa o il gruppo di risorse specificato, usare:

    Get-AzureRoleAssignment

###	Elencare le assegnazioni di ruoli valide per un gruppo di risorse
Per elencare le assegnazioni dell'accesso per un gruppo di risorse, usare:

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Elencare le assegnazioni di ruoli per un utente, inclusi quelli assegnati a gruppi di utenti
Per elencare le assegnazioni dell'accesso per l'utente specificato, nonché per i gruppi di cui è membro l'utente, usare:

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Elencare le assegnazioni di ruoli per l'amministratore e i coamministratori del servizio classici.
Per elencare le assegnazioni dell'accesso per l'amministratore e i coamministratori della sottoscrizione classici, usare:

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## Concedi accesso
### Cercare gli ID oggetto
Per usare le sequenze di comandi seguenti, è necessario individuare prima di tutto gli ID oggetto. Si presuppone che l'ID della sottoscrizione in uso sia già noto. In caso contrario, vedere [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) su MSDN.

#### Trovare l'ID oggetto per un gruppo di Azure AD
Per ottenere l'ID oggetto per un gruppo di Azure AD, usare:

    Get-AzureADGroup -SearchString <group name in quotes>

#### Trovare l'ID oggetto per un'entità servizio di Azure AD
Per ottenere l'ID oggetto per un'entità servizio di Azure AD, usare: Get-AzureADServicePrincipal -SearchString <service name in quotes>

### Assegnare un ruolo a un gruppo nell'ambito della sottoscrizione
Per concedere l'accesso a un gruppo nell'ambito della sottoscrizione, usare:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione
Per concedere l'accesso a un'applicazione nell'ambito della sottoscrizione, usare:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Assegnare un ruolo a un utente nell'ambito di un gruppo di risorse
Per concedere l'accesso a un utente nell'ambito di un gruppo di risorse, usare:

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Assegnare un ruolo a un gruppo nell'ambito delle risorse
Per concedere l'accesso a un gruppo nell'ambito delle risorse, usare:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Rimuovere un accesso
Per rimuovere l'accesso per utenti, gruppi e applicazioni, usare:

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Argomenti relativi a Controllo degli accessi in base al ruolo
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->