<properties 
	pageTitle="Uso dei tag per organizzare le risorse di Azure" 
	description="" 
	services="" 
	documentationCenter="" 
	authors="tfitzmac"
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="AzurePortal" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="tomfitz"/>


# Uso dei tag per organizzare le risorse di Azure

Il portale di Azure e il componente Gestione risorse sottostante consentono di organizzare le risorse e personalizzare le proprie modalità d'uso adattandole alle esigenze personali.

Nel portale di Azure classico le sottoscrizioni rappresentano l'unico metodo per classificare e raggruppare le risorse. Con il portale di Azure [sono stati introdotti i gruppi di risorse](./resource-group-portal.md), che consentono il raggruppamento di entità correlate. Questa funzionalità si è rivelata ancora più utile quando [è stato introdotto l'accesso basato sui ruoli](./role-based-access-control-configure.md). Adesso, secondo lo stesso principio, è possibile aggiungere alle proprie risorse tag con coppie chiave-valore in modo da poterle ulteriormente classificare e visualizzare anche se sparse tra vari gruppi di risorse e, all'interno del portale, tra varie sottoscrizioni.

Sarà così possibile raggruppare risorse per team, progetto o anche per ambiente per concentrarsi esattamente su ciò che più interessa al momento più opportuno.


## Tag nel portale di Azure

Aggiungere tag alle risorse e ai gruppi di risorse nel portale è facile. Usare l'hub Sfoglia per passare alla risorsa o al gruppo di risorse a cui aggiungere i tag e fare clic sull'area Tag nella sezione Panoramica nella parte superiore del pannello.

![Parte relativa ai tag nella risorsa e nei pannelli relativi ai gruppi di risorse](./media/resource-group-using-tags/rgblade.png)

Verrà aperto un pannello con l'elenco dei tag che sono già stati applicati. Se questo è il primo tag, l'elenco sarà vuoto. Per aggiungere un tag, specificare un nome e un valore, quindi premere INVIO. Dopo aver aggiunto alcuni tag, si noteranno alcune opzioni di completamento automatico basate sui nomi e sui valori dei tag preesistenti. Questi consentono di garantire una tassonomia coerente tra le risorse e di evitare errori comuni, ad esempio errori di ortografia.

![Assegnare tag a risorse con coppie nome-valore](./media/resource-group-using-tags/tag-resources.png)

A questo punto, è possibile fare clic su ogni singolo tag per visualizzare un elenco di tutte le risorse con lo stesso tag. Naturalmente, se questo è il primo tag, tale elenco non risulterà particolarmente interessante. Per il momento, è consigliabile passare a PowerShell per aggiungere tag a tutte le risorse in modo più rapido.


## Assegnazione di tag tramite PowerShell

Per prima cosa, procurarsi il [modulo di Azure PowerShell più recente](./install-configure-powershell.md). Se questa è la prima volta che si usa il modulo di Azure PowerShell, [leggere la documentazione](./install-configure-powershell.md) per acquisire dimestichezza. Ai fini di questo articolo, si presupporrà che sia già stato aggiunto un account e selezionata una sottoscrizione con le risorse che si vogliono contrassegnare.

L'aggiunta di tag è disponibile solo per risorse e gruppi di risorse presenti in [Gestione risorse](http://msdn.microsoft.com/library/azure/dn790568.aspx), quindi l'operazione successiva da eseguire è passare a Gestione risorse. Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](powershell-azure-resource-manager.md).

    Switch-AzureMode AzureResourceManager

I tag sono presenti direttamente nelle risorse e nei gruppi di risorse, quindi, per vedere quali tag sono già stati applicati, è possibile recuperare una risorsa o un gruppo di risorse rispettivamente con `Get-AzureResource` o `Get-AzureResourceGroup`. Iniziare con un gruppo di risorse.

