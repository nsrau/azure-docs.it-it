<properties
   pageTitle="Supporto di Gestione risorse di Azure per la versione di anteprima di Gestione traffico | Microsoft Azure"
   description="Utilizzo di PowerShell per Gestione traffico con la versione di anteprima di Gestione risorse di Azure"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Supporto di Gestione risorse di Azure per la versione di anteprima di Gestione traffico di Azure
Gestione risorse di Azure rappresenta il nuovo framework di gestione dei servizi in Azure. I profili di Gestione traffico di Azure possono ora essere gestiti utilizzando le API e gli strumenti basati su Gestione risorse di Azure.

## Modello di risorsa

Gestione traffico di Azure viene configurato utilizzando una serie di impostazioni denominate "profilo di Gestione traffico". Tale profilo include le impostazioni DNS, di routing del traffico, di monitoraggio dell'endpoint e un elenco degli endpoint di servizio verso i quali verrà indirizzato il traffico.

In Gestione risorse di Azure, ogni profilo di Gestione traffico è rappresentato da una risorsa di tipo "TrafficManagerProfiles". Tale risorsa è gestita dal provider "Microsoft.Network". A livello di API REST, l'URI per ogni profilo è analogo al seguente:

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Confronto con l'API di gestione del servizio Gestione traffico di Azure

L'uso di ARM per configurare i profili di Gestione traffico fornisce l'accesso allo stesso insieme di funzionalità di Gestione traffico offerte dall'API di gestione del servizio Azure, ad eccezione delle limitazioni della versione di anteprima elencate di seguito.

Sebbene le funzionalità siano identiche, alcune tecnologie hanno subito delle modifiche:

- Il nome "metodo di bilanciamento del carico" che determina il modo in cui in Gestione traffico viene scelto l’endpoint verso il quale indirizzare il traffico quando risponde a una determinata richiesta DNS, è stato modificato in "metodo di routing del traffico".

- Il nome del metodo di routing traffico "round robin" è stato modificato in "Weighted".

- Il nome del metodo di routing traffico "failover" è stato modificato in "Priority".

## Limitazioni
Attualmente, esistono alcune limitazioni nel supporto ARM per Gestione traffico di Azure:

- I profili di Gestione traffico creati mediante l'API di gestione del servizio Azure esistente (non ARM), gli strumenti e il portale "classico" non sono disponibili tramite ARM e viceversa. La migrazione dei profili dalle API di gestione del servizio alle API ARM non è attualmente supportata. È solo possibile eliminare e ricreare il profilo.

- Endpoint di Gestione traffico 'annidati' sono supportati tramite l'interfaccia della riga di comando API ARM, PowerShell ARM e in modalità ARM. Al momento, non sono supportati nel portale di Azure (che usa anche l'API ARM).

- Gli endpoint di Gestione traffico di tipo "AzureEndpoints", quando fanno riferimento a un'app Web, possono fare riferimento solo allo [slot dell'app Web](../app-service-web/web-sites-staged-publishing.md) predefinito (produzione). Gli slot personalizzati non sono ancora supportati. In alternativa, gli slot personalizzati possono essere configurati usando il tipo 'ExternalEndpoints'.

## Configurazione di Azure PowerShell

Nelle presenti istruzioni viene utilizzato Microsoft Azure PowerShell, che può essere configurato seguendo la procedura riportata di seguito.

Per gli utenti non PowerShell o non Windows, è possibile eseguire operazioni analoghe tramite l'interfaccia della riga di comando di Azure. Tutte le operazioni, ad eccezione della gestione dei profili di Gestione traffico 'annidati', sono disponibili anche tramite il portale di Azure.

### Passaggio 1
Installare la versione più recente di Azure PowerShell, accedendo alle pagine di download di Azure.

### Passaggio 2
Accedere all'account Azure.

	PS C:\> Login-AzureRmAccount

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### Passaggio 3
Scegliere le sottoscrizioni ad Azure da utilizzare.

	PS C:\> Set-AzureRmContext -SubscriptionName "MySubscription"

Per visualizzare l'elenco delle sottoscrizioni disponibili, usare il cmdlet 'Get-AzureRmSubscription'.

### Passaggio 4

Il servizio Gestione traffico di Azure viene gestito dal provider di risorse Microsoft.Network. Tale provider deve essere registrato nella sottoscrizione ad Azure prima di utilizzare Gestione traffico tramite Gestione risorse di Azure. Si tratta di un'operazione una tantum per ogni sottoscrizione.

	PS C:\> Register-AzureRmResourceProvider –ProviderNamespace Microsoft.Network

