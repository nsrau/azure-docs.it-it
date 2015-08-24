<properties 
	pageTitle="Uso dei tag per organizzare le risorse di Azure" 
	description="Mostra come applicare i tag per organizzare le risorse per la fatturazione e la gestione." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac"
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="AzurePortal" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/07/2015" 
	ms.author="tomfitz"/>


# Uso dei tag per organizzare le risorse di Azure

La Gestione risorse consente di organizzare logicamente le risorse tramite l'applicazione di tag. I tag sono costituiti da coppie chiave/valore che identificano le risorse con le proprietà definite. Per contrassegnare le risorse come appartenenti alla stessa categoria, applicare lo stesso tag a tali risorse.

Quando si visualizzano le risorse con un tag specifico, vengono visualizzate le risorse da tutti i gruppi di risorse. Non si è limitati unicamente alle risorse che appartengono allo stesso gruppo di risorse, cosa che consente di organizzare le risorse in modo indipendente rispetto alle relazioni di distribuzione. I tag possono essere particolarmente utili quando è necessario organizzare le risorse per la fatturazione o la gestione.

Ogni tag aggiunto a una risorsa o a un gruppo di risorse viene aggiunto automaticamente alla tassonomia a livello di sottoscrizione. È anche possibile precompilare tale tassonomia con nomi e valori di tag che si vogliono usare in futuro come risorse provviste di tag.

