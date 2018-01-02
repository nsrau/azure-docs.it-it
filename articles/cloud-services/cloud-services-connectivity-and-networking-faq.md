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
ms.openlocfilehash: d86f3ec043c504c9d79b18f1f0b4c9cf0adb115b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemi di connettività e rete per Servizi cloud di Azure: domande frequenti

Questo articolo include le domande frequenti relative ai problemi di connettività e rete per [Servizi cloud di Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Per informazioni sulle dimensioni, vedere la pagina [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Non è possibile riservare un indirizzo IP in un servizio cloud con più indirizzi VIP
Assicurarsi prima di tutto che l'istanza di macchina virtuale per cui si sta provando a riservare l'indirizzo IP sia accesa. Assicurarsi quindi che vengano usati indirizzi IP riservati sia per la distribuzione di staging che per quella di produzione. **Non** modificare le impostazioni durante l'aggiornamento della distribuzione.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Come si usa Desktop remoto quando si ha un gruppo di sicurezza di rete?
Aggiungere regole al gruppo di sicurezza di rete che consentono il traffico sulle porte **3389** e **20000**.  Il desktop remoto usa la porta **3389**.  Dato che alle istanze del servizio cloud viene applicato il bilanciamento del carico, non è possibile controllare direttamente a quale istanza connettersi.  Gli agenti *RemoteForwarder* e *RemoteAccess* gestiscono il traffico RDP e consentono al client di inviare un cookie RDP e specificare una singola istanza a cui connettersi.  Gli agenti *RemoteForwarder* e *RemoteAccess* richiedono che la porta **20000** sia aperta e tale porta potrebbe essere bloccata in presenza di un gruppo di sicurezza di rete (NSG).

## <a name="can-i-ping-a-cloud-service"></a>È possibile eseguire il ping di un servizio cloud?

No, non usando il normale protocollo "ping"/ICMP. Il protocollo ICMP non è consentito in Azure Load Balancer.

Per testare la connettività, è consigliabile eseguire un ping di porta. Mentre Ping.exe usa ICMP, altri strumenti, come PSPing, Nmap e telnet, consentono di testare la connettività a una porta TCP specifica.

Per altre informazioni, vedere [Use port pings instead of ICMP to test Azure VM connectivity](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/) (Usare i ping di porta per testare la connettività delle VM di Azure).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>Come si può impedire la ricezione di migliaia di segnalazioni da indirizzi IP sconosciuti che indicano una qualche forma di attacco dannoso al servizio cloud?
Azure implementa una sicurezza di rete su più livelli per proteggere i servizi di piattaforma dagli attacchi Distributed Denial of Service (DDoS). Il sistema di difesa dagli attacchi DDoS di Azure fa parte del processo continuo di monitoraggio di Azure, che viene costantemente migliorato tramite test di penetrazione. Questo sistema di difesa dagli attacchi DDoS è progettato per contrastare attacchi non solo dall'esterno, ma anche da altri tenant di Azure. Per altre informazioni, vedere [Microsoft Azure Network Security](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (Sicurezza di rete di Microsoft Azure).

È anche possibile creare un'attività di avvio per bloccare selettivamente alcuni indirizzi IP specifici. Per altre informazioni, vedere [Bloccare un indirizzo IP specifico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando si prova a usare RDP nell'istanza del servizio cloud, viene visualizzato il messaggio "L'account utente è scaduto".
Il messaggio di errore "L'account utente è scaduto" potrebbe venire visualizzato quando si ignora la data di scadenza configurata nelle impostazioni RDP. È possibile modificare la data di scadenza dal portale seguendo questa procedura:
1. Accedere al [portale di Azure](https://portal.azure.com), passare al servizio cloud e selezionare la scheda **Desktop remoto**.
2. Selezionare lo slot di distribuzione **Produzione** o **Gestione temporanea**.
3. Modificare la data di scadenza e quindi salvare la configurazione.

A questo punto dovrebbe essere possibile usare RDP nel computer.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>Perché LoadBalancer non bilancia il carico del traffico in modo uniforme?
Per informazioni sul funzionamento del servizio di bilanciamento del carico interno, vedere [Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/) (Nuova modalità di distribuzione di Azure Load Balancer).

L'algoritmo di distribuzione usato è un hash a 5 tuple (IP di origine, porta di origine, IP di destinazione, porta di destinazione, tipo di protocollo) per eseguire il mapping del traffico ai server disponibili. La persistenza viene mantenuta solo all'interno di una sessione di trasporto. I pacchetti nella stessa sessione TCP o UDP verranno indirizzati alla stessa istanza di IP di data center (DIP) nell'endpoint con carico bilanciato. Quando il client chiude e riapre la connessione o avvia una nuova sessione dallo stesso IP di origine, la porta di origine cambia e il traffico quindi viene indirizzato a un endpoint DIP diverso.

## <a name="how-can-i-redirect-the-incoming-traffic-to-my-default-url-of-cloud-service-to-a-custom-url"></a>In che modo è possibile reindirizzare il traffico in ingresso per l'URL predefinito del servizio Cloud a un URL personalizzato? 

Il modulo di riscrittura URL IIS può essere usato per reindirizzare il traffico in arrivo all'URL predefinito per il servizio cloud (ad esempio, \*.cloudapp.net) ad alcuni Nomi DNS/URL personalizzati. Poiché il modulo di riscrittura URL è per impostazione predefinita abilitato nei ruoli Web e le regole sono configurate in Web.config dell'applicazione, sarà sempre disponibile nella macchina virtuale indipendentemente dai riavvii/ricreazione delle immagini. Per altre informazioni, vedere:

- [Creazione di regole di riscrittura per il modulo di riscrittura URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Come rimuovere il collegamento predefinito](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-the-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Come posso bloccare/disabilitare il traffico in ingresso per l'URL predefinito del servizio cloud? 

È possibile impedire il traffico in ingresso per l'URL/Nome predefinito del servizio cloud (ad esempio, \*.cloudapp.net) impostando l'intestazione host su un nome DNS personalizzato (ad esempio www.MyCloudService.com) nella configurazione di associazione del sito nel file di definizione del servizio cloud ( * .csdef) come indicato di seguito: 
 

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
 
Poiché l'associazione di intestazione host viene applicata tramite il file csdef, il servizio sarebbe accessibile solo tramite il nome personalizzato 'www.MyCloudService.com', mentre tutte le richieste in ingresso nel dominio ' * .cloudapp.net' avrebbero sempre esito negativo. Ciò premesso, se nel servizio si usa un probe SLB personalizzato o un bilanciamento interno del carico, il blocco dell'URL/Nome predefinito può interferire con il comportamento del probe. 

## <a name="how-to-make-sure-the-public-facing-ip-address-of-a-cloud-service-aka-vip-never-changes-so-that-it-could-be-customarily-whitelisted-by-few-specific-clients"></a>Come assicurare che l'indirizzo IP pubblico di un servizio cloud (noto anche come, VIP) non cambi mai in modo che possa essere di norma inserito nell'elenco elementi consentiti da alcuni client specifici?

Per inserire l'indirizzo IP dei servizi cloud nell'elenco elementi consentiti si consiglia di disporre di un indirizzo IP riservato associato. In caso contrario l'indirizzo IP virtuale fornito da Azure verrà deallocato dalla sottoscrizione se si elimina la distribuzione. Si noti che per una corretta operazione di scambio VIP è necessario avere singoli indirizzi IP riservati per gli slot di produzione e di staging, in assenza dei quali l'operazione di scambio avrà esito negativo. Seguire questi articoli per riservare un indirizzo IP e associarlo con i servizi cloud:  
 
- [Riservare l'indirizzo IP di un servizio cloud esistente](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associare un indirizzo IP riservato a un servizio cloud usando un file cscfg](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Fino a quando si dispone di più di un'istanza per i ruoli, l'associazione RIP con il servizio cloud non dovrebbe causare alcun tempo di inattività. In alternativa, è possibile inserire l'intervallo IP del Data Center di Azure nell'elenco elementi consentiti. È possibile trovare tutti gli intervalli IP di Azure [qui](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Questo file contiene gli intervalli di indirizzi IP, inclusi gli intervalli di Calcolo, SQL e Archiviazione, usati nei data center di Microsoft Azure. Ogni settimana viene pubblicato un file che riflette gli intervalli attualmente distribuiti e le eventuali modifiche imminenti agli intervalli IP. I nuovi intervalli riportati nel file non verranno usati nei data center per almeno una settimana. Scaricare il nuovo file XML ogni settimana e apportare le modifiche necessarie nel sito per identificare correttamente i servizi in esecuzione in Azure. Gli utenti di ExpressRoute possono notare che questo file viene usato per aggiornare l'annuncio BGP dello spazio di Azure la prima settimana del mese. 

## <a name="how-can-i-use-azure-resource-manager-vnets-with-cloud-services"></a>Come è possibile usare le reti virtuali di Azure Resource Manager con i servizi cloud? 

I servizi cloud non possono stare nelle reti virtuali di Azure Resource Manager, ma una rete virtuale di Azure Resource Manager e una rete virtuale classica possono essere connesse tramite il peering. Per altre informazioni, vedere [Peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md).