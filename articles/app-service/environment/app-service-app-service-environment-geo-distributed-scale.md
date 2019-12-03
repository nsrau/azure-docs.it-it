---
title: Scalabilità distribuita geografica
description: Informazioni sulla scalabilità orizzontale delle app mediante la distribuzione a livello geografico con Gestione traffico e ambienti del servizio app
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 7ab04e23b838f2dfd39b73476db7492947d62e6e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688808"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Ridimensionamento in diverse aree geografiche con ambienti del servizio app
## <a name="overview"></a>Panoramica

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Gli scenari di applicazioni che richiedono una scalabilità molto elevata possono superare la capacità delle risorse di calcolo disponibili per una singola distribuzione di un'app.  Applicazioni di voto, eventi sportivi ed eventi di intrattenimento televisivi sono tutti esempi di scenari che richiedono una scalabilità estremamente elevata. I requisiti di scalabilità elevata possono essere soddisfatti con la scalabilità orizzontale delle applicazioni, con più distribuzioni di app eseguite in una singola area geografica, oltre che in diverse aree, per gestire i requisiti di carico particolarmente intenso.

Gli ambienti del servizio app sono una piattaforma ideale per la scalabilità orizzontale orizzontale.  Una volta selezionata una configurazione di ambiente del servizio app in grado di supportare una frequenza di richieste note, gli sviluppatori possono distribuire altri ambienti del servizio app in modalità di "cookie cutter" per ottenere una capacità di carico di picco desiderata.

Si supponga ad esempio che un'app eseguita in una configurazione di Ambiente del servizio app sia stata testata per la gestione di 20 KB di richieste al secondo (RPS).  Se la capacità di carico di picco desiderata è pari a 100 KB RPS, è possibile creare cinque (5) ambienti del servizio app per assicurare che l'applicazione possa gestire il carico massimo previsto.

Poiché in genere i clienti accedono alle app usando un dominio personalizzato, o personale, gli sviluppatori hanno bisogno di un modo per distribuire le richieste di app tra tutte le istanze di Ambiente del servizio app.  Un ottimo modo per eseguire questa operazione consiste nel risolvere il dominio personalizzato usando un [profilo di gestione traffico di Azure][AzureTrafficManagerProfile].  Il profilo di Gestione traffico può essere configurato in modo che punti a tutti i singoli ambienti del servizio app.  Gestione traffico gestirà automaticamente la distribuzione dei clienti tra tutti gli ambienti del servizio app in base alle impostazioni di bilanciamento del carico nel profilo di Gestione traffico.  Questo approccio funziona indipendentemente dal fatto che tutti gli ambienti del servizio app siano distribuiti in una singola area di Azure o a livello mondiale tra più aree di Azure.

Inoltre, poiché i clienti accedono alle app tramite il dominio personale, non conoscono il numero di ambienti del servizio app che eseguono un'app.  Di conseguenza, gli sviluppatori possono aggiungere e rimuovere in modo rapido e facile ambienti del servizio app in base al carico di traffico osservato.

Il diagramma concettuale seguente illustra un'app con scalabilità orizzontale fra tre ambienti del servizio app in una singola area geografica.

![Architettura concettuale][ConceptualArchitecture] 

Il resto di questo argomento illustra in dettaglio la procedura prevista per configurare una topologia distribuita per l'app di esempio con più ambienti del servizio app.

## <a name="planning-the-topology"></a>Pianificazione della topologia
Prima di compilare il footprint di un'app distribuita, è utile avere anticipatamente alcune informazioni.