> [AZURE.NOTE]È possibile applicare dei tag solo alle risorse che supportano le operazioni di gestione delle risorse. Se è stato creata una macchina virtuale, una rete virtuale o una risorsa di archiviazione con il modello di distribuzione classico (ad esempio con il portale di Azure o l'[API di gestione del servizio](https://msdn.microsoft.com/library/azure/dn948465.aspx)), non è possibile applicare un tag a tale risorsa. È necessario distribuire nuovamente tali risorse tramite Gestione risorse per supportare l'assegnazione di tag. Tutte le altre risorse supportano l'assegnazione di tag.


## I tag nel portale di anteprima

Aggiungere tag alle risorse e ai gruppi di risorse nel portale di anteprima è facile. Usare l'hub Sfoglia per passare alla risorsa o al gruppo di risorse a cui aggiungere i tag e fare clic sull'area Tag nella sezione Panoramica nella parte superiore del pannello.

![Parte relativa ai tag nella risorsa e nei pannelli relativi ai gruppi di risorse](./media/resource-group-using-tags/tag-icon.png)

Verrà aperto un pannello con l'elenco dei tag che sono già stati applicati. Se questo è il primo tag, l'elenco sarà vuoto. Per aggiungere un tag, specificare un nome e un valore, quindi premere INVIO. Dopo aver aggiunto alcuni tag, si noteranno alcune opzioni di completamento automatico basate sui nomi e sui valori dei tag preesistenti. Questi consentono di garantire una tassonomia coerente tra le risorse e di evitare errori comuni, ad esempio errori di ortografia.

![Assegnare tag a risorse con coppie nome-valore](./media/resource-group-using-tags/tag-resources.png)

Per visualizzare la tassonomia dei tag nel portale, usare l'hub Sfoglia per visualizzare tutti gli elementi e quindi selezionare la voce Tag.

![Trovare tag tramite l'hub di esplorazione](./media/resource-group-using-tags/browse-tags.png)

Aggiungere i tag più importanti alla propria schermata iniziale per accedervi velocemente. A questo punto si è pronti per iniziare. Buon divertimento!

![Aggiungere tag alla Schermata iniziale  
](./media/resource-group-using-tags/pin-tags.png)

## Assegnazione di tag tramite PowerShell

Se Azure PowerShell non è stato usato in precedenza con Gestione risorse, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md). Ai fini di questo articolo, si presupporrà che sia già stato aggiunto un account e selezionata una sottoscrizione con le risorse che si vogliono contrassegnare.

L'aggiunta di tag è disponibile solo per risorse e gruppi di risorse presenti in [Gestione risorse](http://msdn.microsoft.com/library/azure/dn790568.aspx), quindi l'operazione successiva da eseguire è passare a Gestione risorse.

    Switch-AzureMode AzureResourceManager

I tag sono presenti direttamente nelle risorse e nei gruppi di risorse, quindi, per vedere quali tag sono già stati applicati, è possibile recuperare una risorsa o un gruppo di risorse rispettivamente con `Get-AzureResource` o `Get-AzureResourceGroup`. Iniziare con un gruppo di risorse.

    PS C:\> Get-AzureResourceGroup tag-demo

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    Resources         :
                    Name                             Type                                  Location
                    ===============================  ====================================  ==============
                    CPUHigh ExamplePlan              microsoft.insights/alertrules         eastus
                    ForbiddenRequests tag-demo-site  microsoft.insights/alertrules         eastus
                    LongHttpQueue ExamplePlan        microsoft.insights/alertrules         eastus
                    ServerErrors tag-demo-site       microsoft.insights/alertrules         eastus
                    ExamplePlan-tag-demo             microsoft.insights/autoscalesettings  eastus
                    tag-demo-site                    microsoft.insights/components         centralus
                    ExamplePlan                      Microsoft.Web/serverFarms             southcentralus
                    tag-demo-site                    Microsoft.Web/sites                   southcentralus


Questo cmdlet restituisce diversi bit di metadati sul gruppo di risorse, inclusi i tag che sono stati applicati, se presenti. Per aggiungere un tag a un gruppo di risorse, usare semplicemente il comando `Set-AzureResourceGroup` e specificare un nome e un valore per il tag.

    PS C:\> Set-AzureResourceGroup tag-demo -Tag @( @{ Name="project"; Value="tags" }, @{ Name="env"; Value="demo"} )

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  =====
                    project  tags
                    env      demo

I tag vengono aggiornati nel loro complesso, quindi se si aggiunge un tag a una risorsa che è già stata provvista di tag, è necessario usare una matrice con tutti i tag che si vogliono mantenere. A tale scopo, è possibile selezionare prima i tag esistenti e aggiungerne uno nuovo.

    PS C:\> $tags = (Get-AzureResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureResourceGroup tag-demo -Tag $tags

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  ========
                    project  tags
                    env      demo
                    status   approved


Se si vuole rimuovere uno o più tag, è sufficiente salvare la matrice senza i tag da rimuovere.

Il processo è lo stesso per le risorse, ad eccezione del fatto che si useranno i cmdlet `Get-AzureResource` e `Set-AzureResource`. Per recuperare le risorse o i gruppi di risorse che presentano un tag specifico, usare il cmdlet `Get-AzureResource` o `Get-AzureResourceGroup` con il parametro `-Tag`.

    PS C:\> Get-AzureResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo
    PS C:\> Get-AzureResource -Tag @{ Name="env"; Value="demo" } | %{ $_.Name }
    rbacdemo-web
    rbacdemo-docdb
    ...

Per ottenere un elenco di tutti i tag all'interno di una sottoscrizione tramite PowerShell, usare il cmdlet `Get-AzureTag`.

    PS C:/> Get-AzureTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

È possibile che vengano visualizzati tag che iniziano con "hidden-" e "link:". Si tratta di tag interni, che dovrebbero essere ignorati e comunque non devono modificati.

Usare il cmdlet `New-AzureTag` per aggiungere nuovi tag alla tassonomia. Questi tag verranno inclusi nel completamento automatico anche se non sono stati ancora applicati a risorse o gruppi di risorse. Per rimuovere una coppia nome-valore di un tag, rimuovere prima il tag dalle eventuali risorse che ne fanno uso, quindi usare il cmdlet `Remove-AzureTag` per rimuoverlo dalla tassonomia.

## Tag con l'API REST

Sia il portale che PowerShell usano l'[API REST di Gestione risorse](http://msdn.microsoft.com/library/azure/dn790568.aspx) dietro le quinte. Se è necessario integrare l'assegnazione di tag in un altro ambiente, è possibile ottenere i tag con un'operazione GET sull'ID di risorsa e aggiornare il set di tag con una chiamata PATCH.


## Assegnazione di tag e fatturazione

Per i servizi supportati, è possibile utilizzare i tag per raggruppare i dati di fatturazione. Ad esempio, [Macchine virtuali integrato con Gestione risorse di Azure](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) consente di definire e applicare i tag per organizzare l'utilizzo della fatturazione per le macchine virtuali. Se si eseguono più macchine virtuali per organizzazioni diverse, è possibile utilizzare i tag per raggruppare l'utilizzo in base al centro di costo. È inoltre possibile utilizzare i tag per classificare i costi in base all'ambiente di runtime; ad esempio, l'utilizzo di fatturazione per le macchine virtuali in esecuzione nell'ambiente di produzione.

È possibile recuperare le informazioni sui tag tramite l’[API di utilizzo](billing-usage-rate-card-overview.md) o il file con valori delimitati da virgole (CSV) di utilizzo che è possibile scaricare dal [portale degli account di Azure](https://account.windowsazure.com/) o dal [portale EA](https://ea.azure.com). Per altre informazioni sull'accesso a livello di codice alle informazioni sulla fatturazione, vedere [Ottenere informazioni dettagliate sul consumo delle risorse di Microsoft Azure](billing-usage-rate-card-overview.md).

Quando si scarica il CSV di utilizzo per i servizi che supportano i tag di fatturazione, i tag vengono visualizzati nella colonna **Tag**. Per informazioni dettagliate, vedere [Informazioni sulla fattura di Microsoft Azure](billing-understand-your-bill.md).

![Vedere i tag della fatturazione](./media/resource-group-using-tags/billing_csv.png)

## Passaggi successivi

- Per un'introduzione all'uso di Azure PowerShell per la distribuzione delle risorse, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](./powershell-azure-resource-manager.md).
- Per un'introduzione all'uso dell'interfaccia della riga di comando di Azure per la distribuzione delle risorse, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](./xplat-cli-azure-resource-manager.md).
- Per un’introduzione all'uso del portale di anteprima, vedere [Uso del portale di anteprima di Azure per gestire le risorse di Azure](./resource-group-portal.md)  
  

  

<!---HONumber=August15_HO7-->