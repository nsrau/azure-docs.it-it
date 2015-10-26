<properties
   pageTitle="Supporto di Gestione risorse di Azure per la versione di anteprima di Gestione traffico | Microsoft Azure"
   description="Utilizzo di PowerShell per Gestione traffico con la versione di anteprima di Gestione risorse di Azure"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />





# Supporto di Gestione risorse di Azure per la versione di anteprima di Gestione traffico di Azure
Gestione risorse di Azure rappresenta il nuovo framework di gestione dei servizi in Azure. I profili di Gestione traffico di Azure possono ora essere gestiti utilizzando le API e gli strumenti basati su Gestione risorse di Azure. Per ulteriori informazioni su Gestione risorse di Azure, vedere [Uso dei gruppi di risorse per gestire le risorse di Azure](../azure-preview-portal-using-resource-groups.md).

>[AZURE.NOTE]Attualmente, il supporto di Gestione risorse di Azure per Gestione traffico è disponibile in modalità di anteprima, così come API REST, Azure PowerShell, l'interfaccia della riga di comando di Azure e .NET SDK.



## Modello di risorsa

Gestione traffico di Azure viene configurato utilizzando una serie di impostazioni denominate "profilo di Gestione traffico". Tale profilo include le impostazioni DNS, di routing del traffico, di monitoraggio dell'endpoint e un elenco degli endpoint di servizio verso i quali verrà indirizzato il traffico.

In Gestione risorse di Azure, ogni profilo di Gestione traffico è rappresentato da una risorsa di tipo "TrafficManagerProfiles". Tale risorsa è gestita dal provider "Microsoft.Network". A livello di API REST, l'URI per ogni profilo è analogo al seguente:

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Confronto con l'API di gestione del servizio Gestione traffico di Azure

Se Gestione risorse di Azure viene utilizzato per configurare i profili di Gestione traffico, offre la stessa gamma di funzionalità di Gestione traffico previste dall'API di gestione del servizio (non di Gestione risorse di Azure). Tuttavia, fanno eccezione i limiti di anteprima riportati di seguito.

Sebbene le funzionalità siano identiche, alcune tecnologie hanno subito delle modifiche:

- Il nome "metodo di bilanciamento del carico" che determina il modo in cui in Gestione traffico viene scelto l’endpoint verso il quale indirizzare il traffico quando risponde a una determinata richiesta DNS, è stato modificato in "metodo di routing del traffico".

- Il nome del metodo di routing traffico "round robin" è stato modificato in "Weighted".

- Il nome del metodo di routing traffico "failover" è stato modificato in "Priority".

## Limiti di anteprima
Il supporto di Gestione risorse di Azure per Gestione traffico rappresenta un servizio di anteprima, attualmente caratterizzato da qualche limitazione:

- I profili di Gestione traffico creati utilizzando le API di gestione del servizio (non Gestione risorse), gli strumenti e il portale esistenti non sono disponibili in Gestione risorse e viceversa. La migrazione dei profili dalle API non appartenenti a Gestione risorse di Azure a quelle di Gestione risorse di Azure non è supportata al momento.

- 	L'API REST non supporta l'applicazione di patch ai profili di Gestione traffico. Per aggiornare una proprietà del profilo, è necessario VISUALIZZARE il profilo e IMMETTERE il profilo modificato.
- 	Sono supportati soltanto gli endpoint "esterni". È ancora possibile ricorrere a tali endpoint quando si utilizza Gestione traffico con i servizi basati su Azure. In questo caso, gli endpoint vengono addebitati alla stessa tariffa dell'endpoint interno. L'utilizzo degli endpoint esterni comporta soltanto la loro mancata rimozione o disattivazione automatica nel caso in cui venga eliminato o disattivato il servizio Azure sottostante. Al contrario, gli endpoint vanno eliminati o disattivati manualmente.
-	Gestione traffico di Azure non è ancora disponibile nel portale di Azure, ma soltanto in quello classico.

## Configurazione di Azure PowerShell

Nelle presenti istruzioni viene utilizzato Microsoft Azure PowerShell, che può essere configurato seguendo la procedura riportata di seguito.

Gli utenti che non utilizzano PowerShell possono eseguire la medesima procedura utilizzando interfacce differenti.

