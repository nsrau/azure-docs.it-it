<properties urlDisplayName="" pageTitle="Uso dei tag per organizzare le risorse di Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Using tags to organize your Azure resources" authors="Michael Flanakin" solutions="" writer="" manager="carolz" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="micflan" />


# Uso dei tag per organizzare le risorse di Azure

Il portale di anteprima di Azure e il componente Gestione risorse sottostante consentono di organizzare le risorse e personalizzare le proprie modalità di utilizzo adattandole alle esigenze personali. 

Nel portale di Azure completo le sottoscrizioni rappresentano l'unico metodo per classificare e raggruppare le proprie risorse. Con il portale di anteprima, [sono stati introdotti i gruppi di risorse](http://azure.microsoft.com/it-it/documentation/articles/azure-preview-portal-using-resource-groups), che consentono il raggruppamento di entità correlate. Questa funzionalità si è rivelata ancora più utile quando [è stato introdotto l'accesso basato sui ruoli](http://azure.microsoft.com/it-it/documentation/articles/role-based-access-control-configure). Adesso, secondo lo stesso principio, è possibile aggiungere alle proprie risorse tag con coppie chiave-valore in modo da poterle ulteriormente classificare e visualizzare anche se sparse tra vari gruppi di risorse e, all'interno del portale, tra varie sottoscrizioni.

Sarà così possibile raggruppare risorse per team, progetto o anche per ambiente per concentrarsi esattamente su ciò che più interessa al momento più opportuno. 


## Tag nel portale di anteprima di Azure

Aggiungere tag alle risorse e ai gruppi di risorse nel portale di anteprima è facile. Usare l'hub Sfoglia per passare alla risorsa o al gruppo di risorse a cui aggiungere i tag e fare clic sull'area Tag nella sezione Panoramica nella parte superiore del pannello. 

![Tags part on resource and resource group blades](./media/azure-preview-portal-using-tags/rgblade.png)

Verrà aperto un pannello con l'elenco dei tag che sono già stati applicati. Se questo è il primo tag, l'elenco sarà vuoto. Per aggiungere un tag, specificare un nome e un valore, quindi premere INVIO. Dopo aver aggiunto alcuni tag, si noteranno alcune opzioni di completamento automatico basate sui nomi e sui valori dei tag preesistenti. Questi consentono di garantire una tassonomia coerente tra le risorse e di evitare errori comuni, ad esempio errori di ortografia.

![Tag resources with name/value pairs](./media/azure-preview-portal-using-tags/tag-resources.png)

A questo punto, è possibile fare clic su ogni singolo tag per visualizzare un elenco di tutte le risorse con lo stesso tag. Naturalmente, se questo è il primo tag, tale elenco non risulterà particolarmente interessante. Per il momento, è consigliabile passare a PowerShell per aggiungere tag a tutte le risorse in modo più rapido.


## Assegnazione di tag tramite PowerShell

Per prima cosa, procurarsi il [Modulo di Azure PowerShell](http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/) più recente. Se questa è la prima volta che si usa il modulo di Azure PowerShell, [leggere la documentazione](http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell) per acquisirvi dimestichezza. Ai fini di questo articolo, si presupporrà che sia già stato aggiunto un account e selezionata una sottoscrizione con le risorse che si vogliono contrassegnare.

L'aggiunta di tag è disponibile solo per risorse e gruppi di risorse presenti in [Gestione risorse](http://msdn.microsoft.com/it-it/library/azure/dn790568.aspx), quindi la prossima operazione da eseguire è passare a Gestione risorse. Per altre informazioni, vedere [Utilizzo di Windows PowerShell con Gestione risorse](http://azure.microsoft.com/it-it/documentation/articles/powershell-azure-resource-manager/).

  Switch-AzureMode AzureResourceManager

I tag sono presenti direttamente nelle risorse e nei gruppi di risorse, quindi, per vedere quali tag sono già stati applicati, è possibile recuperare una risorsa o un gruppo di risorse con `Get-AzureResource` o `Get-AzureResourceGroup`, rispettivamente. Iniziare con un gruppo di risorse.

![Getting tags with Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Questo cmdlet restituisce diversi bit di metadati sul gruppo di risorse, inclusi i tag che sono stati applicati, se presenti. Per apporre un tag  a un gruppo di risorse, si userà semplicemente `Set-AzureResourceGroup` e si specificheranno un nome e un valore per il tag.

![Setting tags with Set-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

Tenere presente che i tag vengono aggiornati nel loro complesso, pertanto se si aggiunge un tag a una risorsa che è già stata contrassegnata con tag, è necessario salvare una matrice con tutti i tag che si vogliono mantenere. Se si vuole rimuovere un tag, è sufficiente salvare la matrice senza il tag da rimuovere. 

Il processo è lo stesso per le risorse, ad eccezione del fatto che si useranno i cmdlet `Get-AzureResource` e `Set-AzureResource`. Per recuperare le risorse o i gruppi di risorse che presentano uno specifico tag, usare il cmdlet `Get-AzureResource` o `Get-AzureResourceGroup` con il parametro `-Tag`.

![Getting tagged resources and resource groups with Get-AzureResource and Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Assegnazione di tag con Gestione risorse

Sia il portale di anteprima che PowerShell usano l'[API REST di gestione risorse](http://msdn.microsoft.com/it-it/library/azure/dn790568.aspx) dietro le quinte. Se è necessario integrare l'assegnazione di tag in un altro ambiente, è possibile ottenere i tag con un'operazione GET sull'ID di risorsa e aggiornare il set di tag con una chiamata PATCH.


## Gestire la tassonomia

Più indietro si è fatto accenno a come la funzionalità di completamento automatico può garantire coerenza e consentire di evitare errori. Completamento automatico viene popolato in base alla tassonomia di configurazione dei tag disponibili per la sottoscrizione. Ogni tag aggiunto a una risorsa o a un gruppo di risorse viene aggiunto automaticamente alla tassonomia a livello di sottoscrizione, ma è anche possibile precompilare tale tassonomia con nomi e valori di tag che si vogliono usare in futuro come risorse provviste di tag.

Per ottenere un elenco di tutti i tag all'interno di una sottoscrizione usando PowerShell, usare il cmdlet `Get-AzureTag`.

![Get-AzureTag in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureTag-in-PowerShell.png)


È possibile che vengano visualizzati tag che iniziano con "hidden-" e "link:". Si tratta di tag interni, che dovrebbero essere ignorati e comunque non devono modificati. 

Usare il cmdlet `New-AzureTag` per aggiungere nuovi tag alla tassonomia. Questi tag verranno inclusi nel completamento automatico anche se non sono stati ancora applicati a risorse o gruppi di risorse. Per rimuovere una coppia nome-valore di un tag, rimuovere prima il tag dalle eventuali risorse che ne fanno uso, quindi servirsi del cmdlet `Remove-AzureTag` per rimuoverlo dalla tassonomia.

Per visualizzare la tassonomia dei tag nel portale, usare l'hub Sfoglia per visualizzare tutti gli elementi e quindi selezionare la voce Tag.

![Find tags via the Browse hub](./media/azure-preview-portal-using-tags/browse-tags.png)

Aggiungere i tag più importanti alla propria schermata iniziale per accedervi velocemente. A questo punto si è pronti per iniziare. Buon divertimento!

![Pin tags to the Startboard](./media/azure-preview-portal-using-tags/pin-tags.png)