### Passaggio 5
Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

	PS C:\> New-AzureRmResourceGroup -Name MyRG -Location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Tuttavia, dal momento che tutte le risorse di Gestione traffico sono globali (non locali), la scelta del percorso relativo al gruppo di risorse non ha alcun impatto sul servizio Gestione traffico di Azure.

## Creazione di un profilo di Gestione traffico

Per creare un profilo di Gestione traffico, usare il cmdlet New-AzureRmTrafficManagerProfile:

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

I parametri sono i seguenti:

- Name: il nome della risorsa Gestione risorse di Azure per il profilo di Gestione traffico I profili dello stesso gruppo di risorse devono disporre di nomi univoci. Tale nome è diverso rispetto a quello DNS utilizzato per le query DNS.

- ResourceGroupName: il nome del gruppo risorse di Gestione risorse di Azure che include la risorsa del profilo.

- TrafficRoutingMethod: indica il metodo di routing del traffico utilizzato per determinare quale endpoint viene restituito in risposta alle query DNS in entrata. I valori possibili sono "Performance", "Weighted" e "Priority".

- RelativeDnsName: indica il nome DNS fornito dal profilo Gestione traffico. Questo valore viene combinato con il nome del dominio DNS utilizzato da Gestione traffico di Azure per formare il nome di dominio completo del profilo. Ad esempio, il valore 'contoso' consentirà di ottenere un profilo di Gestione traffico con nome completo 'contoso.trafficmanager.net'.

- TTL: consente di specificare la durata DNS in secondi. In questo modo, i resolver locali e i client DNS sono informati sulla durata della memorizzazione nella cache delle risposte DNS fornite dal profilo Gestione traffico.

- MonitorProtocol: indica il protocollo da utilizzare per monitorare lo stato di integrità dell'endpoint. I valori possibili sono "HTTP" e "HTTPS".

- MonitorPort: indica la porta TCP utilizzata per monitorare l'integrità dell'endpoint.

- MonitorPath: indica il percorso relativo al nome di dominio dell'endpoint utilizzato per verificare l'integrità dell'endpoint.

