---
title: Cenni preliminari sui record alias-DNS di Azure
description: In questo articolo vengono fornite informazioni sul supporto per i record alias in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: allensu
ms.openlocfilehash: da396d3e3db4acd1a9843977b8b3e3d51c33f021
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212335"
---
# <a name="azure-dns-alias-records-overview"></a>Panoramica dei record di alias DNS di Azure

I record di alias DNS di Azure sono qualifiche su un set di record DNS. Possono fare riferimento ad altre risorse di Azure dall'interno della zona DNS. È ad esempio possibile creare un set di record alias che fa riferimento a un indirizzo IP pubblico di Azure invece che a un record A. Il set di record alias rimanda a un'istanza del servizio Indirizzo IP pubblico di Azure in modo dinamico. Il set di record alias si aggiorna pertanto automaticamente durante la risoluzione DNS.

Un set di record alias è supportato per i tipi di record seguenti in una zona DNS di Azure: 

- A
- AAAA
- CNAME

> [!NOTE]
> Se si prevede di usare un record alias per i tipi di record A o AAAA per puntare a un [profilo di Gestione traffico di Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md) è necessario assicurarsi che il profilo di Gestione traffico disponga solo di [endpoint esterni](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). È necessario specificare gli indirizzi IPv4 o IPv6 per gli endpoint esterni in Gestione traffico. Non è possibile usare nomi di dominio completi (FQDN) negli endpoint. Idealmente, usare indirizzi IP statici.

## <a name="capabilities"></a>Capabilities

- **Puntare a una risorsa IP pubblica da un set di record DNS A/AAAA**. È possibile creare un set di record A/AAAA e renderlo un set di record alias in modo che punti a una risorsa IP pubblica (standard o Basic). Il set di record DNS cambia automaticamente se l'indirizzo IP pubblico cambia o viene eliminato. Vengono evitati i record DNS inesatti che puntano a indirizzi IP non corretti.

   È previsto un limite corrente di 20 set di record alias per ogni risorsa.

- **Puntare a un profilo di Gestione traffico da un set di record DNS A/AAAA/CNAME**. È possibile creare un set di record A/AAAA o CNAME e usare i record alias per associarlo a un profilo di Gestione traffico. È particolarmente utile quando è necessario instradare il traffico a un vertice della zona, perché i record CNAME tradizionali non sono supportati per un vertice della zona. Ad esempio, il profilo di Gestione traffico è myprofile.trafficmanager.net e la zona DNS aziendale è contoso.com. È possibile creare un set di record alias del tipo A/AAAA per contoso.com (vertice di zona) e scegliere myprofile.trafficmanager.net.
- **Puntare a un endpoint della rete per la distribuzione di contenuti (CDN) di Azure**. Questa operazione è utile quando si creano siti web statici usando archiviazione di Azure e la rete CDN di Azure.
- **Puntare a un altro set di record DNS all'interno della stessa zona**. I record alias possono fare riferimento ad altri set di record dello stesso tipo. Un set di record CNAME DNS ad esempio può essere un alias per un altro set di record CNAME. Questo approccio è utile se si vuole che solo alcuni set di record siano alias.

## <a name="scenarios"></a>Scenari

Esistono alcuni scenari comuni per i record di alias.

### <a name="prevent-dangling-dns-records"></a>Evitare record DNS inesatti

Un problema comune che si verifica con i record DNS tradizionali è la presenza di record inesatti. Ad esempio, i record DNS che non sono stati aggiornati per riflettere le modifiche apportate agli indirizzi IP. Questi problemi si verificano soprattutto con i tipi di record A/AAAA o CNAME.