* **Dominio personalizzato per l'app**: definire qual è il nome del dominio personalizzato che i clienti useranno per accedere all'app.  Per l'app di esempio il nome di dominio personalizzato è `www.scalableasedemo.com`
* **Dominio di Traffic Manager:**  Quando si crea un [profilo di gestione traffico di Azure][AzureTrafficManagerProfile], è necessario scegliere un nome di dominio.  Questo nome sarà combinato con il suffisso *trafficmanager.net* per registrare una voce di dominio gestita da Gestione traffico.  Per l'app di esempio il nome scelto è *scalable-ase-demo*.  Il nome di dominio completo gestito da Gestione traffico sarà quindi *scalable-ase-demo.trafficmanager.net*.
* **Strategia per la scalabilità del footprint dell'app:** definire se il footprint dell'applicazione sarà distribuito tra più ambienti del servizio app in una singola area geografica,  in più aree geografiche  o con una combinazione di entrambi gli approcci.  La decisione dovrà essere basata sulla previsione dell'origine del traffico dei clienti, oltre che sull'efficacia della scalabilità del resto dell'infrastruttura di back-end di supporto di un'app.  Ad esempio, un'applicazione al 100% senza stato può essere considerevolmente ridimensionata usando una combinazione di più ambienti del servizio app per ogni area di Azure, moltiplicati per gli ambienti del servizio app distribuiti tra più aree di Azure.  Con la disponibilità di oltre 15 aree di Azure pubbliche da cui scegliere, i clienti possono veramente creare un footprint dell'applicazione iperscalabile in tutto il mondo.  Per l'app di esempio usata per questo articolo sono stati creati tre ambienti del servizio app in una singola area di Azure (Stati Uniti centro-meridionali).
* **Convenzione di denominazione per gli ambienti del servizio app**: ogni ambiente del servizio app richiede un nome univoco.  Oltre a uno o due ambienti del servizio app, è utile avere una convenzione di denominazione per facilitare l'identificazione di ogni ambiente del servizio app.  Per l'app di esempio è stata usata una convenzione di denominazione semplice.  I nomi dei tre ambienti del servizio app sono *fe1ase*, *fe2ase* e *fe3ase*.
* **Convenzione di denominazione per le app:** poiché saranno distribuite più istanze dell'app, è necessario un nome per ogni istanza dell'app distribuita.  Una funzionalità poco conosciuta ma molto pratica degli ambienti del servizio app è la possibilità di usare lo stesso nome tra più ambienti.  Poiché ogni ambiente del servizio app ha un suffisso univoco, gli sviluppatori possono scegliere di riutilizzare esattamente lo stesso nome dell'app in ogni ambiente.  Ad esempio, uno sviluppatore potrebbe avere app denominate come segue: *MyApp.foo1.p.azurewebsites.NET*, *MyApp.foo2.p.azurewebsites.NET*, *MyApp.foo3.p.azurewebsites.NET*e così via.  Per l'app di esempio anche se ogni istanza dell'app ha un nome univoco.  I nomi delle istanze dell'app usati sono *webfrontend1*, *webfrontend2* e *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Configurazione del profilo di Gestione traffico
Dopo la distribuzione di più istanze di un'app in più ambienti del servizio app, è possibile registrare le singole istanze con Gestione traffico.  Per l'app di esempio è necessario un profilo di Gestione traffico per *scalable-ase-demo.trafficmanager.net* che possa indirizzare i clienti a una qualsiasi delle istanze seguenti dell'app distribuita:

* **webfrontend1.fe1ase.p.azurewebsites.net**: istanza dell'app di esempio distribuita nel primo ambiente del servizio app.
* **webfrontend2.fe2ase.p.azurewebsites.net**: istanza dell'app di esempio distribuita nel secondo ambiente del servizio app.
* **webfrontend3.fe3ase.p.azurewebsites.net**: istanza dell'app di esempio distribuita nel terzo ambiente del servizio app.

Il modo più semplice per registrare più endpoint di servizio app Azure, tutti in esecuzione nella **stessa** area di Azure, è il [supporto di PowerShell Azure Resource Manager gestione traffico][ARMTrafficManager].  

Come primo passaggio creare un profilo di Gestione traffico di Azure.  Il codice seguente illustra come è stato creato il profilo per l'app di esempio:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Si noti che il parametro *RelativeDnsName* è stato impostato su *scalable-ase-demo*.  È così che il nome di dominio *scalable-ase-demo.trafficmanager.net* viene creato e associato al profilo di Gestione traffico.

Il parametro *TrafficRoutingMethod* definisce i criteri di bilanciamento del carico che saranno usati da Gestione traffico per determinare come distribuire il carico del cliente tra tutti gli endpoint disponibili.  In questo esempio è stato usato il metodo *Weighted* .  Le richieste del cliente saranno quindi distribuite tra tutti gli endpoint dell'applicazione registrata in base ai pesi associati a ogni endpoint. 

