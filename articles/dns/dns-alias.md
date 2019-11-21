---
title: Alias records overview - Azure DNS
description: In this article, learn about support for alias records in Microsoft Azure DNS.
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
> Se si prevede di usare un record alias per i tipi di record A o AAAA per puntare a un [profilo di Gestione traffico di Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md) è necessario assicurarsi che il profilo di Gestione traffico disponga solo di [endpoint esterni](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). È necessario specificare gli indirizzi IPv4 o IPv6 per gli endpoint esterni in Gestione traffico. You can't use fully-qualified domain names (FQDNs) in endpoints. Idealmente, usare indirizzi IP statici.

## <a name="capabilities"></a>Capabilities

- **Puntare a una risorsa IP pubblica da un set di record DNS A/AAAA**. You can create an A/AAAA record set and make it an alias record set to point to a public IP resource (standard or basic). The DNS record set changes automatically if the public IP address changes or is deleted. Vengono evitati i record DNS inesatti che puntano a indirizzi IP non corretti.

   There is a current limit of 20 alias records sets per resource.

- **Puntare a un profilo di Gestione traffico da un set di record DNS A/AAAA/CNAME**. È possibile creare un set di record A/AAAA o CNAME e usare i record alias per associarlo a un profilo di Gestione traffico. It's especially useful when you need to route traffic at a zone apex, as traditional CNAME records aren't supported for a zone apex. Ad esempio, il profilo di Gestione traffico è myprofile.trafficmanager.net e la zona DNS aziendale è contoso.com. È possibile creare un set di record alias del tipo A/AAAA per contoso.com (vertice di zona) e scegliere myprofile.trafficmanager.net.
- **Point to an Azure Content Delivery Network (CDN) endpoint**. This is useful when you create static websites using Azure storage and Azure CDN.
- **Puntare a un altro set di record DNS all'interno della stessa zona**. I record alias possono fare riferimento ad altri set di record dello stesso tipo. Un set di record CNAME DNS ad esempio può essere un alias per un altro set di record CNAME. Questo approccio è utile se si vuole che solo alcuni set di record siano alias.

## <a name="scenarios"></a>Scenari

Esistono alcuni scenari comuni per i record di alias.

### <a name="prevent-dangling-dns-records"></a>Evitare record DNS inesatti

Un problema comune che si verifica con i record DNS tradizionali è la presenza di record inesatti. For example, DNS records that haven't been updated to reflect changes to IP addresses. Questi problemi si verificano soprattutto con i tipi di record A/AAAA o CNAME.

Con un record di zona DNS tradizionale, se l'IP o il CNAME di destinazione non esiste più, il record DNS associato deve essere aggiornato manualmente. In some organizations, a manual update might not happen in time because of process issues or the separation of roles and associated permission levels. Un ruolo può ad esempio disporre dell'autorizzazione per eliminare un CNAME o un indirizzo IP appartenente a un'applicazione, ma può non possedere le autorizzazioni sufficienti per aggiornare il record DNS che punta a tali destinazioni. Un ritardo nell'aggiornamento del record DNS potenzialmente può causare un'interruzione del servizio per gli utenti.

I record alias prevengono i riferimenti inesatti accoppiando il ciclo di vita di un record DNS a una risorsa di Azure. Si consideri, ad esempio, un record DNS che è qualificato come record alias per puntare a un indirizzo IP pubblico o a un profilo di Gestione traffico. If you delete those underlying resources, the DNS alias record becomes an empty record set. It no longer references the deleted resource.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Aggiornare automaticamente il set di record DNS quando cambiano gli indirizzi IP dell'applicazione

Questo scenario è simile a quello precedente. Un'applicazione viene forse spostata o viene riavviata la macchina virtuale sottostante. Un record alias si aggiorna quindi automaticamente quando cambia l'indirizzo IP per la risorsa IP pubblico sottostante. Ciò consente di evitare potenziali rischi di sicurezza nell'indirizzare gli utenti a un'altra applicazione a cui è stato assegnato l'indirizzo IP pubblico precedente.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Ospitare applicazioni con carico bilanciato nel dominio radice

Il protocollo DNS impedisce l'assegnazione di record CNAME al dominio radice. For example if your domain is contoso.com; you can create CNAME records for somelabel.contoso.com; but you can't create CNAME for contoso.com itself.
Questa restrizione presenta un problema per i proprietari delle applicazioni che dispongono di applicazioni con carico bilanciato dietro [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Since using a Traffic Manager profile requires creation of a CNAME record, it isn't possible to point at the Traffic Manager profile from the zone apex.

This problem is solved using alias records. Unlike CNAME records, alias records are created at the zone apex and application owners can use it to point their zone apex record to a Traffic Manager profile that has external endpoints. Application owners point to the same Traffic Manager profile that's used for any other domain within their DNS zone.

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. Per altre informazioni sull'uso di record alias con i profili di Gestione traffico di Azure, vedere la sezione Passaggi successivi.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Point zone apex to Azure CDN endpoints

Just like a Traffic Manager profile, you can also use alias records to point your DNS zone apex to Azure CDN endpoints. This is useful when you create static websites using Azure storage and Azure CDN. You can then access the website without prepending "www" to your DNS name.

For example, if your static website is named www.contoso.com, your users can access your site using contoso.com without the need to prepend www to the DNS name.

As described previously, CNAME records aren't supported at the zone apex. So, you can’t use a CNAME record to point contoso.com to your CDN endpoint. Instead, you can use an alias record to point the zone apex to a CDN endpoint directly.

> [!NOTE]
> Pointing a zone apex to CDN endpoints for Azure CDN from Akamai is currently not supported.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui record alias, vedere gli articoli seguenti:

- [Esercitazione: Configurare un record alias per fare riferimento a un indirizzo IP pubblico di Azure](tutorial-alias-pip.md)
- [Esercitazione: Configurare un record alias per supportare nomi di dominio vertice con Gestione traffico](tutorial-alias-tm.md)
- [Domande frequenti sui DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
