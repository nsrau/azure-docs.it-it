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
	ms.date="01/04/2016"
	ms.author="inhenk"/>

# Gestire il controllo degli accessi in base al ruolo con Azure PowerShell
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Elencare i ruoli Controllo degli accessi in base al ruolo
### Elencare tutti i ruoli disponibili
Per elencare i ruoli Controllo degli accessi in base al ruolo disponibili per l'assegnazione e controllare le operazioni a cui concedono l'accesso, usare:

		Get-AzureRmRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Elencare le azioni di un ruolo
Per elencare le azioni per un ruolo specifico, usare:

    Get-AzureRmRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Elencare l'accesso
### Elencare tutte assegnazioni di ruoli nella sottoscrizione selezionata.
Per elencare le assegnazioni dell'accesso per il controllo degli accessi in base al ruolo valide per la sottoscrizione, la risorsa o il gruppo di risorse specificato, usare:

    Get-AzureRmRoleAssignment

###	Elencare le assegnazioni di ruoli valide per un gruppo di risorse
Per elencare le assegnazioni dell'accesso per un gruppo di risorse, usare:

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Elencare le assegnazioni di ruoli per un utente, inclusi quelli assegnati a gruppi di utenti
Per elencare le assegnazioni dell'accesso per l'utente specificato, nonché per i gruppi di cui è membro l'utente, usare:

    Get-AzureRmRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Elencare le assegnazioni di ruoli per l'amministratore e i coamministratori del servizio classici.
Per elencare le assegnazioni dell'accesso per l'amministratore e i coamministratori della sottoscrizione classici, usare:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Concedi accesso
### Cercare gli ID oggetto
Per usare le sequenze di comandi seguenti, è necessario individuare prima di tutto gli ID oggetto. Si presuppone che l'ID della sottoscrizione in uso sia già noto. In caso contrario, vedere [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) su MSDN.

#### Trovare l'ID oggetto per un gruppo di Azure AD
Per ottenere l'ID oggetto per un gruppo di Azure AD, usare:

    Get-AzureRmADGroup -SearchString <group name in quotes>

#### Trovare l'ID oggetto per un'entità servizio di Azure AD
Per ottenere l'ID oggetto per un'entità servizio di Azure AD, usare: Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Assegnare un ruolo a un gruppo nell'ambito della sottoscrizione
Per concedere l'accesso a un gruppo nell'ambito della sottoscrizione, usare:

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione
Per concedere l'accesso a un'applicazione nell'ambito della sottoscrizione, usare:

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Assegnare un ruolo a un utente nell'ambito di un gruppo di risorse
Per concedere l'accesso a un utente nell'ambito di un gruppo di risorse, usare:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Assegnare un ruolo a un gruppo nell'ambito delle risorse
Per concedere l'accesso a un gruppo nell'ambito delle risorse, usare:

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Rimuovere un accesso
Per rimuovere l'accesso per utenti, gruppi e applicazioni, usare:

    Remove-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Creare un ruolo personalizzato
Per creare un ruolo personalizzato, usare il comando `New-AzureRmRoleDefinition`.

Nell'esempio seguente viene creato un ruolo personalizzato denominato *Operatore macchina virtuale* che concede l'accesso a tutte le operazioni di lettura dei provider di risorse *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede l'accesso per avviare, riavviare e monitorare le macchine virtuali. Il ruolo personalizzato può essere usato in due sottoscrizioni.

![](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## Modificare un ruolo personalizzato
Per modificare un ruolo personalizzato, usare il comando `Get-AzureRmRoleDefinition` per recuperare la definizione di ruolo. Apportare quindi le modifiche desiderate alla definizione del ruolo. Infine, usare il comando `Set-AzureRmRoleDefinition` per salvare la definizione del ruolo modificata.

Nell'esempio seguente viene aggiunta l'operazione `Microsoft.Insights/diagnosticSettings/*` al ruolo personalizzato *Operatore macchina virtuale*.

![](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

Nell'esempio seguente viene aggiunta una sottoscrizione di Azure agli ambiti assegnabili del ruolo personalizzato Operatore macchina virtuale.

![](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato, usare il comando `Remove-AzureRmRoleDefinition`.

Nell'esempio seguente viene rimosso il ruolo personalizzato *Operatore macchina virtuale*.

![](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## Elencare ruoli personalizzati
Per elencare i ruoli disponibili per l'assegnazione a un ambito, usare il comando `Get-AzureRmRoleDefinition`.

Nell'esempio seguente vengono elencati tutti i ruoli disponibili per l'assegnazione nella sottoscrizione selezionata.

![](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

Nell'esempio seguente il ruolo personalizzato *Operatore macchina virtuale* non è disponibile nella sottoscrizione *Production4* poiché la sottoscrizione non si trova nella proprietà **AssignableScopes** del ruolo.

![](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## Argomenti relativi a Controllo degli accessi in base al ruolo
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0107_2016-->