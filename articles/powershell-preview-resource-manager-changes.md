<properties
	pageTitle="Modifiche di Gestione risorse nell'anteprima di Azure PowerShell 1.0 | Microsoft Azure"
	description="Vengono descritte le modifiche che sono state apportate ai cmdlet di Gestione risorse nell'anteprima di Azure PowerShell 1.0."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="na"
	ms.date="10/09/2015"
	ms.author="gauravbh;tomfitz"/>

#Modifiche dei cmdlet di PowerShell di gestione di Gestione risorse di Azure

Nell'ambito del rilascio dell'anteprima di Azure PowerShell 1.0, sono stati apportati alcuni miglioramenti ai cmdlet di gestione. Questi miglioramenti si aggiungono alle modifiche dei nomi dei cmdlet che fanno parte dell'anteprima 1.0. Alcuni di questi miglioramenti comportano notevoli cambiamenti e potrebbero causare problemi per il funzionamento degli script esistenti. L'obiettivo di queste modifiche è migliorare l'esperienza degli utenti. Microsoft è interessata a ricevere commenti e suggerimenti su queste modifiche. Il feedback verrà incorporato in Azure PowerShell 1.0. Tutti gli utenti sono pertanto invitati a inviare i propri commenti dopo avere provato i nuovi cmdlet.

##Separazione della distribuzione dei modelli dai gruppi di risorse

Nella versione 0.9.8 tutti i parametri di distribuzione dei modelli erano presenti anche nei cmdlet per i gruppi di risorse. Pertanto, in New-AzureResourceGroup era possibile creare un nuovo gruppo di risorse, nonché fornire i dettagli dei modelli da distribuire. La stessa funzionalità di distribuzione dei modelli era anche presente in New-AzureResourceGroupDeployment. Nell'anteprima 1.0 queste funzionalità sono state separate. Ora New-AzureRMResourceGroup offre le funzionalità per la creazione di nuovi gruppi di risorse e New-AzureRmResourceGroupDeployment offre le funzionalità per la distribuzione del modello. È possibile usare il piping per impiegare insieme le due funzionalità. Questo rende i cmdlet più facili da comprendere e da usare.

##Cmdlet consolidati per i log di controllo

Per i log di controllo, in precedenza erano disponibili numerosi cmdlet per ottenere log in vari ambiti, ad esempio Get-AzureResourceProviderLog, Get-AzureResourceGroupLog, Get-AzureSubscriptionIdLog e Get-AzureResourceLog. Per ottenere i log, spesso era necessario eseguire una combinazione di cmdlet per i log. Questa esperienza non era ottimale. Queste funzionalità sono state consolidate in un singolo cmdlet, che può essere chiamato in diversi ambiti mediante i parametri. Ora è possibile chiamare Get-AzureRmLog con il parametro appropriato per specificare l'ambito.

Nella versione 0.9.8, per ottenere i log per un determinato gruppo di risorse, eseguire un cmdlet simile a seguente:

    Get-AzureResourceGroupLog -ResourceGroup <resource-group-name>

Per ottenere i log per una determinata risorsa, eseguire il cmdlet seguente:

     Get-AzureResourceLog -ResourceId
     /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>/providers/<provider-namespace>/
     <resource-name>

Nella versione 1.0 di anteprima, si otterranno le stesse informazioni eseguendo il cmdlet seguente. Per ottenere i log per un gruppo di risorse, eseguire il cmdlet seguente:

     Get-AzureRmLog -ResourceGroup <resource-group-name>
     
Per ottenere i log per una determinata risorsa, eseguire il cmdlet seguente:

     Get-AzureRmLog -ResourceId /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>
     /providers/<provider-namespace>/<resource-name>

##Modifiche apportate ai cmdlet Get per le risorse e i gruppi di risorse

Sono state incorporate modifiche ai cmdlet Get-AzureRmResource e Get-AzureRmResourceGroup, in modo che questi cmdlet ora eseguano direttamente query solo al provider di risorse. Le funzionalità per le query sulla cache sono state separate in nuovi cmdlet denominati Find-AzureRmResource*. Se si desidera trovare un gruppo di risorse con un tag specifico, è possibile usare il nuovo cmdlet Find-AzureRmResourceGroup. Con questa modifica, i parametri per l'esecuzione di query sui tag sono stati rimossi dai cmdlet Get-AzureRmResource e Get-AzureRmResourceGroup.

Nella versione 0.9.8, individuare tutti i gruppi di risorse che contengono un tag specifico ed eseguire il cmdlet seguente:

    Get-AzureResourceGroup -Tag <tag-object>

Nella versione 1.0 di anteprima, eseguire il cmdlet seguente per ottenere lo scenario precedente:

    Find-AzureRmResourceGroup -Tag <tag-object>
    
##Rimozione di Test-AzureResource e Test-AzureResourceGroup

Questi cmdlet non funzionavano in modo affidabile per ogni scenario e tipo di risorsa. È in corso lo sviluppo di una soluzione migliore per queste funzionalità. Nel frattempo è stato deciso di interrompere l'uso di cmdlet non affidabili. I cmdlet sono stati rimossi in questa versione e verrà aggiunta una soluzione affidabile in una versione futura.

##Miglioramenti di Get-AzureRmResourceProvider

Ora è possibile usare il cmdlet Get-AzureRmResourceProvider per ottenere informazioni sulla posizione per i provider di risorse. Questo cmdlet indicherà quali provider e tipi sono disponibili in una particolare area. Inoltre, è possibile usare questo cmdlet per ottenere l'elenco delle posizioni disponibili per un determinato provider. È stato eliminato il cmdlet Get-AzureLocation poiché è possibile ottenere tutte le informazioni sulla posizione tramite il cmdlet Get-AzureRmResourceProvider.

Nella versione 0.9.8, per ottenere l'elenco di tutti i percorsi supportati, eseguire il cmdlet seguente:

     Get-AzureLocation

Per ottenere lo stato della registrazione dei provider, eseguire il cmdlet seguente:

     Get-AzureProvider -ListAvailable

Nella versione 1.0 di anteprima, eseguire quanto riportato sopra usando un solo cmdlet, come illustrato di seguito:

     Get-AzureRmResourceProvider -Location <location>

Il cmdlet precedente filtrerà i risultati per visualizzare solo i provider e i tipi disponibili nel percorso specificato.

In alternativa, è possibile filtrare i risultati per visualizzare un determinato provider, come illustrato di seguito:

     Get-AzureRmResourceProvider -ProviderNamespace <provider-namespace>

Il cmdlet precedente filtrerà i risultati per visualizzare i percorsi supportati solo dal provider specificato.

##Cmdlet per i criteri

È stato aggiunto il supporto dei criteri a Gestione risorse di Azure. I cmdlet PowerShell per gestire i criteri sono stati aggiunti in questa versione. Per altre informazioni sui criteri, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

<!---HONumber=Oct15_HO4-->