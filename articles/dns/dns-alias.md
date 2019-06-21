---
title: Panoramica dei record di alias DNS di Azure
description: Panoramica del supporto per i record di alias DNS di Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/7/2019
ms.author: victorh
ms.openlocfilehash: 5dfc00b1193117c22ba1c763bb0e75d9c4712222
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275749"
---
# <a name="azure-dns-alias-records-overview"></a>Panoramica dei record di alias DNS di Azure

I record di alias DNS di Azure sono qualifiche su un set di record DNS. Possono fare riferimento ad altre risorse di Azure dall'interno della zona DNS. È ad esempio possibile creare un set di record alias che fa riferimento a un indirizzo IP pubblico di Azure invece che a un record A. Il set di record alias rimanda a un'istanza del servizio Indirizzo IP pubblico di Azure in modo dinamico. Il set di record alias si aggiorna pertanto automaticamente durante la risoluzione DNS.

Un set di record alias è supportato per i tipi di record seguenti in una zona DNS di Azure: 

- Una
- AAAA
- CNAME

> [!NOTE]
> Se si prevede di usare un record alias per i tipi di record A o AAAA per puntare a un [profilo di Gestione traffico di Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md) è necessario assicurarsi che il profilo di Gestione traffico disponga solo di [endpoint esterni](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). È necessario specificare gli indirizzi IPv4 o IPv6 per gli endpoint esterni in Gestione traffico. È possibile usare nomi di dominio completo (FQDN) negli endpoint. Idealmente, usare indirizzi IP statici.

## <a name="capabilities"></a>Capabilities

- **Puntare a una risorsa IP pubblica da un set di record DNS A/AAAA**. È possibile creare un set di record A/AAAA e renderlo un set di record alias impostato per puntare a una risorsa IP pubblica. Il record DNS le modifiche al set automaticamente se l'indirizzo IP pubblico viene modificato o viene eliminato. Vengono evitati i record DNS inesatti che puntano a indirizzi IP non corretti.

- **Puntare a un profilo di Gestione traffico da un set di record DNS A/AAAA/CNAME**. È possibile creare un set di record A/AAAA o CNAME e usare i record alias per associarlo a un profilo di Gestione traffico. È particolarmente utile quando è necessario instradare il traffico a un vertice della zona, come i record CNAME tradizionali non sono supportati per un vertice della zona. Ad esempio, il profilo di Gestione traffico è myprofile.trafficmanager.net e la zona DNS aziendale è contoso.com. È possibile creare un set di record alias del tipo A/AAAA per contoso.com (vertice di zona) e scegliere myprofile.trafficmanager.net.
- **Scegliere un endpoint Content Delivery Network (rete CDN di Azure)** . Ciò è utile quando si creano siti Web statici con archiviazione di Azure e rete CDN di Azure.
- **Puntare a un altro set di record DNS all'interno della stessa zona**. I record alias possono fare riferimento ad altri set di record dello stesso tipo. Un set di record CNAME DNS ad esempio può essere un alias per un altro set di record CNAME. Questo approccio è utile se si vuole che solo alcuni set di record siano alias.

## <a name="scenarios"></a>Scenari

Esistono alcuni scenari comuni per i record di alias.

### <a name="prevent-dangling-dns-records"></a>Evitare record DNS inesatti

Un problema comune che si verifica con i record DNS tradizionali è la presenza di record inesatti. Record DNS, ad esempio, che non sono stati aggiornati per riflettere le modifiche agli indirizzi IP. Questi problemi si verificano soprattutto con i tipi di record A/AAAA o CNAME.

Con un record di zona DNS tradizionale, se l'IP o il CNAME di destinazione non esiste più, il record DNS associato deve essere aggiornato manualmente. In alcune organizzazioni, un aggiornamento manuale potrebbe non avvenire in tempo a causa di problemi di processo o la separazione dei ruoli e livelli di autorizzazione associato. Un ruolo può ad esempio disporre dell'autorizzazione per eliminare un CNAME o un indirizzo IP appartenente a un'applicazione, ma può non possedere le autorizzazioni sufficienti per aggiornare il record DNS che punta a tali destinazioni. Un ritardo nell'aggiornamento del record DNS potenzialmente può causare un'interruzione del servizio per gli utenti.

