---
title: Panoramica dei record di alias DNS di Azure
description: Panoramica del supporto per i record di alias DNS di Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957573"
---
# <a name="azure-dns-alias-records-overview"></a>Panoramica dei record di alias DNS di Azure

I record di alias DNS di Azure sono qualifiche su un set di record DNS che consente di fare riferimento ad altre risorse di Azure presenti nella propria zona DNS. Ad esempio, è possibile creare un recordset di alias che fa riferimento a un indirizzo IP pubblico di Azure invece che a un record A. Poiché il recordset di alias rimanda a un'istanza del servizio Indirizzo IP pubblico di Azure in modo dinamico, il set di record di alias si aggiorna automaticamente durante la risoluzione DNS.

Un set di record di alias è supportato per i seguenti tipi di record in una zona DNS di Azure: A, AAAA e CNAME. 

> [!NOTE]
> I record di alias per i tipi di record A o AAAA per la Gestione traffico sono supportati solo per i tipi di Endpoint esterni. È necessario fornire l'indirizzo IPv4 o IPv6 (indirizzi IP statici in una situazione ideale) appropriato per gli endpoint esterni in Gestione traffico.

## <a name="capabilities"></a>Capabilities

- **Rimandare a una risorsa IP pubblica da un set di record A o AAAA di DNS**. È possibile creare un set di record A/AAAA e renderlo un set di record alias impostato per rimandare a una risorsa IP pubblica.
- **Rimandare a un profilo di Gestione traffico da un set di record DNS A/AAAA/CNAME**. Oltre a poter rimandare al CNAME del profilo di Gestione traffico (ad esempio: contoso.trafficmanager.net) da un recordset DNS CNAME, è ora possibile anche rimandare a un profilo di Gestione traffico con endpoint esterni da recordset A o AAAA nella propria zona DNS.
   > [!NOTE]
   > I record di alias per i tipi di record A o AAAA per la Gestione traffico sono supportati solo per i tipi di Endpoint esterni. È necessario fornire l'indirizzo IPv4 o IPv6 (indirizzi IP statici in una situazione ideale) appropriato per gli endpoint esterni in Gestione traffico.
- **Rimandare a un altro set di record DNS all'interno della stessa zona**. I record di alias possono fare riferimento ad altri set di record dello stesso tipo. Ad esempio, è possibile avere un recordset DNS CNAME come alias a un altro recordset CNAME dello stesso tipo. Ciò è utile se si desidera avere alcuni recordset come alias e altri come non-alias in termini di comportamento.

## <a name="scenarios"></a>Scenari
Esistono alcuni scenari comuni per i record di Alias:

### <a name="prevent-dangling-dns-records"></a>Evitare record DNS inesatti
All'interno delle zone DNS di Azure, i record di alias possono essere usati per tenere traccia del ciclo di vita delle risorse di Azure, ad esempio un profilo IP pubblico e di Gestione traffico. Uno dei problemi comuni con i record DNS tradizionali è "record inesatti", in particolare con i tipi di record A/AAAA o CNAME. 

Con un record di zona DNS tradizionali, se l'IP o il CNAME di destinazione non esiste più, il record di zona DNS non è a conoscenza di questo aspetto e deve essere aggiornato manualmente. In alcune organizzazioni, questo aggiornamento manuale potrebbe non avvenire in tempo o essere problematico a causa della separazione dei ruoli e dei livelli di autorizzazione associati.

Ad esempio, il ruolo che dispone delle autorizzazioni per eliminare un record CNAME o l'indirizzo IP appartenente a un'applicazione potrebbe non avere le autorizzazioni sufficienti per aggiornare il record DNS che rimanda a tali destinazioni. Di conseguenza, potrebbe esserci un ritardo tra l'eliminazione dell'IP o del CNAME e la rimozione del record DNS che rimanda a esso e questo potrebbe causare un'interruzione del servizio per gli utenti finali.

I record di alias rimuovono la complessità associata a questo scenario e aiutano a evitare tali riferimenti inesatti. Quando un record DNS è qualificato come record alias in modo che rimandi a un indirizzo IP pubblico o a un profilo di Gestione traffico, e nel caso in cui vengano eliminate tali risorse sottostanti, anche il record dell'alias DNS viene rimosso nello stesso momento. In questo modo si garantisce che gli utenti finali non incorrano in interruzioni del servizio.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Aggiornare automaticamente le zone DNS quando cambiano gli indirizzi IP delle applicazioni

Analogamente allo scenario precedente, se un'applicazione viene spostata o se la macchina virtuale sottostante viene riavviata, un record alias viene aggiornato automaticamente quando l'indirizzo IP cambia per la risorsa IP pubblico sottostante. Se gli utenti finali vengono indirizzati a un'altra applicazione con l'indirizzo IP precedente, è possibile evitare i potenziali rischi di sicurezza.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hosting di applicazioni con carico bilanciato al vertice della zona

Il protocollo DNS impedisce l'assegnazione di un valore diverso da un record A o AAAA al vertice della zona (ad esempio: contoso.com). Questo rappresenta un problema per i proprietari delle applicazioni che dispongono di applicazioni con carico bilanciato dietro una Gestione traffico, in quanto non è stato possibile rimandare al profilo di Gestione traffico dal record vertice di zona. Di conseguenza, i proprietari delle applicazioni erano costretti a usare una soluzione alternativa. Ad esempio, un reindirizzamento al livello dell'applicazione per il reindirizzamento dal vertice di zona a un altro dominio (da contoso.com a www.contoso.com). Questa operazione presenta un singolo punto di guasto in termini di funzionalità di reindirizzamento.

Con i record di alias, questo problema non esiste più. I proprietari delle applicazioni possono ora indirizzare i record vertice della propria zona a un profilo di Gestione traffico con endpoint esterni. Con questa funzionalità, i proprietari delle applicazioni possono rimandare allo stesso profilo di Gestione traffico che viene usato per qualsiasi altro dominio all'interno della zona DNS. Ad esempio, www.contoso.com e contoso.com possono entrambi rimandare allo stesso profilo di Gestione traffico, purché il profilo di Gestione traffico abbia solo gli endpoint esterni configurati.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui record di alias, vedere gli articoli seguenti:

- [Esercitazione: Configurare un record alias per fare riferimento a un IP pubblico di Azure](tutorial-alias-pip.md)
- [Esercitazione: Configurare un record alias per supportare nomi di dominio vertice con Gestione traffico](tutorial-alias-tm.md)
- [Domande frequenti sui DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
