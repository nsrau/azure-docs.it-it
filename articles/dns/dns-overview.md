<properties 
   pageTitle="Panoramica del servizio DNS di Azure | Microsoft Azure" 
   description="Panoramica dei servizi di hosting DNS di Azure in Microsoft Azure. Ospitare il dominio in Microsoft Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Panoramica di DNS di Azure


Il nome DNS (Domain Name System) è responsabile della conversione (o risoluzione) del nome di un sito Web o del servizio nel relativo indirizzo IP. DNS di Azure è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.


I domini DNS nel servizio DNS di Azure sono ospitati nella rete globale di Azure dei server dei nomi DNS. Usiamo le reti Anycast, in modo che ogni query DNS riceva una risposta dal server DNS disponibile più vicino. Ciò consente di accelerare le prestazioni e ottenere la disponibilità elevata per il dominio.

Il servizio DNS di Azure si basa su Gestione risorse di Azure. Si avvale quindi dei vantaggi offerti dalle funzionalità di Azure Resource Manager, ad esempio il controllo degli accessi in base al ruolo, i log di controllo e il blocco delle risorse. I domini e i record possono essere gestiti tramite il portale di Azure, i cmdlet di Azure PowerShell e l'interfaccia della riga di comando di Azure. Le applicazioni che richiedono la gestione automatica di DNS possono essere integrate con il servizio tramite l'API REST e gli SDK.

DNS di Azure attualmente non supporta l'acquisto dei nomi di dominio. Se si desidera acquistare domini, è necessario usare un registrar di nomi di dominio di terze parti. Il registrar in genere addebiterà un prezzo annuo irrisorio. I domini possono quindi essere ospitati in DNS di Azure per la gestione dei record DNS. Per informazioni dettagliate, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).


## Passaggi successivi

[Creare una zona DNS](dns-getstarted-create-dnszone-portal.md)




 

<!---HONumber=AcomDC_0817_2016-->