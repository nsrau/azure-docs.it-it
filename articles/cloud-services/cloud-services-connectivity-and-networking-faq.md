---
title: "Domande frequenti relative ai problemi di connettività e rete per Servizi cloud di Microsoft Azure| Microsoft Docs"
description: "Questo articolo elenca le domande frequenti relative alla connettività e alla rete per Servizi cloud di Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: genli
ms.openlocfilehash: e89549f51abb896c1ddf48a46de78fb5e4988f22
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemi di connettività e rete per Servizi cloud di Azure: domande frequenti

Questo articolo include le domande frequenti relative ai problemi di connettività e rete per [Servizi cloud di Azure](https://azure.microsoft.com/services/cloud-services). Per informazioni sulle dimensioni, vedere la [pagina Dimensioni delle macchine virtuali per i servizi cloud](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Non è possibile riservare un indirizzo IP in un servizio cloud con più indirizzi VIP.
Assicurarsi prima di tutto che l'istanza di macchina virtuale per cui si tenta di riservare l'indirizzo IP sia accesa. Verificare quindi che vengano usati indirizzi IP riservati sia per la distribuzione di staging che per quella di produzione. *Non* modificare le impostazioni durante l'aggiornamento della distribuzione.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Come si usa Desktop remoto quando si ha un gruppo di sicurezza di rete?
Aggiungere regole al gruppo di sicurezza di rete che consentono il traffico sulle porte **3389** e **20000**. Desktop remoto usa la porta **3389**. Dato che alle istanze del servizio cloud viene applicato il bilanciamento del carico, non è possibile controllare direttamente a quale istanza connettersi. Gli agenti *RemoteForwarder* e *RemoteAccess* gestiscono il traffico Remote Desktop Protocol (RDP) e consentono al client di inviare un cookie RDP e specificare una singola istanza a cui connettersi. Gli agenti *RemoteForwarder* e *RemoteAccess* richiedono che sia aperta la porta **20000**, che potrebbe essere bloccata in presenza di un gruppo di sicurezza di rete.

## <a name="can-i-ping-a-cloud-service"></a>È possibile eseguire il ping di un servizio cloud?

No, non usando il normale protocollo "ping"/ICMP. Il protocollo ICMP non è consentito in Azure Load Balancer.

Per testare la connettività, è consigliabile eseguire un ping di porta. Mentre Ping.exe usa ICMP, è possibile usare altri strumenti, come PSPing, Nmap e telnet, per testare la connettività a una porta TCP specifica.

Per altre informazioni, vedere [Use port pings instead of ICMP to test Azure VM connectivity](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/) (Usare i ping di porta per testare la connettività delle VM di Azure).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Come si può impedire la ricezione di migliaia di segnalazioni da indirizzi IP sconosciuti che potrebbero indicare un attacco dannoso al servizio cloud?
Azure implementa una sicurezza di rete su più livelli per proteggere i servizi di piattaforma dagli attacchi Distributed Denial of Service (DDoS). Il sistema di difesa dagli attacchi DDoS di Azure fa parte del processo continuo di monitoraggio di Azure, che viene costantemente migliorato tramite test di penetrazione. Questo sistema di difesa dagli attacchi DDoS è progettato per contrastare attacchi non solo dall'esterno, ma anche da altri tenant di Azure. Per altre informazioni, vedere [Azure network security](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (Sicurezza della rete di Azure).

È anche possibile creare un'attività di avvio per bloccare selettivamente alcuni indirizzi IP specifici. Per altre informazioni, vedere [Bloccare un indirizzo IP specifico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando si prova a usare RDP nell'istanza del servizio cloud, viene visualizzato il messaggio "L'account utente è scaduto".
Il messaggio di errore "L'account utente è scaduto" potrebbe venire visualizzato quando si ignora la data di scadenza configurata nelle impostazioni RDP. È possibile modificare la data di scadenza dal portale seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com), passare al servizio cloud e selezionare la scheda **Desktop remoto**.

2. Selezionare lo slot di distribuzione **Produzione** o **Gestione temporanea**.

3. Modificare la **data di scadenza** e quindi salvare la configurazione.

A questo punto dovrebbe essere possibile usare RDP nel computer.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Perché Azure Load Balancer non bilancia il carico del traffico in modo uniforme?
Per informazioni sul funzionamento del servizio di bilanciamento del carico interno, vedere [Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/) (Nuova modalità di distribuzione di Azure Load Balancer).