### Passaggio 1
Installare la versione più recente di Azure PowerShell, accedendo alle pagine di download di Azure.
### Passaggio 2
Modificare la modalità di PowerShell affinché utilizzi i cmdlet di Gestione risorse di Azure. Altre informazioni sono disponibili in Uso di Windows PowerShell con Gestione risorse.

	PS C:\> Switch-AzureMode -Name AzureResourceManager
### Passaggio 3
Accedere all'account Azure.

	PS C:\> Add-AzureAccount

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### Passaggio 4
Scegliere le sottoscrizioni ad Azure da utilizzare.

	PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Per visualizzare un elenco di sottoscrizioni disponibili, utilizzare il cmdlet "Get-AzureSubscription".

### Passaggio 5

 Il servizio Gestione traffico di Azure viene gestito dal provider di risorse Microsoft.Network. Tale provider deve essere registrato nella sottoscrizione ad Azure prima di utilizzare Gestione traffico tramite Gestione risorse di Azure. Si tratta di un'operazione una tantum per ogni sottoscrizione.

	PS C:\> Register-AzureProvider –ProviderNamespace Microsoft.Network

### Passaggio 6
Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

	PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dal momento che tutte le risorse di Gestione traffico sono globali (non locali), la scelta del percorso relativo al gruppo di risorse non ha alcun impatto sul servizio Gestione traffico di Azure.

## Creazione di un profilo di Gestione traffico

Per creare un profilo di gestione traffico, utilizzare il cmdlet New-AzureTrafficManagerProfile:

	PS C:\> $profile = New-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

I parametri sono i seguenti:

- Name: il nome della risorsa Gestione risorse di Azure per il profilo di Gestione traffico I profili dello stesso gruppo di risorse devono disporre di nomi univoci. Tale nome è diverso rispetto a quello DNS utilizzato per le query DNS.

-	ResourceGroupName: il nome del gruppo risorse di Gestione risorse di Azure che include la risorsa del profilo.

-	TrafficRoutingMethod: indica il metodo di routing del traffico utilizzato per determinare quale endpoint viene restituito in risposta alle query DNS in entrata. I valori possibili sono "Performance", "Weighted" e "Priority".

-	RelativeDnsName: indica il nome DNS fornito dal profilo Gestione traffico. Questo valore viene combinato con il nome del dominio DNS utilizzato da Gestione traffico di Azure per formare il nome di dominio completo del profilo. Ad esempio, il valore "contoso" fornirà al profilo Gestione traffico il nome di dominio completo "contoso.trafficmanager.net".

-	TTL: consente di specificare la durata DNS in secondi. In questo modo, i resolver locali e i client DNS sono informati sulla durata della memorizzazione nella cache delle risposte DNS fornite dal profilo Gestione traffico.

-	MonitorProtocol: indica il protocollo da utilizzare per monitorare lo stato di integrità dell'endpoint. I valori possibili sono "HTTP" e "HTTPS".

-	MonitorPort: indica la porta TCP utilizzata per monitorare l'integrità dell'endpoint.

-	MonitorPath: indica il percorso relativo al nome di dominio dell'endpoint utilizzato per verificare l'integrità dell'endpoint.

