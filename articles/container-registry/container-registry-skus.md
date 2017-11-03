---
title: SKU del Registro contenitori di Azure
description: Confronto tra i diversi livelli di servizio disponibili nel Registro contenitori di Azure
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: 630bc088fcb6d3c7e5bb3a9713107c3fb6653ec6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2017
---
# <a name="azure-container-registry-skus"></a>SKU del Registro contenitori di Azure

Il Registro contenitori di Azure è disponibile su più livelli di servizio, noti come SKU. Gli SKU offrono prezzi prevedibili e diverse opzioni per la modalità d'uso del registro docker privato in Azure. La scelta di uno SKU di livello superiore offre maggiori prestazioni e una migliore scalabilità. Tuttavia, tutti gli SKU offrono le stesse funzionalità programmatiche, consentendo a uno sviluppatore di iniziare da un livello base e di passare al livello standard e premium man mano che aumenta l'uso del registro.

## <a name="basic"></a>Basic
Un punto di ingresso di ottimizzazione dei costi per gli sviluppatori che iniziano a usare Registro contenitori di Azure. I registri di base dispongono delle stesse funzionalità programmatiche del livello standard e premium, ovvero l'integrazione dell'autenticazione, l'eliminazione di immagini e gli hook Web di Azure Active Directory, tuttavia ci sono limiti di dimensioni e uso.

## <a name="standard"></a>Standard
I registri standard offrono le stesse funzionalità del livello base, con limiti di archiviazione e velocità effettiva di immagine aumentati. I registri standard devono soddisfare le esigenze della maggior parte degli scenari di produzione.

## <a name="premium"></a>Premium
I registri premium offrono limiti più elevati sui vincoli quali l'archiviazione e le operazioni simultanee, l'abilitazione di scenari con volumi elevati. Oltre alle maggiore velocità effettiva per le immagini, il livello premium aggiunge funzionalità come la [replica geografica](container-registry-geo-replication.md) per la gestione di un solo registro in più aree, mantenendo un registro in una posizione di rete vicina a ogni distribuzione.

## <a name="classic"></a>Classico
Lo SKU del registro classico ha abilitato la versione iniziale del servizio Registro contenitori di Azure in Azure. I registri classici sono supportati da un account di archiviazione creato da Azure nella sottoscrizione, che limita la capacità di Registro contenitori di Azure di offrire funzionalità di livello superiore, ad esempio velocità effettiva e replica geografica aumentate. A causa delle capacità limitate, si pensa in futuro di rimuovere lo SKU classico.

