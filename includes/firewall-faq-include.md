---
title: File di inclusione
description: File di inclusione
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 8/13/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a63a12658bd0a4b4d018d51824af9814691a3cbf
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "40183009"
---
### <a name="what-is-azure-firewall"></a>Informazioni sul firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali. Firewall di Azure è attualmente in anteprima pubblica.

### <a name="which-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Quali funzionalità sono supportate nella versione di anteprima pubblica di Firewall di Azure?  

* Firewall con stato come servizio
* Disponibilità elevata e scalabilità cloud senza limiti
* Filtro dei nomi di dominio completi 
* Regole di filtro per il traffico di rete
* Supporto SNAT in uscita
* Possibilità di creare, applicare e registrare criteri di connettività di applicazioni e rete in modo centralizzato tra le reti virtuali e le sottoscrizioni di Azure
* Integrazione completa con Monitoraggio di Azure per la registrazione e l'analisi 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Come partecipare all'anteprima pubblica di Firewall di Azure?

Firewall di Azure è attualmente un'anteprima pubblica gestita a cui è possibile partecipare usando il comando Register-AzureRmProviderFeature di PowerShell. Questo comando è illustrato nella documentazione dell'anteprima pubblica di Firewall di Azure.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Quali prezzi vengono applicati per Firewall di Azure?

Firewall di Azure ha un costo fisso e un costo variabile. Di seguito sono indicati i prezzi, a cui viene applicato uno sconto del 50% durante l'anteprima pubblica.

* Tariffa fissa: 0,125 USD all'ora per unità firewall
* Tariffa variabile: 0,03 USD per GB elaborato dal firewall (traffico dati in ingresso o in uscita)

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual è il modello di distribuzione tipico per Firewall di Azure?

È possibile distribuire Firewall di Azure in qualsiasi rete virtuale, ma i clienti distribuiscono in genere Firewall di Azure in una rete virtuale centrale e configurano il peering di altre reti virtuali verso tale rete in un modello di tipo hub-spoke. È quindi possibile impostare la route predefinita dalle reti virtuali con peering verso la rete virtuale centrale del firewall.

### <a name="how-can-i-install-the-azure-firewall"></a>Come si installa Firewall di Azure?

È possibile configurare Firewall di Azure tramite il portale di Azure, PowerShell o l'API REST oppure usando modelli. Per istruzioni dettagliate, vedere [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](../articles/firewall/tutorial-firewall-deploy-portal.md).

### <a name="what-are-some-azure-firewall-concepts"></a>Su quali concetti si basa Firewall di Azure?

Firewall di Azure supporta regole e raccolte di regole. Una raccolta di regole è un set di regole con lo stesso ordine e la stessa priorità. Le raccolte di regole vengono eseguite in ordine di priorità. Quelle di rete hanno maggiore priorità rispetto a quelle di applicazione e tutte le regole sono di terminazione.

Esistono due tipi di raccolte di regole:

* *Regole di applicazione*: consentono di configurare i nomi di dominio completi (FQDN) accessibili da una subnet. 
* *Regole di rete*: consentono di configurare le regole che contengono indirizzi di origine, protocolli, porte di destinazione e indirizzi di destinazione. 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Firewall di Azure supporta il filtraggio del traffico in ingresso?

L'anteprima pubblica di Firewall di Azure supporta solo il filtraggio in uscita. La protezione in ingresso per i protocolli non HTTP/S, come RDP, SSH o FTP, è provvisoriamente pianificata per la versione con disponibilità generale di Firewall di Azure.  
 
### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quali servizi di registrazione e analisi sono supportati da Firewall di Azure?

Firewall di Azure è integrato con Monitoraggio di Azure per la visualizzazione e l'analisi dei log di firewall. I log possono essere inviati a Log Analytics, Archiviazione di Azure o Hub eventi e possono essere analizzati in Log Analytics o con strumenti diversi, ad esempio Excel e Power BI. Per altre informazioni, vedere [Esercitazione: Monitorare i log di Firewall di Azure](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Come funziona Firewall di Azure rispetto ad altri servizi esistenti, come le appliance virtuali di rete, nel marketplace?

Firewall di Azure è un servizio firewall di base che consente di risolvere determinati scenari dei clienti. Si presuppone che ci sia una combinazione di appliance virtuali di rete di terze parti e Firewall di Azure. L'integrazione è una priorità fondamentale.
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual è la differenza tra la funzionalità Web application firewall del gateway applicazione e Firewall di Azure?

Web application firewall (WAF) è una funzionalità del gateway applicazione che offre una protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Firewall di Azure fornisce la protezione a livello di rete in uscita per tutte le porte e tutti i protocolli e la protezione a livello di applicazione per HTTP/S in uscita. La protezione in ingresso per i protocolli non HTTP/S, come RDP, SSH e FTP, è provvisoriamente pianificata per la versione con disponibilità generale di Firewall di Azure.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual è la differenza tra i gruppi di sicurezza di rete e Firewall di Azure?

Il servizio Firewall di Azure si integra con la funzionalità dei gruppi sicurezza di rete offrendo una migliore sicurezza di rete con strategie di difesa avanzate. I gruppi di sicurezza di rete forniscono un filtraggio del traffico distribuito a livello di rete per limitare il traffico verso le risorse all'interno delle reti virtuali in ogni sottoscrizione. Firewall di Azure è un firewall di rete centralizzato con stato completo, distribuito come servizio, che fornisce protezione a livello di rete e di applicazione in diverse sottoscrizioni e reti virtuali. 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Come si configura Firewall di Azure con gli endpoint di servizio?

Per l'accesso sicuro ai servizi PaaS, è consigliabile usare gli endpoint di servizio. È possibile scegliere di abilitare gli endpoint di servizio nella subnet di Firewall di Azure e disabilitarli nelle reti virtuali spoke connesse. In questo modo si possono sfruttare entrambe le funzionalità: sicurezza degli endpoint di servizio e registrazione centrale per tutto il traffico.

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Come è possibile arrestare e avviare il Firewall di Azure?

È possibile usare i metodi di *deallocazione* e *allocazione* di Azure PowerShell.

Ad esempio: 

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

### <a name="what-are-the-known-service-limits"></a>Quali sono i limiti noti del servizio?

* Firewall di Azure ha un limite non rigido di 1000 TB al mese per unità firewall. 
* Un'istanza di Firewall di Azure in esecuzione in una rete virtuale centrale è soggetta alle limitazioni relative al peering delle reti virtuali, ovvero un massimo di 50 reti virtuali spoke.  
* Firewall di Azure non consente il peering globale. È quindi necessaria almeno una distribuzione di Firewall per ogni area.
* Firewall di Azure supporta 10.000 regole di applicazione e 10.000 regole di rete.