<properties 
   pageTitle="Panoramica del servizio DNS di Azure | Microsoft Azure"
	description="Panoramica dei servizi di hosting di DNS di Azure in Microsoft Azure e avvio dell'hosting del dominio in Microsoft Azure"
	services="dns"
	documentationCenter="na"
	authors="joaoma"
	manager="adinah"
	editor=""/>

<tags
   ms.service="dns"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/12/2015"
	ms.author="joaoma"/>

# Panoramica di DNS di Azure

Protetto da qualsiasi sito Web o servizio su Internet, esiste un indirizzo IP. Ad esempio, www.microsoft.com usa l'indirizzo IP 134.170.185.46. Il nome DNS (Domain Name System) è responsabile della conversione (o risoluzione) del nome del sito Web o del servizio nel relativo indirizzo IP.

DNS di Azure è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure.

I domini DNS nel servizio DNS di Azure sono ospitati nella rete globale di Azure dei server dei nomi DNS. Usiamo le reti Anycast, in modo che ogni query DNS riceva una risposta dal server DNS disponibile più vicino. Ciò consente di accelerare le prestazioni e ottenere la disponibilità elevata per il dominio.

Il servizio si basa su Gestione risorse di Azure. I domini e i record possono essere gestite con le API REST di Gestione risorse di Azure, .NET SDK, i cmdlet PowerShell e l'interfaccia della riga di comando. DNS di Azure è attualmente in anteprima e non è ancora supportato nel portale di gestione di Azure.<BR><BR> DNS di Azure attualmente non supporta l'acquisto dei nomi di dominio. Per acquistare domini è necessario usare un terzo registrar, che in genere addebiterà un prezzo irrisorio annuo. Tali domini possono quindi essere ospitati in DNS di Azure per la gestione dei record DNS. Per informazioni dettagliate, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).


## Passaggi successivi

[Introduzione alla creazione di zone DNS](dns-getstarted-create-dnszone.md)

[Automatizzare le operazioni di Azure con .NET SDK](dns-sdk.md)

[Informazioni di riferimento sulle API REST di DNS di Azure](https://msdn.microsoft.com/library/azure/mt163862.aspx)




 

<!---HONumber=August15_HO9-->