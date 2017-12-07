---
title: Procedure consigliate in Registro contenitori di Azure
description: Informazioni su come usare il Registro contenitori di Azure in modo efficace seguendo queste procedure consigliate.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/05/2017
ms.author: marsma
ms.openlocfilehash: 5ccbb3022dc38f13eed9b5aa24beb14dfdb3b5b6
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Procedure consigliate per il Registro contenitori di Azure

Seguendo queste procedure consigliate, è possibile contribuire a sfruttare al massimo le prestazioni e la convenienza del Registro contenitori privato Docker in Azure.

## <a name="network-close-deployment"></a>Distribuzione in reti vicine

Creare il Registro contenitori nella stessa area di Azure in cui vengono distribuiti i contenitori. La collocazione del Registro in un'area di rete vicina ai propri host contenitori può ridurre la latenza e i costi.

La distribuzione in reti vicine è una delle ragioni principali per cui si usa un registro contenitori privato. Le immagini di Docker hanno un eccellente [costrutto di livelli](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) che consente la distribuzione incrementale. Tuttavia, è necessario che nuovi nodi eseguano il pull di tutti i livelli richiesti per una data immagine. Questo `docker pull` iniziale si può rapidamente aggiungere a più gigabyte. Avere un registro contenitori privato vicino alla propria distribuzione riduce al minimo la latenza di rete.
In aggiunta, tutti i cloud pubblici, Azure incluso, implementano i corrispettivi per il traffico di rete in uscita. Il pull di immagini da un data center a un altro aggiunge corrispettivi di rete in uscita e latenza.

## <a name="geo-replicate-multi-region-deployments"></a>Eseguire la replica geografica della distribuzione in più aree

Usare la funzione [replica geografica](container-registry-geo-replication.md) del registro contenitori di Azure se si distribuiscono contenitori a più aree. Se si servono clienti globali da data center locali o il proprio team di sviluppo è in più posizioni, è possibile semplificare la gestione del registro e ridurre al minimo la latenza eseguendo la replica geografica del proprio registro. Attualmente in anteprima, questa funzione è disponibile con registri [Premium](container-registry-skus.md#premium).

Per capire come usare la replica geografica, vedere il tutorial in tre parti [Preparare un registro contenitori di Azure con replica geografica](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Spazi dei nomi dell'archivio

Con l'uso di spazi dei nomi dell'archivio, è possibile consentire la condivisione di un singolo registro in più gruppi all'interno dell'organizzazione. I registri possono essere condivisi tra le distribuzioni e i team. Il Registro contenitori di Azure supporta spazi dei nomi annidati, abilitando l'isolamento in gruppo.

Si considerino ad esempio i tag delle immagini del contenitore seguenti: Le immagini che vengono usate a livello aziendale, ad esempio `aspnetcore`, vengono inserite nello spazio dei nomi radice, mentre le immagini del contenitore di proprietà dei gruppi di Produzione e di Marketing usano spazi dei nomi dedicati.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Gruppo di risorse dedicato

Poiché i registri contenitori sono risorse usate in più host contenitori, un registro contenitori deve trovarsi nel proprio gruppo di risorse.

Anche se è possibile provare con un tipo di host specifico, come le istanze di contenitori di Azure, probabilmente al termine si vorrà eliminare l'istanza del contenitore. Tuttavia, è anche possibile mantenere la raccolta di immagini inserita nel registro contenitori di Azure. Posizionando il registro nel suo gruppo di risorse, si riduce al minimo il rischio di eliminare accidentalmente la raccolta di immagini nel registro quando si elimina il gruppo di risorse delle istanze del contenitore.

## <a name="authentication"></a>Autenticazione

Quando si esegue l'autenticazione con un registro contenitori di Azure, esistono due scenari principali: autenticazione singola e autenticazione di servizio (o "headless"). La tabella seguente fornisce una breve panoramica di questi scenari e il metodo di autenticazione consigliato per ognuno.

| Tipo | Scenario di esempio | Metodo consigliato |
|---|---|---|
| Identità singola | Uno sviluppatore che esegue il pull o il push di immagini dal computer di sviluppo. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Identità headless/del servizio | Pipeline di compilazione e distribuzione in cui l'utente non è direttamente coinvolto. | [Entità servizio](container-registry-authentication.md#service-principal) |

Per informazioni dettagliate sull'autenticazione al Registro contenitori di Azure, vedere [Eseguire l'autenticazione con un registro contenitori Docker privato](container-registry-authentication.md).

## <a name="next-steps"></a>Passaggi successivi

Il Registro contenitori di Azure è disponibile in più livelli, chiamati SKU, ognuno dei quali fornisce funzionalità diverse. Per informazioni dettagliate sui livelli SKU disponibili, vedere [SKU del Registro contenitori di Azure](container-registry-skus.md).