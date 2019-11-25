---
title: Procedure consigliate per il registro contenitori
description: Informazioni su come usare Registro Azure Container in modo efficace seguendo queste procedure consigliate.
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: 4b0512674358d4db2e29596408ebbf44af4ea2a9
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455326"
---
# <a name="best-practices-for-azure-container-registry"></a>Procedure consigliate per Registro Azure Container

Seguendo queste procedure consigliate, è possibile contribuire a sfruttare al massimo le prestazioni e la convenienza del Registro contenitori privato Docker in Azure.

## <a name="network-close-deployment"></a>Distribuzione in reti vicine

Creare il registro contenitori nella stessa area di Azure in cui vengono distribuiti i contenitori. La collocazione del Registro in un'area di rete vicina ai propri host contenitori può ridurre la latenza e i costi.

La distribuzione in reti vicine è una delle ragioni principali per cui si usa un registro contenitori privato. Le immagini di Docker hanno un efficiente [costrutto di livelli](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) che consente la distribuzione incrementale. Tuttavia, è necessario che nuovi nodi eseguano il pull di tutti i livelli richiesti per una data immagine. Questo `docker pull` iniziale si può rapidamente aggiungere a più gigabyte. Avere un registro contenitori privato vicino alla propria distribuzione riduce al minimo la latenza di rete.
In aggiunta, tutti i cloud pubblici, Azure incluso, implementano i corrispettivi per il traffico di rete in uscita. Il pull di immagini da un data center a un altro aggiunge corrispettivi di rete in uscita e latenza.

## <a name="geo-replicate-multi-region-deployments"></a>Eseguire la replica geografica della distribuzione in più aree

Usare la funzione [replica geografica](container-registry-geo-replication.md) di Registro Azure Container se si distribuiscono contenitori a più aree. Se si servono clienti globali da data center locali o il proprio team di sviluppo è in più posizioni, è possibile semplificare la gestione del registro e ridurre al minimo la latenza eseguendo la replica geografica del proprio registro. La replica geografica è disponibile solo con registri [Premium](container-registry-skus.md).

Per capire come usare la replica geografica, vedere il tutorial in tre parti [Preparare un'istanza di Registro Azure Container con replica geografica](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Spazi dei nomi dell'archivio

Con l'uso di spazi dei nomi dell'archivio, è possibile consentire la condivisione di un singolo registro in più gruppi all'interno dell'organizzazione. I registri possono essere condivisi tra le distribuzioni e i team. Registro Azure Container supporta spazi dei nomi annidati, abilitando l'isolamento in gruppo.

Si considerino ad esempio i tag delle immagini del contenitore seguenti: Le immagini che vengono usate a livello aziendale, ad esempio `aspnetcore`, vengono inserite nello spazio dei nomi radice, mentre le immagini del contenitore di proprietà dei gruppi di Produzione e di Marketing usano spazi dei nomi dedicati.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Gruppo di risorse dedicato

Poiché i registri contenitori sono risorse usate in più host contenitori, un registro deve trovarsi nel relativo gruppo di risorse.

Anche se è possibile provare con un tipo di host specifico, come le Istanze di Azure Container, probabilmente al termine si vorrà eliminare l'istanza del contenitore. Tuttavia, è anche possibile mantenere la raccolta di immagini inserita in Registro Azure Container. Posizionando il registro nel suo gruppo di risorse, si riduce al minimo il rischio di eliminare accidentalmente la raccolta di immagini nel registro quando si elimina il gruppo di risorse delle istanze del contenitore.

## <a name="authentication"></a>Authentication

Quando si esegue l'autenticazione con Registro Azure Container, esistono due scenari principali: autenticazione singola e autenticazione di servizio (o "headless"). La tabella seguente fornisce una breve panoramica di questi scenari e il metodo di autenticazione consigliato per ognuno.

| Type | Scenario di esempio | Metodo consigliato |
|---|---|---|
| Identità singola | Uno sviluppatore che esegue il pull o il push di immagini dal computer di sviluppo. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Identità headless/del servizio | Pipeline di compilazione e distribuzione in cui l'utente non è direttamente coinvolto. | [Entità servizio](container-registry-authentication.md#service-principal) |

Per informazioni dettagliate sull'autenticazione a Registro Azure Container, vedere [Eseguire l'autenticazione con un registro contenitori Docker privato](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Gestire le dimensioni del registro

The storage constraints of each [container registry SKU][container-registry-skus] are intended to align with a typical scenario: **Basic** for getting started, **Standard** for the majority of production applications, and **Premium** for hyper-scale performance and [geo-replication][container-registry-geo-replication]. Per tutta la durata del registro, è necessario gestirne le dimensioni eliminando periodicamente il contenuto non usato.

Use the Azure CLI command [az acr show-usage][az-acr-show-usage] to display the current size of your registry:

```console
$ az acr show-usage --resource-group myResourceGroup --name myregistry --output table
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

È anche possibile trovare la risorsa di archiviazione corrente usata nella sezione **Panoramica** del registro nel portale di Azure:

![Informazioni sull'uso del registro nel portale di Azure][registry-overview-quotas]

### <a name="delete-image-data"></a>Eliminare i dati di immagini

Registro Azure Container supporta diversi metodi per l'eliminazione di dati di immagini dal registro contenitori. È possibile eliminare le immagini in base al tag o al digest del manifesto oppure eliminare un intero repository.

Per informazioni dettagliate sull'eliminazione dei dati di immagini dal registro, tra cui immagini senza tag (a volte indicate come "tralasciate" o "orfane"), vedere [Eliminare le immagini del contenitore in Registro Azure Container](container-registry-delete.md).

## <a name="next-steps"></a>Passaggi successivi

Registro Azure Container è disponibile in più livelli, chiamati SKU, ognuno dei quali fornisce funzionalità diverse. Per informazioni dettagliate sui livelli SKU disponibili, vedere [SKU di Registro Azure Container](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