Con un record di zona DNS tradizionale, se l'IP o il CNAME di destinazione non esiste più, il record DNS associato deve essere aggiornato manualmente. In alcune organizzazioni, un aggiornamento manuale potrebbe non avvenire in tempo a causa di problemi di processo o la separazione dei ruoli e dei livelli di autorizzazione associati. Un ruolo può ad esempio disporre dell'autorizzazione per eliminare un CNAME o un indirizzo IP appartenente a un'applicazione, ma può non possedere le autorizzazioni sufficienti per aggiornare il record DNS che punta a tali destinazioni. Un ritardo nell'aggiornamento del record DNS potenzialmente può causare un'interruzione del servizio per gli utenti.

I record alias prevengono i riferimenti inesatti accoppiando il ciclo di vita di un record DNS a una risorsa di Azure. Si consideri, ad esempio, un record DNS che è qualificato come record alias per puntare a un indirizzo IP pubblico o a un profilo di Gestione traffico. Se si eliminano le risorse sottostanti, il record dell'alias DNS diventa un set di record vuoto. Non fa più riferimento alla risorsa eliminata.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Aggiornare automaticamente il set di record DNS quando cambiano gli indirizzi IP dell'applicazione

Questo scenario è simile a quello precedente. Un'applicazione viene forse spostata o viene riavviata la macchina virtuale sottostante. Un record alias si aggiorna quindi automaticamente quando cambia l'indirizzo IP per la risorsa IP pubblico sottostante. Ciò consente di evitare potenziali rischi di sicurezza nell'indirizzare gli utenti a un'altra applicazione a cui è stato assegnato l'indirizzo IP pubblico precedente.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Ospitare applicazioni con carico bilanciato nel dominio radice

Il protocollo DNS impedisce l'assegnazione di record CNAME al dominio radice. Se ad esempio il dominio è contoso.com; è possibile creare record CNAME per somelabel.contoso.com; Tuttavia, non è possibile creare CNAME per contoso.com.
Questa restrizione presenta un problema per i proprietari delle applicazioni che dispongono di applicazioni con carico bilanciato dietro [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Poiché l'uso di un profilo di gestione traffico richiede la creazione di un record CNAME, non è possibile puntare al profilo di Traffic Manager dal vertice della zona.

Questo problema viene risolto utilizzando i record alias. A differenza dei record CNAME, i record di alias vengono creati nel vertice della zona e i proprietari dell'applicazione possono usarli per puntare il record Apex della zona a un profilo di gestione traffico con endpoint esterni. I proprietari dell'applicazione puntano allo stesso profilo di gestione traffico usato per qualsiasi altro dominio nella zona DNS.

Ad esempio, contoso.com e www\.contoso.com possono puntare allo stesso profilo di gestione traffico. Per altre informazioni sull'uso di record alias con i profili di Gestione traffico di Azure, vedere la sezione Passaggi successivi.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Apice della zona punto per gli endpoint della rete CDN di Azure

Analogamente a un profilo di gestione traffico, è anche possibile usare record alias per puntare il vertice della zona DNS agli endpoint della rete CDN di Azure. Questa operazione è utile quando si creano siti web statici usando archiviazione di Azure e la rete CDN di Azure. È quindi possibile accedere al sito Web senza anteporre "www" al nome DNS.

Ad esempio, se il sito Web statico è denominato www.contoso.com, gli utenti possono accedere al sito usando contoso.com senza che sia necessario anteporre www al nome DNS.

Come descritto in precedenza, i record CNAME non sono supportati nel vertice della zona. Non è quindi possibile usare un record CNAME per puntare contoso.com all'endpoint della rete CDN. In alternativa, è possibile usare un record alias per puntare direttamente il vertice della zona a un endpoint della rete CDN.

> [!NOTE]
> Il puntamento di un vertice della zona agli endpoint della rete CDN per la rete CDN di Azure da Akamai non è attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui record alias, vedere gli articoli seguenti:

- [Esercitazione: Configurare un record alias per fare riferimento a un indirizzo IP pubblico di Azure](tutorial-alias-pip.md)
- [Esercitazione: Configurare un record alias per supportare nomi di dominio vertice con Gestione traffico](tutorial-alias-tm.md)
- [Domande frequenti sui DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
