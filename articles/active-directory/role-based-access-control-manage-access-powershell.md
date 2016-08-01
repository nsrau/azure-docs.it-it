<properties
	pageTitle="Gestire il controllo degli accessi in base al ruolo con Azure PowerShell | Microsoft Azure"
	description="Come gestire il controllo degli accessi in base al ruolo con Azure PowerShell, come elencare ruoli, assegnare ruoli ed eliminare assegnazioni di ruoli."
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
	ms.date="07/14/2016"
	ms.author="kgremban"/>

# Gestire il controllo degli accessi in base al ruolo con Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)


Nel Portale di Azure e nell'API di Azure Resource Manager il controllo degli accessi in base al ruolo (RBAC, Role-Based Access Control) consente di gestire con estrema precisione l'accesso alla propria sottoscrizione. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

Per poter usare PowerShell per gestire il controllo degli accessi in base al ruolo, è necessario avere i programmi seguenti:

- Azure PowerShell 0.8.8 o versione successiva. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

- Cmdlet di Azure Resource Manager Installare i [cmdlet di Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx) in PowerShell.

## Elenco dei ruoli

### Elencare tutti i ruoli disponibili
Per elencare i ruoli Controllo degli accessi in base al ruolo disponibili per l'assegnazione e controllare le operazioni a cui concedono l'accesso, usare:

		Get-AzureRmRoleDefinition

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Elencare le azioni di un ruolo
Per elencare le azioni per un ruolo specifico, usare:

    Get-AzureRmRoleDefinition <role name>

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition per un ruolo specifico - Schermata](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Assegnazioni di accesso
Per elencare le assegnazioni di accesso al controllo degli accessi in base al ruolo, usare:

    Get-AzureRmRoleAssignment

###	Elencare le assegnazioni di ruolo in un ambito specifico
È possibile elencare le assegnazioni di accesso in base a sottoscrizione, gruppo di risorse o risorsa specificati. Ad esempio, per elencare tutte le assegnazioni attive per una gruppo di risorse, usare:

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition per un gruppo di risorse - Schermata](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Elencare i ruoli assegnati ad un utente
Per elencare tutti i ruoli assegnati a un utente specifico, compresi i ruoli assegnati ai gruppi ai quali appartiene, usare:

    Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition per un utente - Schermata](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Elencare le assegnazioni di ruoli per l'amministratore e i coamministratori del servizio classici.
Per elencare le assegnazioni dell'accesso per l'amministratore e i coamministratori della sottoscrizione classici, usare:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Concedere l'accesso
### Cercare gli ID oggetto
Per assegnare un ruolo, è necessario identificare l'oggetto (utente, gruppo o applicazione) e l'ambito.

Se non si conosce l'ID sottoscrizione, è possibile reperire tale informazione nel pannello **Sottoscrizioni** nel portale di Azure. In alternativa, vedere [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) su MSDN per sapere come richiederlo.

Per ottenere l'ID oggetto per un gruppo di Azure AD, usare:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Per ottenere l'ID oggetto per un'entità servizio di Azure AD, o applicazione, usare:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione
Per concedere l'accesso a un'applicazione nell'ambito della sottoscrizione, usare:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name in quotes> -Scope <subscription id>

![Controllo degli accessi in base al ruolo di PowerShell - New-AzureRmRoleAssignment - Schermata](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Assegnare un ruolo a un utente nell'ambito di un gruppo di risorse
Per concedere l'accesso a un utente nell'ambito di un gruppo di risorse, usare:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![Controllo degli accessi in base al ruolo di PowerShell - New-AzureRmRoleAssignment - Schermata](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Assegnare un ruolo a un gruppo nell'ambito delle risorse
Per concedere l'accesso a un gruppo nell'ambito delle risorse, usare:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![Controllo degli accessi in base al ruolo di PowerShell - New-AzureRmRoleAssignment - Schermata](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Rimuovere un accesso
Per rimuovere l'accesso per utenti, gruppi e applicazioni, usare:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![Controllo degli accessi in base al ruolo di PowerShell - Remove-AzureRmRoleAssignment - Schermata](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Creare un ruolo personalizzato
Per creare un ruolo personalizzato, usare il comando `New-AzureRmRoleDefinition`.

Quando si crea un ruolo personalizzato in PowerShell, è necessario iniziare con uno dei [ruoli predefiniti](role-based-access-built-in-roles.md). Modificare gli attributi e aggiungere Actions, notActions o gli ambiti desiderati e quindi salvare le modifiche come nuovo ruolo.

L'esempio seguente inizia con il ruolo *Collaboratore Macchina virtuali* e lo usa per creare un ruolo personalizzato denominato *Operatore macchina virtuale*. Il nuovo ruolo concede l'accesso a tutte le operazioni di lettura dei provider di risorse *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede l'accesso per avviare, riavviare e monitorare le macchine virtuali. Il ruolo personalizzato può essere usato in due sottoscrizioni.

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## Modificare un ruolo personalizzato
Per modificare un ruolo personalizzato, usare il comando `Get-AzureRmRoleDefinition` per recuperare la definizione di ruolo. Apportare quindi le modifiche desiderate alla definizione del ruolo. Infine, usare il comando `Set-AzureRmRoleDefinition` per salvare la definizione del ruolo modificata.

Nell'esempio seguente viene aggiunta l'operazione `Microsoft.Insights/diagnosticSettings/*` al ruolo personalizzato *Operatore macchina virtuale*.

![Controllo degli accessi in base al ruolo di PowerShell - Set-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

Nell'esempio seguente viene aggiunta una sottoscrizione di Azure agli ambiti assegnabili del ruolo personalizzato Operatore macchina virtuale.

![Controllo degli accessi in base al ruolo di PowerShell - Set-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## Eliminare un ruolo personalizzato

Per eliminare un ruolo personalizzato, usare il comando `Remove-AzureRmRoleDefinition`.

Nell'esempio seguente viene rimosso il ruolo personalizzato *Operatore macchina virtuale*.

![Controllo degli accessi in base al ruolo di PowerShell - Remove-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## Elencare ruoli personalizzati
Per elencare i ruoli disponibili per l'assegnazione a un ambito, usare il comando `Get-AzureRmRoleDefinition`.

Nell'esempio seguente vengono elencati tutti i ruoli disponibili per l'assegnazione nella sottoscrizione selezionata.

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

Nell'esempio seguente il ruolo personalizzato *Operatore macchina virtuale* non è disponibile nella sottoscrizione *Production4* poiché la sottoscrizione non si trova nella proprietà **AssignableScopes** del ruolo.

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## Vedere anche
- [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md) [AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0720_2016-->