Il cmdlet consente di creare un profilo in Gestione traffico di Azure e restituisce un oggetto di profilo corrispondente. A questo punto, il profilo non include più endpoint. Per ulteriori dettagli su come aggiungere endpoint a un profilo Gestione traffico, vedere [Aggiornamento di un profilo Gestione traffico](#update-a-traffic-manager-profile).

## Visualizzazione di un profilo di Gestione traffico

Per recuperare un oggetto profilo di Gestione traffico esistente, utilizzare il cmdlet Get-AzureTrafficManagerProfle:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

Questo cmdlet restituisce un oggetto profilo di Gestione traffico.

## Aggiornamento di un profilo di Gestione traffico [](#update-traffic-manager-profile)

La modifica dei profili di Gestione traffico, ad esempio, per aggiungere/rimuovere endpoint o per modificare le impostazioni del profilo, viene eseguita in 3 passaggi:

1.	Recuperare il profilo utilizzando Get-AzureTrafficManagerProfile oppure il profilo restituito da New-AzureTrafficManagerProfile.

2.	Modificare il profilo, aggiungendo o rimuovendo gli endpoint, modificando i parametri dell'endpoint o del profilo. Queste modifiche si eseguono offline; pertanto, soltanto l'oggetto locale che rappresenta il profilo viene modificato.

3.	Confermare le modifiche utilizzando il cmdlet Set-AzureTrafficManagerProfile. In questo modo, si sostituisce il profilo esistente in Gestione traffico di Azure con quello fornito.

L'esempio seguente chiarisce ulteriormente quanto riportato in precedenza:

### Aggiunta degli endpoint a un profilo

È possibile aggiungere endpoint a un profilo di Gestione traffico utilizzando il cmdlet "Add-AzureTrafficManagerEndpointConfig":

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target site1.contoso.com –EndpointStatus Enabled –Weight 10 –Priority 1 –EndpointLocation “West US”
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

I parametri per Add-AzureTrafficManagerEndpointConfig sono i seguenti:

- EndpointName: il nome dell'endpoint. Gli endpoint presenti nello stesso profilo devono avere nomi differenti. Il parametro viene utilizzato per fare riferimento all'endpoint durante le operazioni di gestione del servizio, ma non è il nome DNS dell'endpoint.

-	TrafficManagerProfile: l'oggetto profilo di Gestione traffico a cui verrà aggiunto l'endpoint.

-	Type: il tipo di endpoint di Gestione traffico. Attualmente, è supportato soltanto il tipo "ExternalEndpoint" tramite l'API di Gestione risorse di Azure (vedere [Limiti di anteprima](#preview-limitations)).

-	Target: nome del dominio DNS completo per l'endpoint. Gestione traffico restituisce questo valore nelle risposte DNS per indirizzare il traffico all'endpoint.

-	EndpointStatus: indica lo stato dell'endpoint. Se l'endpoint è abilitato, ne viene verificato lo stato di integrità e viene incluso nel metodo di routing del traffico. I valori possibili sono "Enabled" o "Disabled".

-	Weight: indica il peso assegnato all'endpoint. Viene utilizzato soltanto se il profilo Gestione traffico viene configurato affinché utilizzi il metodo di routing del traffico "Weighted". I valori possibili variano da 1 a 1000.

-	Priority: indica la priorità dell'endpoint quando si utilizza il metodo di routing traffico "Priority". La priorità deve essere compreso nell'intervallo 1-1000. I valori più bassi rappresentano un livello di priorità maggiore.

-	EndpointLocation: indica il percorso dell'endpoint esterno da utilizzare con il metodo di routing traffico "Performance". Per visualizzare un elenco di possibili percorsi, vedere Get-AzureLocation.

I parametri Status, Weight e Priority dell'endpoint sono opzionali. Se si omettono, vengono bloccati e si applicano le impostazioni predefinite sul lato server.

### Eliminazione di endpoint da un profilo

Per eliminare un endpoint da un profilo, utilizzare "Remove-AzureTrafficmanagerEndpointConfig", specificando il nome dell'endpoint da eliminare:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

La sequenza delle operazioni da eseguire per aggiungere o eliminare gli endpoint può essere "inoltrata tramite pipe", consentendo all'oggetto di passare tramite il pipe invece che come parametro. Ad esempio:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 | Set-AzureTrafficManagerProfile

### Modificare le impostazioni del profilo o dell'endpoint

I parametri del profilo e dell'endpoint possono essere modificati offline e le modifiche possono essere confermate utilizzando Set-AzureTrafficManagerProfile. Fa eccezione soltanto il RelativeDnsName del profilo, che non può essere modificato dopo aver creato il profilo. Per modificare tale valore, eliminare e creare di nuovo il profilo. Ad esempio, per modificare il profilo TTL e lo stato del primo endpoint:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> $profile.Ttl = 300
	PS C:\> $profile.Endpoints[0].EndpointStatus = "Disabled"
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

### Eliminare un profilo di Gestione traffico
Per eliminare un profilo di Gestione traffico, utilizzare il cmdlet Remove-AzureTrafficManagerProfile, specificando il nome del profilo e del gruppo di risorse:

	PS C:\> Remove-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

Il cmdlet richiede una conferma. L'opzione facoltativa "-Force" può essere usata per eliminare questa richiesta. Inoltre, il profilo da eliminare può essere specificato utilizzando un oggetto di profilo:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Questa sequenza può anche essere inoltrata tramite pipe:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]


## Vedere anche

[Gestione traffico di Azure](traffic-manager-overview.md)

[Introduzione ai cmdlet di Azure](https://msdn.microsoft.com/library/jj554332.aspx)
 

<!---HONumber=Oct15_HO3-->