Dopo la creazione del profilo, ogni istanza dell'app viene aggiunta al profilo come endpoint nativo di Azure.  Il codice seguente recupera un riferimento a ogni app Web front-end e quindi aggiunge ogni app come endpoint di Gestione traffico tramite il parametro *TargetResourceId* .

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Come si noterà, è presente una chiamata a *Add-AzureTrafficManagerEndpointConfig* per ogni singola istanza dell'app.  Il parametro *TargetResourceId* in ogni comando di Powershell fa riferimento a una delle tre istanze dell'app distribuite.  Il profilo di Gestione traffico distribuirà il carico tra tutti e tre gli endpoint registrati nel profilo.

Tutti e tre gli endpoint usano lo stesso valore (10) per il parametro *Weight* .  Gestione traffico distribuirà quindi le richieste dei clienti tra tutte e tre le istanze dell'app in modo relativamente uniforme. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Indirizzamento del dominio personalizzato dell'app al dominio di Gestione traffico
Come passaggio finale è necessario che il dominio personalizzato dell'app punti al dominio di Gestione traffico.  Per l'app di esempio, significa puntare `www.scalableasedemo.com` in `scalable-ase-demo.trafficmanager.net`.  Questo passaggio deve essere completato con il registrar che gestisce il dominio personalizzato.  

Usando gli strumenti di gestione del dominio del registrar, è necessario creare un record CNAME che dal dominio personalizzato punti al dominio di Gestione traffico.  L'immagine seguente mostra un esempio di come appare la configurazione di questo CNAME:

![CNAME per il dominio personalizzato][CNAMEforCustomDomain] 

Anche se questo aspetto non viene trattato in questo argomento, tenere presente che per ogni singola istanza dell'app deve essere registrato anche il dominio personalizzato.  In caso contrario, se una richiesta raggiunge un'istanza dell'app e per l'app non è stato registrato il dominio personalizzato, la richiesta non riuscirà.  

In questo esempio il dominio personalizzato è `www.scalableasedemo.com`e a ogni istanza dell'applicazione è associato il dominio personalizzato.

![Dominio personalizzato][CustomDomain] 

Per un riepilogo della registrazione di un dominio personalizzato con app Azure app di servizio, vedere l'articolo seguente sulla [registrazione di domini personalizzati][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Prova della topologia distribuita
Il risultato finale della configurazione di gestione traffico e DNS è che le richieste di `www.scalableasedemo.com` passano attraverso la sequenza seguente:

1. Un browser o un dispositivo effettuerà una ricerca DNS per `www.scalableasedemo.com`
2. La voce CNAME del registrar causa il reindirizzamento della ricerca DNS a Gestione traffico di Azure.
3. Viene eseguita una ricerca DNS di *scalable-ase-demo.trafficmanager.net* in uno dei server DNS di Gestione traffico di Azure.
4. In base ai criteri di bilanciamento del carico, ovvero il parametro *TrafficRoutingMethod* usato in precedenza durante la creazione del profilo di Gestione traffico, Gestione traffico sceglierà uno degli endpoint configurati e restituirà l'FQDN dell'endpoint al browser o al dispositivo.
5. Poiché l'FQDN dell'endpoint è l'URL di un'istanza dell'app in esecuzione in un ambiente del servizio app, il browser o il dispositivo chiederà a un server DNS di Microsoft Azure di risolvere l'FQDN in un indirizzo IP. 
6. Il browser o il dispositivo invierà la richiesta HTTP/S all'indirizzo IP.  
7. La richiesta arriverà a una delle istanze dell'app in esecuzione in uno degli ambienti del servizio app.

L'immagine della console seguente mostra una ricerca DNS del dominio personalizzato dell'app di esempio risolta correttamente in un'istanza dell'app eseguita in uno dei tre ambienti del servizio app di esempio, in questo caso il secondo dei tre ambienti del servizio app:

![Ricerca DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Informazioni e collegamenti aggiuntivi
Documentazione sul supporto di [Gestione traffico][ARMTrafficManager]di PowerShell Azure Resource Manager.  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
