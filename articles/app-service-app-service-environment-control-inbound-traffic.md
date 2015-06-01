<properties 
	pageTitle="Come controllare il traffico in ingresso a un ambiente del servizio app" 
	description="Informazioni su come configurare le regole di sicurezza di rete per controllare il traffico in ingresso a un ambiente del servizio app." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# Come controllare il traffico in ingresso a un ambiente del servizio app

## Panoramica ##
Un ambiente del servizio app viene sempre creato in una subnet di una [rete virtuale][virtualnetwork] regionale. È possibile definire una nuova rete virtuale regionale e una nuova subnet al momento della creazione di un ambiente del servizio app. In alternativa, è possibile creare un ambiente del servizio app in una rete virtuale regionale e in una subnet preesistenti. Per altre informazioni su come creare un ambiente del servizio app, vedere [Come creare un ambiente del servizio app][HowToCreateAnAppServiceEnvironment].

È sempre necessario creare un ambiente del servizio app all'interno di una subnet perché la subnet fornisce un limite di rete che può essere usato per bloccare il traffico in ingresso proveniente da dispositivi e servizi upstream, in modo che il traffico HTTP e HTTPS sia accettato solo da indirizzi IP upstream specifici.

Il traffico in ingresso e in uscita a e da una subnet è controllato tramite un [gruppo di sicurezza di rete][NetworkSecurityGroups]. Per controllare il traffico in ingresso è necessario creare regole di sicurezza di rete in un gruppo di sicurezza di rete, quindi assegnare al gruppo di sicurezza di rete la subnet contenente l'ambiente del servizio app.

Dopo aver assegnato un gruppo di sicurezza di rete a una subnet, il traffico in ingresso alle app nell'ambiente del servizio app è consentito/bloccato in base alle regole di autorizzazione consentita o negata definite nel gruppo di sicurezza di rete.

## Porte di rete usate in un ambiente del servizio app ##
Prima di bloccare il traffico di rete in ingresso tramite un gruppo di sicurezza di rete, è importante conoscere quali sono le porte di rete obbligatorie e facoltative usate da un ambiente del servizio app. Il blocco accidentale del traffico ad alcune porte può comportare una perdita di funzionalità nell'ambiente del servizio app.

Di seguito è riportato un elenco delle porte usate da un ambiente del servizio app:

- 454: **porta obbligatoria** usata dall'infrastruttura di Azure per la gestione e la manutenzione degli ambienti del servizio app. Non bloccare il traffico indirizzato a questa porta.
- 455: **porta obbligatoria** usata dall'infrastruttura di Azure per la gestione e la manutenzione degli ambienti del servizio app. Non bloccare il traffico indirizzato a questa porta.
- 80: porta predefinita per il traffico HTTP in ingresso alle app in esecuzione nei piani del servizio app in un ambiente del servizio app
- 443: porta predefinita per il traffico SSL in ingresso alle app in esecuzione nei piani del servizio app in un ambiente del servizio app
- 21: canale di controllo per il servizio FTP. Questa porta può essere bloccata, se non si usa un servizio FTP.
- 10001-10020: canali di dati per il servizio FTP. Come per il canale di controllo, queste porte possono essere bloccate se non si usa il servizio FTP. **Nota:** i canali di dati per FTP potrebbero variare durante il periodo di anteprima del servizio.
- 4016: porta usata per il debug remoto con Visual Studio 2012. Questa porta può essere bloccata, se non si usa questa funzionalità.
- 4018: porta usata per il debug remoto con Visual Studio 2013. Questa porta può essere bloccata, se non si usa questa funzionalità.
- 4020: porta usata per il debug remoto con Visual Studio 2015. Questa porta può essere bloccata, se non si usa questa funzionalità.

## Creazione di un gruppo di sicurezza di rete ##
Per i dettagli sul funzionamento dei gruppi di sicurezza di rete, vedere le [informazioni][NetworkSecurityGroups] seguenti. Di seguito sono riportate le informazioni principali relative ai gruppi di sicurezza di rete, con particolare attenzione alla configurazione e all'applicazione di un gruppo di sicurezza di rete a una subnet contenente un ambiente del servizio app.

I gruppi di sicurezza di rete vengono innanzitutto creati come un'entità autonoma associata a una sottoscrizione. Poiché i gruppi di sicurezza di rete vengono creati in un'area di Azure, assicurarsi di creare il gruppo di sicurezza di rete nella stessa area dell'ambiente del servizio app.

Di seguito è illustrata la procedura di creazione di un gruppo di sicurezza di rete:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Dopo aver creato il gruppo di sicurezza di rete, vengono aggiunte una o più regole di sicurezza di rete. Poiché il set di regole può variare nel tempo, è consigliabile lasciare spazi vuoti nello schema di numerazione usato per le priorità delle regole al fine di agevolare l'inserimento di regole aggiuntive in futuro.

