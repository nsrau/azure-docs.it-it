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
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 3fc0d34fdb617ebb1af9c9f33e018d5fe6ec9a7d
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017

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
1. Accedere alla console di gestione di Azure (https://manage.windowsazure.com), passare al servizio cloud e selezionare la scheda **Configura**.
2. Selezionare **Remoto**.
3. Modificare la data di scadenza e quindi salvare la configurazione.

A questo punto dovrebbe essere possibile usare RDP nel computer.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>Perché LoadBalancer non bilancia il carico del traffico in modo uniforme?
Per informazioni sul funzionamento del servizio di bilanciamento del carico interno, vedere [Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/) (Nuova modalità di distribuzione di Azure Load Balancer).

L'algoritmo di distribuzione usato è un hash a 5 tuple (IP di origine, porta di origine, IP di destinazione, porta di destinazione, tipo di protocollo) per eseguire il mapping del traffico ai server disponibili. La persistenza viene mantenuta solo all'interno di una sessione di trasporto. I pacchetti nella stessa sessione TCP o UDP verranno indirizzati alla stessa istanza di IP di data center (DIP) nell'endpoint con carico bilanciato. Quando il client chiude e riapre la connessione o avvia una nuova sessione dallo stesso IP di origine, la porta di origine cambia e il traffico quindi viene indirizzato a un endpoint DIP diverso.

