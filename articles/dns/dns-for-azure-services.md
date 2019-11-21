---
title: Use Azure DNS with other Azure services
description: In this learning path, get started on how to use Azure DNS to resolve names for other Azure services
services: dns
documentationcenter: na
author: asudbring
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: allensu
ms.openlocfilehash: 142157273bd24912311383785d08177abfd04398
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211890"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Funzionamento del servizio DNS di Azure con altri servizi di Azure

Il servizio DNS di Azure è un servizio ospitato di risoluzione nomi e di gestione DNS, utile per creare i nomi DNS pubblici per altri servizi e applicazioni distribuiti in Azure. La creazione di un nome per un servizio di Azure in un dominio personalizzato è semplice. È sufficiente aggiungere un record del tipo corretto per il servizio.

* Per indirizzi IP allocati in modo dinamico, è possibile creare un record CNAME che esegue il mapping al nome DNS che Azure ha creato per il servizio. Gli standard DNS impediscono l'uso di un record CNAME per il vertice della zona. In alternativa, è possibile usare un record di alias. Per altre informazioni, consultare [Esercitazione: Configurare un record alias per fare riferimento a un IP pubblico di Azure](tutorial-alias-pip.md).
* Per gli indirizzi IP statici, è possibile creare un record DNS A usando un nome qualsiasi, incluso un nome di *dominio naked* al vertice della zona.

Nella tabella seguente vengono illustrati i tipi di record supportati che è possibile usare per i vari servizi di Azure. Come si può vedere nella tabella, il servizio DNS di Azure supporta soltanto i record DNS per le risorse di rete con connessione Internet. Il servizio DNS di Azure non può essere usato per la risoluzione nomi degli indirizzi interni e privati.

| Servizio di Azure | interfaccia di rete | Description |
| --- | --- | --- |
| Gateway applicazione di Azure |[IP pubblico front-end](dns-custom-domain.md#public-ip-address) |È possibile creare un record DNS A o CNAME. |
| Servizio di bilanciamento del carico di Azure |[IP pubblico front-end](dns-custom-domain.md#public-ip-address) |È possibile creare un record DNS A o CNAME. Al servizio di bilanciamento del carico può essere assegnato dinamicamente un indirizzo IP pubblico IPv6. Creare un record CNAME per un indirizzo IPv6. |
| Gestione traffico di Azure |Nome pubblico |È possibile creare un record alias che esegua il mapping al nome trafficmanager.net assegnato al profilo di Gestione traffico. Per altre informazioni, consultare [Esercitazione: Configurare un record alias per supportare nomi di dominio vertice con Gestione traffico](tutorial-alias-tm.md). |
| Servizi cloud di Azure |[IP pubblico](dns-custom-domain.md#public-ip-address) |Per gli indirizzi IP allocati staticamente, è possibile creare un record DNS A. Per gli indirizzi IP allocati dinamicamente, è necessario creare un record CNAME che si associa al nome *cloudapp.net* .|
| Servizio app di Azure | [IP esterno](dns-custom-domain.md#app-service-web-apps) |Per gli indirizzi IP esterni, è possibile creare un record DNS A. In caso contrario, è necessario creare un record CNAME che viene associato al nome di azurewebsites.net. Per maggiori informazioni, vedere [Eseguire il mapping di un nome di dominio personalizzato in un'app Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| Macchine virtuali di Azure Resource Manager |[IP pubblico](dns-custom-domain.md#public-ip-address) |Le macchine virtuali di Resource Manager possono avere indirizzi IP pubblici. Una macchina virtuale con un indirizzo IP pubblico può essere anche dietro a un servizio di bilanciamento del carico. È possibile creare un record DNS A, CNAME o alias per l'indirizzo pubblico. Il nome personalizzato può essere usato per aggirare l'indirizzo VIP sul bilanciamento del carico. |
| Macchine virtuali classiche |[IP pubblico](dns-custom-domain.md#public-ip-address) |Le macchine virtuali classiche create con PowerShell o l'interfaccia della riga di comando possono essere configurate con un indirizzo virtuale (riservato) dinamico o statico. È possibile creare rispettivamente un record DNS A o CNAME. |