Il cmdlet consente di creare un profilo in Gestione traffico di Azure e restituisce un oggetto di profilo corrispondente. A questo punto il profilo non contiene alcun endpoint. Per informazioni su come aggiungere endpoint a un profilo di Gestione traffico, vedere [Aggiunta di endpoint di Gestione traffico](#adding-traffic-manager-endpoints).

## Visualizzazione di un profilo di Gestione traffico

Per recuperare un oggetto profilo di Gestione traffico, usare il cmdlet Get-AzureRmTrafficManagerProfle:

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG

Questo cmdlet restituisce un oggetto profilo di Gestione traffico.

## Aggiornamento di un profilo di Gestione traffico [](#update-traffic-manager-profile)

La modifica dei profili di Gestione traffico, ad esempio, per aggiungere/rimuovere endpoint o per modificare le impostazioni del profilo, viene eseguita in 3 passaggi:

1.	Recuperare il profilo mediante Get-AzureRmTrafficManagerProfile oppure usare il profilo restituito da New-AzureRmTrafficManagerProfile.

2.	Modificare il profilo, aggiungendo o rimuovendo gli endpoint, modificando i parametri dell'endpoint o del profilo. Queste modifiche sono operazioni offline. Viene modificato solo l'oggetto locale che rappresenta il profilo.

3.	Eseguire il commit delle modifiche mediante il cmdlet Set-AzureRmTrafficManagerProfile. In questo modo, si sostituisce il profilo esistente in Gestione traffico di Azure con quello fornito.

È possibile modificare tutte le proprietà del profilo, ad eccezione del valore RelativeDnsName del profilo, che non può essere modificato dopo la creazione. Per modificare questo valore, eliminare e ricreare il profilo.

Ad esempio, per modificare il valore TTL del profilo:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> $profile.Ttl = 300
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

## Aggiungere endpoint di Gestione traffico
Sono disponibili tre tipi di endpoint di Gestione traffico:

1. Endpoint di Azure: rappresentano i servizi ospitati in Azure.<BR>
2. Endpoint esterni: rappresentano i servizi ospitati all'esterno di Azure.<BR>
3. Endpoint annidati: usati per costruire gerarchie annidate di profili di Gestione traffico, per abilitare configurazioni avanzate dell'indirizzamento del traffico per applicazioni più complesse. Non sono ancora supportati tramite l'API ARM.<BR>

In tutti e tre i casi è possibile aggiungere gli endpoint in due modi:<BR>

1. Mediante un processo in tre passaggi simile a quello descritto in [Aggiornare un profilo di Gestione traffico](#update-traffic-manager-profile): recupero dell'oggetto profilo mediante Get-AzureRmTrafficManagerProfile, aggiornamento dell'oggetto offline per aggiungere un endpoint mediante Add-AzureRmTrafficManagerEndpointConfig e caricamento delle modifiche in Gestione traffico di Azure mediante Set-AzureRmTrafficManagerProfile. Il vantaggio di questo metodo consiste nel fatto che è possibile apportare alcune modifiche agli endpoint in un singolo aggiornamento.<BR>

2. Mediante il cmdlet New-AzureRmTrafficManagerEndpoint. Questo metodo aggiunge un endpoint a un profilo di Gestione traffico esistente in una singola operazione.

### Aggiunta di endpoint di Azure

Gli endpoint di Azure fanno riferimento ad altri servizi ospitati in Azure. Sono attualmente supportati tre tipi di endpoint di Azure:<BR>
1. App Web di Azure<BR>
2. Servizi cloud "classici" (che possono includere un servizio PaaS o macchine virtuali IaaS)<BR>
3. Risorse di tipo Microsoft.Network/publicIpAddress ARM (che possono essere associate a un servizio di bilanciamento del carico o a una NIC di macchina virtuale). Si noti che è necessario che al valore publicIpAddress sia assegnato un nome DNS, da usare in Gestione traffico.

In ogni caso:
 - Il servizio viene specificato con il parametro "TargetResourceId" del componente Add-AzureRmTrafficManagerEndpointConfig o New-AzureRmTrafficManagerEndpoint.<BR>
 - "Target" ed "EndpointLocation" non devono essere specificati perché sono impliciti nel TargetResourceId specificato in precedenza.<BR>
 - "Weight" è facoltativo ed è possibile scegliere se specificarlo. I pesi vengono usati solo se il profilo è configurato per l'uso del metodo di indirizzamento del traffico 'Weighted'. In caso contrario, vengono ignorati. Se specificato, questo valore deve essere incluso nell'intervallo 1...1000. Il valore predefinito è 1.<BR>
 - "Priority" è facoltativo ed è possibile scegliere se specificarlo. Le priorità vengono usate solo se il profilo è configurato per l'uso del metodo di indirizzamento del traffico 'Priority'. In caso contrario, vengono ignorate. I valori validi sono compresi tra 1 e 1000. I valori più bassi corrispondono a una priorità maggiore. Se si specifica questo valore per un endpoint, sarà necessario specificarlo per tutti gli endpoint. Se questo valore viene omesso, verranno applicati i valori predefiniti a partire da 1, 2, 3 e così via nell'ordine in cui vengono forniti gli endpoint.

#### Esempio 1: Aggiunta di endpoint di app Web mediante Add-AzureRmTrafficManagerEndpointConfig
In questo esempio viene creato un nuovo profilo di Gestione traffico e vengono aggiunti due endpoint di app Web mediante il cmdlet Add-AzureRmTrafficManagerEndpointConfig. Viene quindi eseguito il commit del profilo aggiornato in Gestione traffico di Azure mediante Set-AzureRmTrafficManagerProfile.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $webapp1 = Get-AzureRMWebApp -Name webapp1
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp1ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled
	PS C:\> $webapp2 = Get-AzureRMWebApp -Name webapp2
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp2ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Esempio 2: Aggiunta di un endpoint di servizio cloud "classico" mediante New-AzureRmTrafficManagerEndpoint
In questo esempio viene aggiunto un endpoint di servizio cloud "classico" a un profilo di Gestione traffico. Si noti che in questo caso si sceglie di specificare il profilo mediante il nome del profilo e il nome del gruppo di risorse, invece di passare un oggetto profilo. Sono supportati entrambi gli approcci.

	PS C:\> $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyCloudServiceEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $cloudService.Id –EndpointStatus Enabled

#### Esempio 3: Aggiunta di un endpoint publicIpAddress mediante New-AzureRmTrafficManagerEndpoint
In questo esempio viene aggiunta una risorsa indirizzo IP pubblico ARM al profilo di Gestione traffico. L'indirizzo IP pubblico deve avere un nome DNS configurato e può essere associato alla NIC di una VM o a un servizio di bilanciamento del carico.

	PS C:\> $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyIpEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $ip.Id –EndpointStatus Enabled

### Aggiunta di endpoint esterni
Gestione traffico usa endpoint esterni per indirizzare il traffico ai servizi ospitati all'esterno di Azure. Analogamente agli endpoint di Azure, gli endpoint esterni possono essere aggiunti mediante Add-AzureRmTrafficManagerEndpointConfig seguito da Set-AzureRmTrafficManagerProfile o New-AzureRMTrafficManagerEndpoint.

Quando si specificano endpoint esterni:
 - Il nome di dominio dell'endpoint deve essere specificato con il parametro "Target".<BR>
 - "EndpointLocation" è obbligatorio se viene usato il metodo di routing del traffico "Performance", in caso contrario è facoltativo. Il valore deve essere un [nome di area di Azure valido](https://azure.microsoft.com/regions/).<BR>
 - I parametri "Weight" e "Priority" sono facoltativi, come per gli endpoint di Azure.<BR>
 

#### Esempio 1: Aggiunta di endpoint esterni mediante Add-AzureRmTrafficManagerEndpointConfig e Set-AzureRmTrafficManagerProfile
In questo esempio viene creato un nuovo profilo di Gestione traffico, vengono aggiunti due endpoint esterni e viene eseguito il commit delle modifiche.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName eu-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName us-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-us.contoso.com –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Esempio 2: Aggiunta di endpoint esterni mediante New-AzureRmTrafficManagerEndpoint
In questo esempio viene aggiunto un endpoint esterno a un profilo esistente, specificato mediante il nome del profilo e il nome del gruppo di risorse.

	PS C:\> New-AzureRmTrafficManagerEndpoint –Name eu-endpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled

### Aggiunta di endpoint 'annidati'

Gestione traffico consente di configurare un profilo di Gestione traffico (che chiameremo il profilo "figlio") come endpoint all'interno di un altro profilo di Gestione traffico (che chiameremo il profilo "padre").

Gestione traffico di annidamento consente la creazione di routing del traffico e schemi di failover più flessibili e potenti per supportare le esigenze di distribuzioni più grandi e complesse. [Questo post di blog](https://azure.microsoft.com/blog/new-azure-traffic-manager-nested-profiles/) illustra diversi esempi.

Endpoint annidati vengono configurati nel profilo padre tramite un tipo di endpoint specifico, 'NestedEndpoints'. Quando si specificano endpoint annidati:
 - L'endpoint, ad esempio il profilo figlio, deve essere specificato con il parametro "TargetResourceId". <BR>
 - "EndpointLocation" è obbligatorio se viene usato il metodo di routing del traffico "Performance", in caso contrario è facoltativo. Il valore deve essere un [nome di area di Azure valido](http://azure.microsoft.com/regions/).<BR>
 - I parametri "Weight" e "Priority" sono facoltativi, come per gli endpoint di Azure.<BR>
 - Il parametro "MinChildEndpoints" è facoltativo e il valore predefinito è 1. Se il numero degli endpoint disponibili nel profilo figlio scende sotto questa soglia, il profilo padre considera il profilo figlio "degradato" con conseguente deviazione del traffico agli altri endpoint del profilo padre.<BR>


#### Esempio 1: Aggiunta di endpoint annidati mediante Add-AzureRmTrafficManagerEndpointConfig e Set-AzureRmTrafficManagerProfile

In questo esempio, vengono creati un nuovo profilo padre e un nuovo profilo figlio di Gestione traffico, si aggiunge il profilo figlio come un endpoint annidato nel profilo padre, quindi vengono confermate le modifiche. Per brevità, non vengono aggiunti altri endpoint al profilo figlio o al profilo padre, anche se normalmente sono richiesti anche questi.<BR>

	PS C:\> $child = New-AzureRmTrafficManagerProfile –Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $parent = New-AzureRmTrafficManagerProfile –Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName child-endpoint –TrafficManagerProfile $parent –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Esempio 2: Aggiunta di endpoint annidati mediante New-AzureRmTrafficManagerEndpoint

In questo esempio, viene aggiunto un profilo figlio esistente come endpoint annidato a un profilo padre esistente, specificato mediante il nome del profilo e del gruppo di risorse.

	PS C:\> $child = Get-AzureRmTrafficManagerEndpoint –Name child -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name child-endpoint –ProfileName parent -ResourceGroupName MyRG –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2


## Aggiornare un endpoint di Gestione traffico
È possibile aggiornare un endpoint di Gestione traffico esistente in due modi:<BR>

1. Ottenere il profilo di Gestione traffico mediante Get-AzureRmTrafficManagerProfile, aggiornare le proprietà dell'endpoint nel profilo ed eseguire il commit delle modifiche mediante Set-AzureRmTrafficManagerProfile. Il vantaggio di questo metodo consiste nella possibilità di aggiornare più endpoint in una singola operazione.<BR>
2. Ottenere l'endpoint di Gestione traffico mediante Get-AzureRmTrafficManagerEndpoint, aggiornare le proprietà dell'endpoint ed eseguire il commit delle modifiche mediante Set-AzureRmTrafficManagerEndpoint. Questo metodo è più semplice, perché non richiede l'indicizzazione nella matrice Endpoints nel profilo.<BR>

#### Esempio 1: Aggiornamento degli endpoint mediante Get-AzureRmTrafficManagerProfile e Set-AzureRmTrafficManagerProfile
In questo esempio viene modificata la priorità di due endpoint in un profilo esistente.

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG
	PS C:\> $profile.Endpoints[0].Priority = 2
	PS C:\> $profile.Endpoints[1].Priority = 1
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Esempio 2: Aggiornamento di un endpoint mediante Get-AzureRmTrafficManagerEndpoint e Set-AzureRmTrafficManagerEndpoint
In questo esempio verrà modificato il peso di un singolo endpoint in un profilo esistente.

	PS C:\> $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
	PS C:\> $endpoint.Weight = 20
	PS C:\> Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## Abilitazione e disabilitazione di endpoint e profili
Gestione traffico consente l'abilitazione e la disabilitazione dei singoli endpoint, oltre a consentire l'abilitazione e la disabilitazione di interi profili. Queste modifiche possono essere apportate ottenendo/aggiornando/impostando l'endpoint o le risorse del profilo. Per semplificare queste operazioni comuni, le modifiche sono supportate anche tramite cmdlet dedicati.

#### Esempio 1: Abilitazione e disabilitazione di un profilo di Gestione traffico
Per abilitare un profilo di Gestione traffico, usare Enable-AzureRmTrafficManagerProfile. Il profilo può essere specificato mediante un oggetto profilo, passato tramite la pipeline o mediante il parametro '-TrafficManagerProfile', oppure specificando direttamente il nome del profilo e il nome del gruppo di risorse, come illustrato nell'esempio.

	PS C:\> Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Analogamente, per disabilitare un profilo di Gestione traffico:

	PS C:\> Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Il cmdlet Disable-AzureRmTrafficManagerProfile richiederà una conferma. È possibile eliminare la richiesta di conferma mediante il parametro '-Force'.

#### Esempio 2: Abilitazione e disabilitazione di un endpoint di Gestione traffico
Per abilitare un endpoint di Gestione traffico, usare Enable-AzureRmTrafficManagerEndpoint. L'endpoint può essere specificato mediante un oggetto TrafficManagerEndpoint, passato tramite la pipeline o mediante il parametro '-TrafficManagerEndpoint', oppure specificando direttamente il nome dell'endpoint, il tipo dell'endpoint, il nome del profilo e il nome del gruppo di risorse:

	PS C:\> Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG

Analogamente, per disabilitare un endpoint di Gestione traffico:

 	PS C:\> Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force

Analogamente a Disable-AzureRmTrafficManagerProfile, il cmdlet Disable-AzureRmTrafficManagerEndpoint include una richiesta di conferma, che può essere eliminata mediante il parametro '-Force'.

## Eliminare un endpoint di Gestione traffico
Un modo per eliminare un endpoint di Gestione traffico consiste nel recuperare l'oggetto profilo mediante Get-AzureRmTrafficManagerProfile, aggiornare l'elenco di endpoint nell'oggetto profilo locale ed eseguire il commit delle modifiche mediante Set-AzureRmTrafficManagerProfile. Questo metodo consente di eseguire il commit di più modifiche agli endpoint contemporaneamente.

Un altro modo per rimuovere i singoli endpoint consiste nell'usare il cmdlet Remove-AzureRmTrafficManagerEndpoint:

	PS C:\> Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
	
Questo cmdlet richiederà una conferma, a meno che non si usi il parametro '-Force' per eliminare la richiesta di conferma.

## Eliminare un profilo di Gestione traffico
Per eliminare un profilo di Gestione traffico, usare il cmdlet Remove-AzureRmTrafficManagerProfile, specificando il nome del profilo e il nome del gruppo di risorse:

	PS C:\> Remove-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG [-Force]

Il cmdlet richiede una conferma. L'opzione facoltativa "-Force" può essere usata per eliminare questa richiesta. Inoltre, il profilo da eliminare può essere specificato utilizzando un oggetto di profilo:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Questa sequenza può anche essere inoltrata tramite pipe:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG | Remove-AzureTrafficManagerProfile [-Force]

## Passaggi successivi

[Monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

[Considerazioni sulle prestazioni di gestione traffico](traffic-manager-performance-considerations.md)
 

<!---HONumber=AcomDC_0323_2016-->