L'esempio seguente illustra una regola che concede l'accesso in modo esplicito alle porte necessarie per la gestione e la manutenzione dell'ambiente del servizio app da parte dell'infrastruttura di Azure. Si noti che tutto il traffico di gestione transita attraverso il protocollo SSL ed è protetto tramite certificati client, quindi anche se le porte sono aperte risultano inaccessibili a entità diverse dall'infrastruttura di gestione di Azure.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Quando si blocca l'accesso alle porte 80 e 443 per "nascondere" un ambiente del servizio app oltre i dispositivi o servizi upstream, è necessario conoscere l'indirizzo IP upstream. Se, ad esempio, si un firewall per applicazioni Web, il firewall disporrà di uno o più indirizzi IP che userà per l'inoltro del traffico a un ambiente del servizio app downstream. Sarà necessario usare questo indirizzo IP nel parametro *SourceAddressPrefix* di una regola di sicurezza di rete.

Nell'esempio seguente, il traffico in ingresso da un indirizzo IP upstream specifico è consentito in modo esplicito. L'indirizzo *1.2.3.4* è usato come segnaposto per l'indirizzo IP di un firewall per applicazioni Web upstream. Modificare il valore in modo che corrisponda all'indirizzo usato dal dispositivo o dal servizio upstream corrente.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Se si desidera usufruire del supporto per FTP, è possibile usare le regole seguenti come un modello per concedere l'accesso alla porta di controllo FTP e alle porte dei canali di dati. Poiché FTP è un protocollo con stato, potrebbe non essere possibile instradare il traffico FTP attraverso un dispositivo proxy o un firewall HTTP/HTTPS tradizionale. In questo caso sarà necessario impostare *SourceAddressPrefix* su un valore diverso, ad esempio l'intervallo di indirizzi IP per i computer dedicati allo sviluppo o alla distribuzione in cui sono in esecuzione i client FTP.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

**Nota:** l'intervallo delle porte dei canali di dati per FTP potrebbero variare durante il periodo di anteprima del servizio.

Se si usa la funzionalità di debug remoto con Visual Studio, usare le regole seguenti per concedere l'accesso. Esiste una regola separata per ogni versione supportata di Visual Studio, dal momento che ogni versione usa una porta diversa per il debug remoto. Come per l'accesso FTP, il traffico di debug remoto potrebbe non transitare correttamente attraverso un dispositivo proxy o un firewall per applicazioni Web tradizionale. È quindi possibile impostare *SourceAddressPrefix* sull'intervallo di indirizzi IP dei computer di sviluppo che eseguono Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## Assegnazione di un gruppo di sicurezza di rete a una subnet ##
Un gruppo di sicurezza di rete include una regola di sicurezza predefinita che nega l'accesso a tutto il traffico esterno. La combinazione delle regole di sicurezza di rete descritte in precedenza e della regola di sicurezza predefinita che blocca il traffico in ingresso determina che solo il traffico proveniente da intervalli di indirizzi di origine associati a un'azione *Consenti* potranno inviare il traffico alle app in esecuzione in un ambiente del servizio app.

Quando un gruppo di sicurezza di rete è stato popolato con le regole di sicurezza, deve essere assegnato alla subnet contenente l'ambiente del servizio app. Il comando di assegnazione fa riferimento sia al nome della rete virtuale in cui risiede l'ambiente del servizio app, sia al nome della subnet in cui è stato creato l'ambiente.

L'esempio seguente illustra l'assegnazione di un gruppo di sicurezza di rete a una subnet e a una rete virtuale:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Dopo l'assegnazione del gruppo di sicurezza di rete (si tratta di un'operazione con esecuzione prolungata che potrebbe richiedere alcuni minuti), solo il traffico in ingresso che corrisponde alle regole *Consenti* potrà raggiungere le app nell'ambiente del servizio app.

Per completezza, di seguito viene mostrato un esempio di come rimuovere e quindi dissociare il gruppo di sicurezza di rete dalla subnet:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## Considerazioni speciali su IP SSL esplicito ##
Se un'app è configurata con un indirizzo IP esplicito anziché con l'indirizzo IP predefinito dell'ambiente del servizio app, il traffico HTTP e HTTPS viene indirizzato alla subnet tramite un set di porte diverso dalle porte 80 e 443.

Durante l'anteprima iniziale degli ambienti del servizio app, non sarà possibile determinare le porte specifiche usate da IP SSL. Tuttavia, non appena questa informazione verrà esposta tramite il portale, gli strumenti da riga di comando e le API REST, gli sviluppatori potranno configurare i gruppi di sicurezza di rete in modo da controllare anche il traffico tramite queste porte.

## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app][IntroToAppServiceEnvironment].

Per informazioni dettagliate sulle app che si connettono in modo sicuro alla risorsa back-end da un ambiente del servizio app, vedere [Connessione sicura alle risorse back-end da un ambiente del servizio app][SecurelyConnecttoBackend]

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/

<!-- IMAGES -->

<!--HONumber=52-->