![Recupero di tag con Get-AzureResourceGroup in PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Questo cmdlet restituisce diversi bit di metadati sul gruppo di risorse, inclusi i tag che sono stati applicati, se presenti. Per apporre un tag a un gruppo di risorse, si userà semplicemente `Set-AzureResourceGroup` e si specificheranno un nome e un valore per il tag.

![Impostazione di tag con Get-AzureResourceGroup in PowerShell](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

Tenere presente che i tag vengono aggiornati nel loro complesso, pertanto se si aggiunge un tag a una risorsa che è già stata contrassegnata con tag, è necessario usare una matrice con tutti i tag che si vogliono mantenere. Se si vuole rimuovere un tag, è sufficiente salvare la matrice senza il tag da rimuovere.

Il processo è lo stesso per le risorse, ad eccezione del fatto che si useranno i cmdlet `Get-AzureResource` e `Set-AzureResource`. Per recuperare le risorse o i gruppi di risorse che presentano un tag specifico, usare il cmdlet `Get-AzureResource` o `Get-AzureResourceGroup` con il parametro `-Tag`.

![Recupero di risorse e gruppi di risorse con tag con Get-AzureResource e Get-AzureResourceGroup in PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Assegnazione di tag con Gestione risorse

Sia il portale che PowerShell usano l'[API REST di Gestione risorse](http://msdn.microsoft.com/library/azure/dn790568.aspx) dietro le quinte. Se è necessario integrare l'assegnazione di tag in un altro ambiente, è possibile ottenere i tag con un'operazione GET sull'ID di risorsa e aggiornare il set di tag con una chiamata PATCH.


## Gestire la tassonomia

Più indietro si è fatto accenno a come la funzionalità di completamento automatico può garantire coerenza e consentire di evitare errori. Completamento automatico viene popolato in base alla tassonomia di configurazione dei tag disponibili per la sottoscrizione. Ogni tag aggiunto a una risorsa o a un gruppo di risorse viene aggiunto automaticamente alla tassonomia a livello di sottoscrizione, ma è anche possibile precompilare tale tassonomia con nomi e valori di tag che si vogliono usare in futuro come risorse provviste di tag.

Per ottenere un elenco di tutti i tag all'interno di una sottoscrizione tramite PowerShell, usare il cmdlet `Get-AzureTag`.

![Get-AzureTag in PowerShell](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


È possibile che vengano visualizzati tag che iniziano con "hidden-" e "link:". Si tratta di tag interni, che dovrebbero essere ignorati e comunque non devono modificati.

Usare il cmdlet `New-AzureTag` per aggiungere nuovi tag alla tassonomia. Questi tag verranno inclusi nel completamento automatico anche se non sono stati ancora applicati a risorse o gruppi di risorse. Per rimuovere una coppia nome-valore di un tag, rimuovere prima il tag dalle eventuali risorse che ne fanno uso, quindi usare il cmdlet `Remove-AzureTag` per rimuoverlo dalla tassonomia.

Per visualizzare la tassonomia dei tag nel portale, usare l'hub Sfoglia per visualizzare tutti gli elementi e quindi selezionare la voce Tag.

![Trovare tag tramite l'hub di esplorazione](./media/resource-group-using-tags/browse-tags.png)

Aggiungere i tag più importanti alla propria schermata iniziale per accedervi velocemente. A questo punto si è pronti per iniziare. Buon divertimento!

![Aggiungere tag alla Schermata iniziale  
](./media/resource-group-using-tags/pin-tags.png)

## Assegnazione di tag e fatturazione

Per i servizi supportati, è possibile utilizzare i tag per raggruppare i dati di fatturazione. Ad esempio, [Macchine virtuali integrato con Gestione risorse di Azure](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) consente di definire e applicare i tag per organizzare l'utilizzo della fatturazione per le macchine virtuali. Se si eseguono più macchine virtuali per organizzazioni diverse, è possibile utilizzare i tag per raggruppare l'utilizzo in base al centro di costo. È inoltre possibile utilizzare i tag per classificare i costi in base all'ambiente di runtime; ad esempio, l'utilizzo di fatturazione per le macchine virtuali in esecuzione nell'ambiente di produzione.

È possibile recuperare le informazioni sui tag tramite l’[API di utilizzo](billing-usage-rate-card-overview.md) o il file con valori delimitati da virgole (CSV) di utilizzo che è possibile scaricare dal [portale degli account di Azure](https://account.windowsazure.com/) o dal [portale EA](https://ea.azure.com).

Quando si scarica il CSV di utilizzo per i servizi che supportano i tag di fatturazione, i tag vengono visualizzati nella colonna **Tag**. Per ulteriori informazioni, vedere [Informazioni sulla fattura di Microsoft Azure](billing-understand-your-bill.md).

![Vedere i tag della fatturazione](./media/resource-group-using-tags/billing_csv.png)

## Passaggi successivi
Introduzione

- [Panoramica di Gestione risorse di Azure](./resource-group-overview.md)  
- [Uso di Azure PowerShell con Gestione risorse di Azure](./powershell-azure-resource-manager.md)
- [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione delle risorse di Azure](./xplat-cli-azure-resource-manager.md)  
- [Uso del portale di Azure per gestire le risorse di Azure](./resource-group-portal.md)  
  
Creazione e distribuzione delle applicazioni
  
- [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md)  
- [Distribuire un'applicazione con un modello di Gestione risorse di Azure](./resource-group-template-deploy.md)  
- [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](./resource-group-deploy-debug.md)  
- [Funzioni del modello di Gestione risorse di Azure](./resource-group-template-functions.md)  
- [Operazioni avanzate con i modelli](./resource-group-advanced-template.md)  
  
Gestione e controllo dell'accesso
  
- [Gestione e controllo dell'accesso alle risorse](./resource-group-rbac.md)  
- [Autenticazione di un'entità servizio con Gestione risorse di Azure](./resource-group-authenticate-service-principal.md)  
- [Creare una nuova entità servizio di Azure usando il portale classico di Azure](./resource-group-create-service-principal-portal.md)  
  

<!---HONumber=July15_HO1-->