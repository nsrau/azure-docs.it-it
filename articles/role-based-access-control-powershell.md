<properties pageTitle="Gestione del controllo di accesso basato sui ruoli con Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Gestione del controllo di accesso basato sui ruoli con Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="11/03/2014" ms.author="guayan" />

# Gestione del controllo di accesso basato sui ruoli con Windows PowerShell #

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/role-based-access-control-powershell.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/it-it/documentation/articles/role-based-access-control-xplat-cli.md" title="Cross-Platform CLI">Interfaccia della riga di comando multipiattaforma</a></div>

Nel portale di anteprima Azure e nell'API di Gestione risorse di Azure, il controllo di accesso basato sui ruoli (RBAC) consente di gestire con estrema precisione l'accesso alla propria sottoscrizione. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

In questa esercitazione vengono fornite informazioni sull'uso di Windows PowerShell per gestire Controllo degli accessi in base al ruolo. Verrà inoltre descritto il processo di creazione e controllo delle assegnazioni dei ruoli.

**Tempo previsto per il completamento:** 15 minuti

## Prerequisiti ##

Per poter usare Windows PowerShell per gestire RBAC, è necessario disporre dei seguenti programmi:

- Windows PowerShell, versione 3.0 o 4.0. Per individuare la versione di Windows PowerShell, digitare: `$PSVersionTable` e verificare che il valore di `PSVersion` sia 3.0 o 4.0. Per installare una versione compatibile, vedere [Windows Management Framework 3.0](http://www.microsoft.com/it-it/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/it-it/download/details.aspx?id=40855).

- Azure PowerShell 0.8.8 o versione successiva. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Come installare e configurare Microsoft Azure PowerShell](http://www.windowsazure.com/it-it/documentation/articles/install-configure-powershell/).

Questa esercitazione è stata ideata per gli utenti di Windows PowerShell non esperti, ma presuppone che si conoscano i concetti di base, come i moduli, i cmdlet e le sessioni. Per altre informazioni su Windows PowerShell, vedere [Introduzione a Windows PowerShell](http://technet.microsoft.com/it-it/library/hh857337.aspx).

Per informazioni dettagliate sui cmdlet usati in questa esercitazione, usare il cmdlet Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Ad esempio, per informazioni sul cmdlet Add-AzureAccount, digitare:

	Get-Help Add-AzureAccount -Detailed

Leggere inoltre le seguenti esercitazioni per acquisire familiarità con l'utilizzo di Gestione risorse di Azure in Windows PowerShell:

- [Come installare e configurare Azure PowerShell](http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/)
- [Uso di Windows PowerShell con Gestione risorse](http://azure.microsoft.com/it-it/documentation/articles/powershell-azure-resource-manager/)

## Contenuto dell'esercitazione: ##

* [Connettersi alle sottoscrizioni](#connect)
* [Controllare le assegnazioni di ruoli esistenti](#check)
* [Creare un'assegnazione di ruolo](#create)
* [Verificare le autorizzazioni](#verify)
* [Passaggi successivi](#next)

## <a id="connect"></a>Connettersi alle sottoscrizioni ##

Poiché RBAC funziona solo con Gestione risorse di Azure, occorre innanzitutto passare alla modalità Gestione risorse di Azure digitando:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](http://azure.microsoft.com/it-it/documentation/articles/powershell-azure-resource-manager/).

Per connettersi alle proprie sottoscrizioni di Azure, digitare:

    PS C:\> Add-AzureAccount

Nel controllo del browser a comparsa, immettere il nome utente e la password dell'account Azure. PowerShell recupera tutte le sottoscrizioni esistenti per l'account e usa la prima come predefinita. Si noti che con RBAC sarà possibile ottenere le sottoscrizioni solo se si dispone di alcune autorizzazioni, sia come coamministratori che come aventi assegnazioni di ruolo. 

Se si dispone di più sottoscrizioni e si desidera passare a un'altra, digitare:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/).

## <a id="check"></a>Controllare le assegnazioni di ruoli esistenti ##

Controllare ora quali assegnazioni di ruoli esistono già nella sottoscrizione. Digitare:

    PS C:\> Get-AzureRoleAssignment

Verranno restituite tutte le assegnazioni di ruoli nella sottoscrizione. Tenere presente le due considerazioni seguenti:

1. È necessario disporre dell'accesso in lettura a livello di sottoscrizione.
2. Se la sottoscrizione conta numerose assegnazioni di ruoli, recuperarle tutte potrebbe richiedere qualche tempo.

È possibile inoltre controllare le assegnazioni di ruoli esistenti per una determinata definizione di ruolo, in un dato ambito e per un utente specifico. Digitare:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Verranno restituite tutte le assegnazioni di ruolo per un determinato utente nel tenant di AD con assegnazione del ruolo "Owner" per il gruppo di risorse "group1". L'assegnazione del ruolo può avere due origini:

1. Un'assegnazione del ruolo di "Owner" all'utente per il gruppo di risorse.
2. Un'assegnazione del ruolo "Owner" all'utente per l'entità principale del gruppo di risorse (in questo caso, la sottoscrizione), poiché se si dispone di un'autorizzazione per un determinato livello si avranno le stesse autorizzazioni anche per tutti i relativi elementi secondari.

Tutti i parametri di questo cmdlet sono facoltativi. È possibile combinarli per controllare le assegnazioni dei ruoli con filtri diversi.

## <a id="create"></a>Creare un'assegnazione di ruolo ##

Per creare un'assegnazione di ruolo è necessario prendere in considerazione quanto segue

- A chi assegnare il ruolo: è possibile usare i seguenti cmdlet di Azure Active Directory per vedere quali utenti, gruppi ed entità di servizio sono presenti nel tenant di AD.

    `PS C:\> Get-AzureADUser
    PS C:\> Get-AzureADGroup
    PS C:\> Get-AzureADGroupMember
    PS C:\> Get-AzureADServicePrincipal` 

- Quale ruolo assegnare: è possibile usare il cmdlet seguente per vedere le definizioni di ruolo supportate.

    `PS C:\> Get-AzureRoleDefinition`

- Quale ambito assegnare: gli ambiti sono riconducibili a tre livelli

    - La sottoscrizione corrente
    - Un gruppo di risorse: per ottenere un elenco di gruppi di risorse, digitare `PS C:\> Get-AzureResourceGroup`
    - Una risorsa: per ottenere un elenco di risorse, digitare `PS C:\> Get-AzureResource`

Usare quindi `New-AzureRoleAssignment` per creare un'assegnazione di ruolo. Ad esempio:

 - In questo modo verrà creata per un utente un'assegnazione di ruolo al livello della sottoscrizione attuale come lettore.

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

- In questo modo verrà creata un'assegnazione di ruolo a livello di gruppo di risorse

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

- In questo modo verrà creata un'assegnazione di ruolo a livello di risorsa

    `PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <a id="verify"></a>Verificare le autorizzazioni ##

Dopo aver controllato che l'account disponga di alcune assegnazioni di ruoli, è possibile visualizzare le autorizzazioni accordate da tali assegnazioni di ruoli eseguendo

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Questi due cmdlet restituiranno solo i gruppi di risorse o le risorse in cui si dispone dell'autorizzazione di lettura. Inoltre, verranno visualizzate anche le autorizzazioni di cui si dispone.

Quando si tenterà di eseguire un altro cmdlet, ad esempio `New-AzureResourceGroup`, verrà restituito un errore di accesso negato se non si dispone dell'autorizzazione necessaria.

## <a id="next"></a>Passaggi successivi ##

Per altre informazioni sulla gestione del controllo di accesso basato sui ruoli con Windows PowerShell e argomenti correlati:
 
- [Controllo di accesso basato sui ruoli in Microsoft Azure](http://azure.microsoft.com/it-it/documentation/articles/role-based-access-control-configure/)
- [Cmdlet di Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): informazioni sull'uso dei cmdlet nel modulo AzureResourceManager.
- [Uso dei gruppi di risorse per la gestione delle risorse di Azure](http://azure.microsoft.com/it-it/documentation/articles/azure-preview-portal-using-resource-groups): informazioni su come creare e gestire i gruppi di risorse nel portale di gestione di Azure.
- [Blog di Azure](http://blogs.msdn.com/windowsazure): informazioni sulle nuove funzionalità di Azure.
- [Blog di Windows PowerShell](http://blogs.msdn.com/powershell): informazioni sulle nuove funzionalità di Windows PowerShell.
- [Blog "Hey, Scripting Guy!" ](http://blogs.technet.com/b/heyscriptingguy/): suggerimenti e consigli basati sull'esperienza dei membri della community di Windows PowerShell.
- [Configurazione del controllo di accesso basato sui ruoli usando l'interfaccia della riga di comando multipiattaforma](http://azure.microsoft.com/it-it/documentation/articles/role-based-access-control-xplat-cli/)
- [Risoluzione dei problemi relativi al controllo di accesso basato sui ruoli](http://azure.microsoft.com/it-it/documentation/articles/role-based-access-control-troubleshooting/)

<!--HONumber=35.2-->