I record alias prevengono i riferimenti inesatti accoppiando il ciclo di vita di un record DNS a una risorsa di Azure. Si consideri, ad esempio, un record DNS che è qualificato come record alias per puntare a un indirizzo IP pubblico o a un profilo di Gestione traffico. Se si eliminano le risorse sottostanti, il record dell'alias DNS diventa un set di record vuoto. Non è più fa riferimento la risorsa eliminata.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Aggiornare automaticamente il set di record DNS quando cambiano gli indirizzi IP dell'applicazione

Questo scenario è simile a quello precedente. Un'applicazione viene forse spostata o viene riavviata la macchina virtuale sottostante. Un record alias si aggiorna quindi automaticamente quando cambia l'indirizzo IP per la risorsa IP pubblico sottostante. Ciò consente di evitare potenziali rischi di sicurezza nell'indirizzare gli utenti a un'altra applicazione a cui è stato assegnato l'indirizzo IP pubblico precedente.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Ospitare applicazioni con carico bilanciato nel dominio radice

Il protocollo DNS impedisce l'assegnazione di record CNAME al dominio radice. Se, ad esempio il dominio è contoso.com; è possibile creare record CNAME per somelabel.contoso.com; ma non è possibile creare record CNAME per contoso.com.
Questa restrizione presenta un problema per i proprietari delle applicazioni che dispongono di applicazioni con carico bilanciato dietro [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Poiché usa un profilo di Traffic Manager richiede la creazione di un record CNAME, non è possibile in modo che punti al profilo di Traffic Manager di vertice della zona.

Questo problema viene risolto utilizzando record di alias. A differenza dei record CNAME, record di alias vengono creati al vertice della zona e i proprietari delle applicazioni possono utilizzarlo in modo da puntare i record vertice della zona a un profilo di gestione traffico con endpoint esterni. I proprietari delle applicazioni scegliere lo stesso profilo di Traffic Manager che viene usato per qualsiasi altro dominio all'interno della zona DNS.

Ad esempio, contoso.com e www\.contoso.com può puntare allo stesso profilo di Traffic Manager. Per altre informazioni sull'uso di record alias con i profili di Gestione traffico di Azure, vedere la sezione Passaggi successivi.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Vertice della zona di punto di endpoint rete CDN di Azure

Proprio come un profilo di Traffic Manager, è possibile usare anche i record di alias in modo da puntare il vertice della zona DNS agli endpoint rete CDN di Azure. Ciò è utile quando si creano siti Web statici con archiviazione di Azure e rete CDN di Azure. È quindi possibile accedere al sito Web senza anteporre "www" per il nome DNS.

Ad esempio, se il sito Web statico è denominato www.contoso.com, gli utenti possono accedere al sito usando contoso.com senza che sia necessario anteporre al nome DNS di www.

Come descritto in precedenza, non sono supportati i record CNAME al vertice della zona. Pertanto, non è possibile utilizzare un record CNAME in modo che punti a contoso.com all'endpoint rete CDN. In alternativa, è possibile usare un record alias in modo da puntare direttamente il vertice della zona a un endpoint della rete CDN.

> [!NOTE]
> Che punta un vertice della zona agli endpoint rete CDN per la rete CDN di Azure fornita da Akamai non è attualmente supportata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui record alias, vedere gli articoli seguenti:

- [Esercitazione: Configurare un record alias per fare riferimento a un indirizzo IP pubblico di Azure](tutorial-alias-pip.md)
- [Esercitazione: Configurare un record alias per supportare nomi di dominio radice con Gestione traffico](tutorial-alias-tm.md)
- [Domande frequenti sui DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
