<properties
	pageTitle="Gestione del controllo di accesso basato sui ruoli con Windows PowerShell"
	description="Gestione del controllo di accesso basato sui ruoli con Windows PowerShell"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="kgremban"/>

# Gestione del controllo di accesso basato sui ruoli con Windows PowerShell #

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](role-based-access-control-xplat-cli.md)

Nel portale di Azure e nell'API di Gestione risorse di Azure, il controllo di accesso basato sui ruoli (RBAC) consente di gestire con estrema precisione l'accesso alla propria sottoscrizione. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

In questa esercitazione vengono fornite informazioni sull'utilizzo di Windows PowerShell per la gestione di RBAC. Verrà inoltre descritto il processo di creazione e controllo delle assegnazioni dei ruoli.

**Tempo previsto per il completamento:** 15 minuti

## Prerequisiti

Per poter utilizzare Windows PowerShell per gestire RBAC, è necessario disporre dei seguenti programmi:

- Windows PowerShell, versione 3.0 o 4.0. Per individuare la versione di Windows PowerShell, digitare `$PSVersionTable` e verificare che il valore di `PSVersion` sia 3.0 o 4.0. Per installare una versione compatibile, vedere [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

- Azure PowerShell 0.8.8 o versione successiva. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell.md).

Questa esercitazione è stata ideata per gli utenti di Windows PowerShell non esperti, ma presuppone che si conoscano i concetti di base, come i moduli, i cmdlet e le sessioni. Per altre informazioni su Windows PowerShell, vedere [Introduzione a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Per informazioni dettagliate sui cmdlet usati in questa esercitazione, eseguire il cmdlet `Get-Help`.

	Get-Help <cmdlet-name> -Detailed

Ad esempio, per informazioni sul cmdlet `Add-AzureAccount` digitare:

	Get-Help Add-AzureAccount -Detailed

Vedere anche le esercitazioni seguenti per acquisire familiarità con la configurazione e l'uso di Gestione risorse di Azure in Windows PowerShell:

- [Come installare e configurare Azure PowerShell](../install-configure-powershell.md)
- [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md)


## Connettersi alle sottoscrizioni

Poiché il controllo di accesso basato sui ruoli funziona solo con Gestione risorse di Azure, la prima cosa da fare è passare alla modalità Gestione risorse di Azure.

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Per maggiori informazioni, vedere [Utilizzo di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Per connettersi alle sottoscrizioni Azure, digitare:

    PS C:\> Add-AzureAccount

Nel controllo del browser a comparsa immettere il nome utente e la password dell'account Azure. PowerShell recupera tutte le sottoscrizioni esistenti per l'account e utilizza la prima come predefinita. Si noti che con RBAC sarà possibile ottenere le sottoscrizioni solo se si dispone di alcune autorizzazioni, sia come coamministratori che come aventi assegnazioni di ruolo.

Se sono disponibili più sottoscrizioni e si vuole passare a un'altra, usare i comandi seguenti:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Per maggiori informazioni, consultare [Come installare e configurare Azure PowerShell](../install-configure-powershell.md).

## Controllare le assegnazioni di ruoli esistenti

Controllare ora quali assegnazioni di ruoli esistono già nella sottoscrizione. Digitare:

    PS C:\> Get-AzureRoleAssignment

Verranno restituite tutte le assegnazioni di ruoli nella sottoscrizione. Si notino due cose:

1. È necessario avere accesso in lettura a livello di sottoscrizione.
2. Se la sottoscrizione conta numerose assegnazioni di ruoli, recuperarle tutte potrebbe richiedere qualche tempo.

È anche possibile controllare le assegnazioni di ruoli esistenti per una determinata definizione di ruolo, in un dato ambito e per un utente specifico. Digitare:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Verranno restituite tutte le assegnazioni di ruolo per un determinato utente nel tenant di AD con assegnazione del ruolo "Owner" per il gruppo di risorse "group1". L'assegnazione del ruolo può avere due origini:

1. Un'assegnazione del ruolo di "Owner" all'utente per il gruppo di risorse.
2. Un'assegnazione del ruolo "Proprietario" all'utente per il livello padre del gruppo di risorse, in questo caso la sottoscrizione, perché se si ha un'autorizzazione per un livello padre si avranno le stesse autorizzazioni anche per tutti i relativi elementi secondari.

Tutti i parametri di questo cmdlet sono facoltativi. È possibile combinarli per controllare le assegnazioni dei ruoli con filtri diversi.

## Creare un'assegnazione di ruolo

Per creare un'assegnazione di ruolo, è necessario considerare quanto segue:

A chi assegnare il ruolo: è possibile usare i seguenti cmdlet di Azure Active Directory per vedere quali utenti, gruppi ed entità di servizio sono presenti nel tenant di AD.

    PS C:\> Get-AzureADUser
	PS C:\> Get-AzureADGroup
	PS C:\> Get-AzureADGroupMember
	PS C:\> Get-AzureADServicePrincipal

Quale ruolo assegnare: è possibile usare il cmdlet seguente per vedere le definizioni di ruolo supportate.

    PS C:\> Get-AzureRoleDefinition

Quale ambito assegnare: sono disponibili tre livelli per gruppo di risorse di ambiti - La sottoscrizione corrente - Un gruppo di risorse, per ottenere un elenco di gruppi di risorse, digitare `PS C:\> Get-AzureResourceGroup` - Una risorsa, per ottenere un elenco di risorse, digitare `PS C:\> Get-AzureResource`

Usare quindi `New-AzureRoleAssignment` per creare un'assegnazione di ruolo. Ad esempio:

	#This will create a role assignment at the current subscription level for a user as a reader.
	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Reader

	#This will create a role assignment at a resource group level.
	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Contributor -ResourceGroupName group1

	#This will create a role assignment for a group at a resource group level.
	PS C:\> New-AzureRoleAssignment -ObjectID <group object ID> -RoleDefinitionName Reader -ResourceGroupName group1

	#This will create a role assignment at a resource level.
	PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId


## Verificare le autorizzazioni

Dopo aver controllato che il proprio account disponga di alcune assegnazioni di ruoli, è possibile visualizzare le autorizzazioni concesse da tali assegnazioni eseguendo:

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Questi due cmdlet restituiranno solo i gruppi di risorse o le risorse in cui si dispone dell'autorizzazione di lettura. Inoltre, verranno visualizzate anche le autorizzazioni di cui si dispone.

Quando si prova ad eseguire altri cmdlet come `New-AzureResourceGroup`, verrà visualizzato un errore di accesso negato se non si ha l'autorizzazione adeguata.

## Passaggi successivi

Per altre informazioni sulla gestione del controllo di accesso basato sui ruoli con Windows PowerShell e argomenti correlati:

- [Controllo di accesso basato sui ruoli in Microsoft Azure](../role-based-access-control-configure.md)
- [Cmdlet di Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): informazioni sull'uso dei cmdlet nel modulo AzureResourceManager.
- [Uso dei gruppi di risorse per la gestione delle risorse di Azure](../azure-preview-portal-using-resource-groups.md): informazioni su come creare e gestire gruppi di risorse nel portale di gestione di Azure.
- [Blog di Azure](http://blogs.msdn.com/windowsazure): informazioni sulle nuove funzionalità di Azure.
- [Blog di Windows PowerShell](http://blogs.msdn.com/powershell): informazioni sulle nuove funzionalità di Windows PowerShell.
- [Blog "Hey, Scripting Guy!"](http://blogs.technet.com/b/heyscriptingguy/): suggerimenti e consigli basati sull'esperienza dei membri della community di Windows PowerShell.
- [Configurazione del controllo di accesso basato sui ruoli usando l'interfaccia della riga di comando di Azure](role-based-access-control-xplat-cli-install.md)
- [Risoluzione dei problemi relativi al controllo di accesso basato sui ruoli](role-based-access-control-troubleshooting.md)

<!---HONumber=AcomDC_0128_2016-->