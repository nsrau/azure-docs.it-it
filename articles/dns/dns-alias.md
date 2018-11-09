---
title: Panoramica dei record di alias DNS di Azure
description: Panoramica del supporto per i record di alias DNS di Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092843"
---
# <a name="azure-dns-alias-records-overview"></a>Panoramica dei record di alias DNS di Azure

I record di alias DNS di Azure sono qualifiche su un set di record DNS. Possono fare riferimento ad altre risorse di Azure dall'interno della zona DNS. È ad esempio possibile creare un set di record alias che fa riferimento a un indirizzo IP pubblico di Azure invece che a un record A. Il set di record alias rimanda a un'istanza del servizio Indirizzo IP pubblico di Azure in modo dinamico. Il set di record alias si aggiorna pertanto automaticamente durante la risoluzione DNS.

Un set di record alias è supportato per i tipi di record seguenti in una zona DNS di Azure: 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> I record di alias per i tipi di record A/AAAA per Gestione traffico di Microsoft Azure sono supportati solo per i tipi di endpoint esterni. È necessario specificare l'indirizzo IPv4 o IPv6 appropriato per gli endpoint esterni in Gestione traffico. In teoria, si usano gli indirizzi IP statici per l'indirizzo.

## <a name="capabilities"></a>Capabilities

- **Puntare a una risorsa IP pubblica da un set di record DNS A/AAAA**. È possibile creare un set di record A/AAAA e renderlo un set di record alias impostato per puntare a una risorsa IP pubblica.

- **Puntare a un profilo di Gestione traffico da un set di record DNS A/AAAA/CNAME**. È possibile puntare al CNAME di un profilo di Gestione traffico da un set di record CNAME DNS, ad esempio contoso.trafficmanager.net. A questo punto è anche possibile puntare a un profilo di Gestione traffico con endpoint esterni da un set di record A/AAAA nella zona DNS in uso.

   > [!NOTE]
   > I record di alias per i tipi di record A/AAAA per Gestione traffico sono supportati solo per i tipi di endpoint esterni. È necessario specificare l'indirizzo IPv4 o IPv6 appropriato per gli endpoint esterni in Gestione traffico. In teoria, si usano gli indirizzi IP statici per l'indirizzo.
   
- **Puntare a un altro set di record DNS all'interno della stessa zona**. I record alias possono fare riferimento ad altri set di record dello stesso tipo. Un set di record CNAME DNS ad esempio può essere un alias per un altro set di record CNAME dello stesso tipo. Questo approccio è utile se si vuole che solo alcuni set di record siano alias.

## <a name="scenarios"></a>Scenari
Esistono alcuni scenari comuni per i record di alias.

### <a name="prevent-dangling-dns-records"></a>Evitare record DNS inesatti
 All'interno delle zone DNS di Azure i record alias possono essere usati per tenere traccia del ciclo di vita delle risorse di Azure. Le risorse includono un indirizzo IP pubblico o un profilo di Gestione traffico. Un problema comune che si verifica con i record DNS tradizionali è la presenza di record inesatti. Questo problema si verifica soprattutto con i tipi di record A/AAAA o CNAME. 

Con un record di zona DNS tradizionale, se l'IP o il CNAME di destinazione non esiste più, il record della zona DNS non lo sa. Il record deve di conseguenza essere aggiornato manualmente. In alcune organizzazioni l'aggiornamento manuale potrebbe non avvenire in tempo. Potrebbe inoltre risultare problematico a causa della separazione dei ruoli e dei livelli di autorizzazione associati.

Un ruolo può ad esempio disporre dell'autorizzazione per eliminare un CNAME o un indirizzo IP appartenente a un'applicazione, ma può non possedere le autorizzazioni sufficienti per aggiornare il record DNS che punta a tali destinazioni. Si verifica un ritardo temporale tra quando l'IP o il CNAME viene eliminato e quando il record DNS che punta ad esso viene rimosso. Questo ritardo temporale causa potenzialmente un'interruzione del servizio per gli utenti.

I record di alias rimuovono la complessità associata a questo scenario. Aiutano a evitare riferimenti inesatti. Si consideri ad esempio un record DNS che è qualificato come record alias per puntare a un indirizzo IP pubblico o a un profilo di Gestione traffico. Se vengono eliminate le risorse sottostanti, il record alias DNS viene rimosso nello stesso momento. Questo processo assicura che gli utenti non sperimentino mai un'interruzione del servizio.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Aggiornare automaticamente le zone DNS quando cambiano gli indirizzi IP delle applicazioni

Questo scenario è simile a quello precedente. Un'applicazione viene forse spostata o viene riavviata la macchina virtuale sottostante. Un record alias si aggiorna quindi automaticamente quando cambia l'indirizzo IP per la risorsa IP pubblico sottostante. Per evitare potenziali rischi di sicurezza, indirizzare gli utenti verso un'altra applicazione che usa l'indirizzo IP precedente.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Ospitare applicazioni con carico bilanciato nel dominio radice

Il protocollo DNS impedisce l'assegnazione di qualsiasi elemento eccetto un record A/AAAA nel dominio radice, ad esempio contoso.com. Questa restrizione presenta un problema per i proprietari delle applicazioni che dispongono di applicazioni con carico bilanciato dietro Gestione traffico. Non è possibile puntare al profilo di Gestione traffico dal record di dominio radice. I proprietari delle applicazioni devono quindi usare una soluzione alternativa. Un reindirizzamento al livello applicazione deve reindirizzare dal dominio radice a un altro dominio. Un esempio è il reindirizzamento da contoso.com a www.contoso.com. Questo approccio presenta un singolo punto di guasto in termini di funzionalità di reindirizzamento.

Con i record di alias, questo problema non esiste più. I proprietari delle applicazioni possono ora indirizzare i record del dominio radice a un profilo di Gestione traffico con endpoint esterni. I proprietari delle applicazioni possono puntare allo stesso profilo di Gestione traffico che viene usato per qualsiasi altro dominio all'interno della zona DNS. Gli indirizzi www.contoso.com e contoso.com ad esempio possono puntare allo stesso profilo di Gestione traffico. Ciò avviene fintanto che nel profilo di Gestione traffico sono configurati solo endpoint esterni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui record alias, vedere gli articoli seguenti:

- [Esercitazione: Configurare un record alias per fare riferimento a un indirizzo IP pubblico di Azure](tutorial-alias-pip.md)
- [Esercitazione: Configurare un record alias per supportare nomi di dominio vertice con Gestione traffico](tutorial-alias-tm.md)
- [Domande frequenti sui DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