L'algoritmo di distribuzione usato è un hash a 5 tuple (IP di origine, porta di origine, IP di destinazione, porta di destinazione e tipo di protocollo) per eseguire il mapping del traffico ai server disponibili. La persistenza viene mantenuta solo all'interno di una sessione di trasporto. I pacchetti nella stessa sessione TCP o UDP vengono indirizzati alla stessa istanza di IP di data center (DIP) nell'endpoint con carico bilanciato. Quando il client chiude e riapre la connessione o avvia una nuova sessione dallo stesso IP di origine, la porta di origine cambia e il traffico quindi viene indirizzato a un endpoint DIP diverso.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>In che modo è possibile reindirizzare il traffico in ingresso per l'URL predefinito del servizio cloud a un URL personalizzato? 

URL Rewrite Module per IIS può essere usato per reindirizzare il traffico in arrivo all'URL predefinito per il servizio cloud (ad esempio, \*.cloudapp.net) ad alcuni nomi/URL personalizzati. Poiché URL Rewrite Module è abilitato nei ruoli Web per impostazione predefinita e le regole sono configurate nel file web.config dell'applicazione, è sempre disponibile nella macchina virtuale indipendentemente dai riavvii o dalla ricreazione delle immagini. Per altre informazioni, vedere:

- [Create rewrite rules for the URL Rewrite module](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module) (Creare regole di riscrittura per URL Rewrite Module)
- [Remove a default link](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top) (Rimuovere un collegamento predefinito)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Come posso bloccare/disabilitare il traffico in ingresso per l'URL predefinito del servizio cloud? 

È possibile impedire il traffico in ingresso per l'URL/nome predefinito del servizio cloud (ad esempio, \*.cloudapp.net). Impostare l'intestazione host su un nome DNS personalizzato (ad esempio, www.MyCloudService.com) nella configurazione del binding del sito nel file di definizione del servizio cloud (*.csdef), come indicato: 
 

    <?xml version="1.0" encoding="utf-8"?> 
    <ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
      <WebRole name="MyWebRole" vmsize="Small"> 
        <Sites> 
          <Site name="Web"> 
            <Bindings> 
              <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" /> 
            </Bindings> 
          </Site> 
        </Sites> 
        <Endpoints> 
          <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
        </Endpoints> 
        <ConfigurationSettings> 
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" /> 
        </ConfigurationSettings> 
      </WebRole> 
    </ServiceDefinition> 
 
Poiché questo binding dell'intestazione host viene applicato tramite il file csdef, il servizio è accessibile solo attraverso il nome personalizzato "www.MyCloudService.com". Tutte le richieste in ingresso per il dominio "*.cloudapp.net" hanno sempre esito negativo. Se nel servizio si usa un probe SLB personalizzato o un bilanciamento del carico interno, il blocco dell'URL/nome predefinito potrebbe interferire con il comportamento del probe. 

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Come assicurarsi che l'indirizzo IP pubblico di un servizio cloud non cambi mai?

Per assicurarsi che l'indirizzo IP pubblico del servizio cloud (noto anche come indirizzo VIP) non cambi mai in modo da poter essere abitualmente inserito nell'elenco elementi consentiti da alcuni client specifici, è consigliabile disporre di un indirizzo IP riservato ad esso associato. In caso contrario, l'indirizzo IP virtuale fornito da Azure viene deallocato dalla sottoscrizione se si elimina la distribuzione. Per una corretta operazione di scambio dell'indirizzo VIP, sono necessari singoli indirizzi IP riservati per gli slot di staging e di produzione. Senza di essi, l'operazione di scambio ha esito negativo. Per riservare un indirizzo IP e associarlo al servizio cloud, vedere questi articoli:
 
- [Riservare l'indirizzo IP di un servizio cloud esistente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associare un indirizzo IP riservato a un servizio cloud usando un file cscfg](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Se si dispone di più di un'istanza per i ruoli, l'associazione del RIP con il servizio cloud non dovrebbe causare alcun tempo di inattività. In alternativa, è possibile inserire l'intervallo IP del data center di Azure nell'elenco elementi consentiti. È possibile trovare tutti gli intervalli IP di Azure nell'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Questo file contiene gli intervalli di indirizzi IP, inclusi gli intervalli di calcolo, SQL e archiviazione, usati nei data center di Azure. Ogni settimana viene pubblicato un file aggiornato che riflette gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non vengono usati nei data center per almeno una settimana. Scaricare il nuovo file con estensione xml ogni settimana e apportare le modifiche necessarie nel sito per identificare correttamente i servizi in esecuzione in Azure. Gli utenti di Azure ExpressRoute potrebbero notare che questo file viene usato per aggiornare l'annuncio BGP dello spazio di Azure la prima settimana del mese. 

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Come è possibile usare le reti virtuali di Azure Resource Manager con i servizi cloud? 

I servizi cloud non possono trovarsi nelle reti virtuali di Azure Resource Manager. Le reti virtuali di Resource Manager e le reti virtuali della distribuzione classica possono essere connesse tramite peering. Per altre informazioni, vedere [Peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md).
