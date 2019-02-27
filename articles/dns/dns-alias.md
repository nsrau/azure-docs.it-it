---
title: Panoramica dei record di alias DNS di Azure
description: Panoramica del supporto per i record di alias DNS di Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/20/2019
ms.author: victorh
ms.openlocfilehash: d751d4898be3fd19f9e6f5d03e9313e9d98e9dd2
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446095"
---
# <a name="azure-dns-alias-records-overview"></a>Panoramica dei record di alias DNS di Azure

I record di alias DNS di Azure sono qualifiche su un set di record DNS. Possono fare riferimento ad altre risorse di Azure dall'interno della zona DNS. È ad esempio possibile creare un set di record alias che fa riferimento a un indirizzo IP pubblico di Azure invece che a un record A. Il set di record alias rimanda a un'istanza del servizio Indirizzo IP pubblico di Azure in modo dinamico. Il set di record alias si aggiorna pertanto automaticamente durante la risoluzione DNS.

Un set di record alias è supportato per i tipi di record seguenti in una zona DNS di Azure: 

- Una  
- AAAA 
- CNAME 

> [!NOTE]
> Se si prevede di usare un record alias per i tipi di record A o AAAA per puntare a un [profilo di Gestione traffico di Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md) è necessario assicurarsi che il profilo di Gestione traffico disponga solo di [endpoint esterni](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). È necessario specificare gli indirizzi IPv4 o IPv6 per gli endpoint esterni in Gestione traffico. Idealmente, usare indirizzi IP statici.

## <a name="capabilities"></a>Capabilities

- **Puntare a una risorsa IP pubblica da un set di record DNS A/AAAA**. È possibile creare un set di record A/AAAA e renderlo un set di record alias impostato per puntare a una risorsa IP pubblica. Se l'indirizzo IP pubblico viene modificato o viene eliminato, il set di record DNS è automatico. Vengono evitati i record DNS inesatti che puntano a indirizzi IP non corretti.

- **Puntare a un profilo di Gestione traffico da un set di record DNS A/AAAA/CNAME**. È possibile creare un set di record A/AAAA o CNAME e usare i record alias per associarlo a un profilo di Gestione traffico. È particolarmente utile quando è necessario instradare il traffico in un dominio radice, poiché i record CNAME tradizionali non sono supportati per un dominio radice. Ad esempio, il profilo di Gestione traffico è myprofile.trafficmanager.net e la zona DNS aziendale è contoso.com. È possibile creare un set di record alias del tipo A/AAAA per contoso.com (vertice di zona) e scegliere myprofile.trafficmanager.net.

- **Puntare a un altro set di record DNS all'interno della stessa zona**. I record alias possono fare riferimento ad altri set di record dello stesso tipo. Un set di record CNAME DNS ad esempio può essere un alias per un altro set di record CNAME. Questo approccio è utile se si vuole che solo alcuni set di record siano alias.

## <a name="scenarios"></a>Scenari

Esistono alcuni scenari comuni per i record di alias.

### <a name="prevent-dangling-dns-records"></a>Evitare record DNS inesatti

Un problema comune che si verifica con i record DNS tradizionali è la presenza di record inesatti. Ad esempio, i record DNS che non sono stati aggiornati per riflettere le modifiche agli indirizzi IP. Questi problemi si verificano soprattutto con i tipi di record A/AAAA o CNAME.

Con un record di zona DNS tradizionale, se l'IP o il CNAME di destinazione non esiste più, il record DNS associato deve essere aggiornato manualmente. In alcune organizzazioni, un aggiornamento manuale potrebbe non avvenire in tempo a causa di problemi di processo o a causa della separazione dei ruoli e dei livelli di autorizzazione associati. Un ruolo può ad esempio disporre dell'autorizzazione per eliminare un CNAME o un indirizzo IP appartenente a un'applicazione, ma può non possedere le autorizzazioni sufficienti per aggiornare il record DNS che punta a tali destinazioni. Un ritardo nell'aggiornamento del record DNS potenzialmente può causare un'interruzione del servizio per gli utenti.

I record alias prevengono i riferimenti inesatti accoppiando il ciclo di vita di un record DNS a una risorsa di Azure. Si consideri, ad esempio, un record DNS che è qualificato come record alias per puntare a un indirizzo IP pubblico o a un profilo di Gestione traffico. Se vengono eliminate le risorse sottostanti, il record alias DNS viene rimosso nello stesso momento.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Aggiornare automaticamente il set di record DNS quando cambiano gli indirizzi IP dell'applicazione

Questo scenario è simile a quello precedente. Un'applicazione viene forse spostata o viene riavviata la macchina virtuale sottostante. Un record alias si aggiorna quindi automaticamente quando cambia l'indirizzo IP per la risorsa IP pubblico sottostante. Ciò consente di evitare potenziali rischi di sicurezza nell'indirizzare gli utenti a un'altra applicazione a cui è stato assegnato l'indirizzo IP pubblico precedente.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Ospitare applicazioni con carico bilanciato nel dominio radice

Il protocollo DNS impedisce l'assegnazione di record CNAME al dominio radice. Se ad esempio il dominio è contoso.com; è possibile creare record CNAME per somelable.contoso.com; ma non è possibile creare record CNAME per contoso.com.
Questa restrizione presenta un problema per i proprietari delle applicazioni che dispongono di applicazioni con carico bilanciato dietro [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Poiché l'uso di un profilo di Gestione traffico richiede la creazione di un record CNAME, non è possibile fare in modo che punti al profilo di Gestione traffico dal dominio radice.

Questo problema può essere risolto tramite i record alias. A differenza dei record CNAME, i record alias possono essere creati al dominio radice e i proprietari delle applicazioni possono usarlo in modo da puntare i record di dominio radice a un profilo di Gestione traffico con endpoint esterni. I proprietari delle applicazioni possono puntare allo stesso profilo di Gestione traffico che viene usato per qualsiasi altro dominio all'interno della zona DNS.

Gli indirizzi www.contoso.com e contoso.com ad esempio possono puntare allo stesso profilo di Gestione traffico. Per altre informazioni sull'uso di record alias con i profili di Gestione traffico di Azure, vedere la sezione Passaggi successivi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui record alias, vedere gli articoli seguenti:

- [Esercitazione: Configurare un record alias per fare riferimento a un indirizzo IP pubblico di Azure](tutorial-alias-pip.md)
- [Esercitazione: Configurare un record alias per supportare nomi di dominio radice con Gestione traffico](tutorial-alias-tm.md)
- [Domande frequenti sui DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