> [!NOTE]
> Poiché lo SKU del registro classico verrà eliminato, è consigliabile usare i livelli di base, standard o premium per tutti i nuovi registri. Per informazioni sulla conversione del registro classico esistente, vedere [Cambiare SKU](#changing-skus).
>

## <a name="registry-sku-feature-matrix"></a>Matrice di funzionalità SKU del registro

La tabella seguente illustra le funzionalità e i limiti dei livelli di servizio Base, Standard e Premium.

| Funzionalità | Basic | Standard | Premium |
|---|---|---|---|---|
| Archiviazione | 10 GiB | 100 GiB| 500 GiB |
| Operazioni di lettura al minuto<sup>1, 2</sup> | 1.000 | 300.000 | 10.000.000 |
| Operazioni di scrittura al minuto<sup>1, 3</sup> | 100 | 500 | 2.000 |
| MBps di larghezza di banda per il download<sup>1</sup> | 30 | 60 | 100 |
| MBps di larghezza di banda per l'upload<sup>1</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| Replica geografica | N/D | N/D | [Supportata *(anteprima)*](container-registry-geo-replication.md) |

<sup>1</sup> I dati delle *operazioni di lettura*, *operazioni di scrittura* e *larghezza di banda* sono stime minime. Registro contenitori di Azure migliora le prestazioni in base alle necessità d'uso.

<sup>2</sup> Il [Pull Docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) si traduce in operazioni di lettura multiple in base al numero di livelli dell'immagine e al recupero del manifesto.

<sup>3</sup> Il [push di Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) si traduce in operazioni di scrittura multiple, in base al numero di livelli da inserire. Un `docker push` include *operazioni di lettura* per recuperare il manifesto di un'immagine esistente.

## <a name="manage-registry-size"></a>Gestire le dimensioni del registro
I vincoli di archiviazione di ogni SKU hanno lo scopo di allinearsi con uno scenario tipico: di base per iniziare, standard per la maggior parte delle app di produzione e premium per le prestazioni con iperscalabilità e la [replica geografica](container-registry-geo-replication.md). Per tutta la durata del registro, è necessario gestirne le dimensioni eliminando periodicamente il contenuto non usato.

I dati sull'uso attuale del registro sono contenuti nel registro contenitore **Panoramica** nel portale di Azure:

![Informazioni sull'uso del registro nel portale di Azure](media/container-registry-skus/registry-overview-quotas.png)

Eliminando i repository nel portale di Azure, è possibile gestire le dimensioni del registro.

In **SERVIZI** selezionare **Repository**, quindi fare clic con il pulsante destro del mouse sul repository che si desidera eliminare e selezionare **Elimina**.

![Eliminare un repository nel portale di Azure](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>Cambiare SKU

È possibile cambiare lo SKU del registro nel portale di Azure.

Nella **Panoramica** del registro nel portale di Azure selezionare **Aggiorna**, quindi scegliere un nuovo **SKU** dall'elenco SKU a discesa.

![Aggiornare lo SKU del registro contenitori nel portale di Azure](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>Cambiare il registro classico
Quando si passa da un registro classico a uno di base, standard o premium, Azure copia le immagini del contenitore esistente dall'account di archiviazione associato nella sottoscrizione in un account di archiviazione gestito da Azure. Questo processo potrebbe richiedere un po' di tempo.

Durante la conversione, `docker pull` continua a funzionare, ma `docker push` viene bloccato fino al completamento della conversione.

Dopo aver completato l'operazione, l'account di archiviazione della sottoscrizione non viene più usato dal Registro contenitori di Azure.

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>Perché passare dal registro classico a quello di base, standard o premium?

A causa delle funzionalità limitate dei registri classici, è consigliabile passare dai registri classici ai livelli Base, Standard o Premium. Questi SKU di livello superiore integrano in modo più specifico il registro nelle funzionalità di Azure. Alcune di queste funzionalità includono:

* l'integrazione di Azure Active Directory per la singola autenticazione (vedere [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login))
* il supporto per l'eliminazione di immagini e tag
* [Replica geografica](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

Ma soprattutto, un registro classico dipende dall'account di archiviazione per cui Azure esegue il provisioning automatico nella sottoscrizione di Azure al momento della creazione del registro. Gli SKU di base, standard e premium invece sfruttano l'*archivio gestito*. Questo vuol dire che Azure gestisce in modo trasparente l'archiviazione delle immagini per l'utente: nella propria sottoscrizione non viene creato un account di archiviazione diverso.

Alcuni dei vantaggi dell'archiviazione gestita offerti dai registri di base, standard e premium:

* Le immagini di contenitori sono [crittografati a riposo](../storage/common/storage-service-encryption.md).
* Le immagini vengono archiviate usando l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage), garantendo un backup delle immagini con la replica su più aree.
* Possibilità di [cambiare SKU](#changing-skus), consentendo una maggiore velocità effettiva, quando si sceglie uno SKU di livello superiore. Con ogni SKU, il Registro contenitori di Azure può soddisfare i requisiti di velocità effettiva man mano che aumentano le esigenze. L'implementazione sottostante delle modalità in cui il Registro contenitori di Azure raggiunge la velocità effettiva desiderata viene espresso come *finalità*, selezionando SKU superiori, senza dover gestire i dettagli dell'implementazione.

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi di ogni SKU del Registro contenitori di Azure, vedere [Prezzi di Registro di sistema del contenitore](https://azure.microsoft.com/pricing/details/container-registry/).

## <a name="next-steps"></a>Passaggi successivi

**Guida del Registro contenitori di Azure**

Visitare la [Guida del Registro contenitori di Azure](https://aka.ms/acr/roadmap) su GitHub per maggiori informazioni sulle funzionalità future del servizio.

**UserVoice del Registro contenitori di Azure**

Inviare e votare i nuovi suggerimenti sulle funzionalità in [UserVoice del Registro contenitori di Azure](https://feedback.azure.com/forums/903958-azure-container-registry).