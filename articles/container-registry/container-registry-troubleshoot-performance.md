---
title: Risolvere i problemi relativi alle prestazioni del registro
description: Sintomi, cause e risoluzione dei problemi comuni relativi alle prestazioni di un registro
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 414e3a92b8ebd4ff58528fc5e9ec4794471bd775
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227478"
---
# <a name="troubleshoot-registry-performance"></a>Risolvere i problemi relativi alle prestazioni del registro

Questo articolo consente di risolvere i problemi che possono verificarsi con le prestazioni di un registro contenitori di Azure. 

## <a name="symptoms"></a>Sintomi

Può includere uno o più degli elementi seguenti:

* Le immagini pull o push con l'interfaccia della riga di comando di Docker richiedono più tempo del previsto
* La distribuzione delle immagini in un servizio, ad esempio il servizio Azure Kubernetes, richiede più tempo del previsto
* Non è possibile completare un numero elevato di operazioni pull o push simultanee nel tempo previsto
* Le operazioni pull o push in un registro con replica geografica hanno più tempo del previsto oppure il push ha esito negativo con errore `Error writing blob` o `Error writing manifest`

## <a name="causes"></a>Cause

* La velocità di connessione di rete potrebbe rallentare le operazioni del registro di sistema- [soluzione](#check-expected-network-speed)
* La compressione o l'estrazione del livello immagine può essere lenta nella [soluzione](#check-client-hardware) client  
* Si raggiunge un limite configurato nel livello di servizio o nella [soluzione](#review-configured-limits) dell'ambiente del registro di sistema
* Il registro di sistema con replica geografica contiene repliche nelle aree vicine- [soluzione](#configure-geo-replicated-registry)
* Si sta effettuando il pull da una [soluzione](#configure-dns-for-geo-replicated-registry) di replica del registro di sistema geograficamente distanti

Se non si risolve il problema, vedere la [sezione relativa alla risoluzione dei problemi avanzata](#advanced-troubleshooting) e i [passaggi successivi](#next-steps) per altre opzioni.

## <a name="potential-solutions"></a>Possibili soluzioni

### <a name="check-expected-network-speed"></a>Controllare la velocità di rete prevista

Controllare la velocità di caricamento e download su Internet oppure usare uno strumento come AzureSpeed per testare il [caricamento](https://www.azurespeed.com/Azure/Uploadß) e il [download](https://www.azurespeed.com/Azure/Download) dall'archiviazione BLOB di Azure, che ospita i livelli di immagine del registro di sistema.

Controllare le dimensioni dell'immagine in base alle dimensioni massime supportate e al download supportato o caricare la larghezza di banda per il livello di servizio del registro. Se il registro di sistema si trova nel livello Basic o standard, provare a eseguire l'aggiornamento per migliorare le prestazioni. 

Per la distribuzione di immagini in altri servizi, controllare le aree in cui si trovano il registro di sistema e la destinazione. Per migliorare le prestazioni, provare a individuare il registro di sistema e la destinazione di distribuzione nelle stesse aree o in una rete chiusa.

Collegamenti correlati:

* [Livelli di servizio di Registro Azure Container](container-registry-skus.md)    
* [Domande frequenti sul registro contenitori](container-registry-faq.md)
* [Obiettivi di prestazioni e scalabilità per l'archiviazione BLOB di Azure](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>Controllare l'hardware client

Il tipo di disco e la CPU nel client Docker possono influenzare la velocità di estrazione o compressione dei livelli di immagine nel client come parte delle operazioni pull o push. L'estrazione dei livelli su un'unità disco rigido, ad esempio, importerà più tempo rispetto a un disco a stato solido. Confrontare le operazioni pull per immagini comparabili dal registro contenitori di Azure e da un registro pubblico come l'hub docker.

### <a name="review-configured-limits"></a>Verificare i limiti configurati

Se si esegue contemporaneamente il push o il pull di più immagini a più livelli nel registro di sistema, rivedere i limiti di operazioni lettura e operazioni scrittura supportati per il livello di servizio del registro di sistema. Se il registro di sistema si trova nel livello Basic o standard, provare a eseguire l'aggiornamento per aumentare i limiti. Verificare anche con il provider di rete la limitazione della rete che può verificarsi con molte operazioni simultanee. 

Esaminare la configurazione del daemon Docker per il numero massimo di caricamenti simultanei o download per ogni operazione push o pull nel client. Se necessario, configurare limiti più elevati.

Poiché ogni livello immagine richiede un'operazione di lettura o scrittura del registro di sistema separata, controllare il numero di livelli nelle immagini. Prendere in considerazione strategie per ridurre il numero di livelli di immagine.

Collegamenti correlati:

* [Livelli di servizio di Registro Azure Container](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Configurare il registro con replica geografica

Un client Docker che esegue il push di un'immagine in un registro con replica geografica potrebbe non eseguire il push di tutti i livelli di immagine e del relativo manifesto in un'unica area replicata. Questa situazione può verificarsi perché Gestione traffico di Azure instrada le richieste del registro di sistema al registro di sistema replicato più vicino. Se il registro ha due aree di replica nelle vicinanze, i livelli dell'immagine e il manifesto potrebbero essere distribuiti nei due siti e l'operazione push avrà esito negativo quando viene convalidato il manifesto.

Per ottimizzare la risoluzione DNS nella replica più vicina quando si esegue il push di immagini, configurare un registro con replica geografica nelle stesse aree di Azure dell'origine delle operazioni push oppure nell'area più vicina se si lavora all'esterno di Azure.

Per risolvere i problemi relativi alle operazioni con un registro con replica geografica, è anche possibile disabilitare temporaneamente il routing di gestione traffico a una o più repliche.

Collegamenti correlati:

* [Replica geografica nel servizio Registro Azure Container](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>Configurare il DNS per il registro con replica geografica

Se le operazioni pull da un registro con replica geografica risultano lente, la configurazione DNS sul client potrebbe risolversi in un server DNS geograficamente distante. In questo caso, gestione traffico potrebbe indirizzare le richieste a una replica che è vicina alla rete rispetto al server DNS, ma distante dal client. Eseguire uno strumento come `nslookup` o `dig` (in Linux) per determinare la replica a cui gestione traffico indirizza le richieste del registro di sistema. Ad esempio:

```console
nslookup myregistry.azurecr.io
```

Una possibile soluzione consiste nel configurare un server DNS più vicino.

Collegamenti correlati:

* [Replica geografica nel servizio Registro Azure Container](container-registry-geo-replication.md)
* [Risolvere i problemi relativi alle operazioni push con registri con replica geografica](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Disabilitare temporaneamente il routing alla replica](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Domande frequenti su Gestione traffico](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Risoluzione dei problemi avanzata

Se le autorizzazioni per le risorse del registro di sistema consentono, [controllare l'integrità dell'ambiente del registro di sistema](container-registry-check-health.md). Se vengono segnalati errori, esaminare le informazioni di [riferimento sull'errore](container-registry-health-error-reference.md) per le potenziali soluzioni.

Se nel registro di sistema è abilitata la [raccolta di log delle risorse](container-registry-diagnostics-audit-logs.md) , esaminare il log ContainterRegistryRepositoryEvents. Questo log archivia informazioni per operazioni quali gli eventi push o pull. Eseguire una query sul log per individuare gli [errori delle operazioni a livello di repository](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures). 

Collegamenti correlati:

* [Log per la valutazione diagnostica e il controllo](container-registry-diagnostics-audit-logs.md)
* [Domande frequenti sul registro contenitori](container-registry-faq.md)
* [Procedure consigliate per Registro Azure Container](container-registry-best-practices.md)

## <a name="next-steps"></a>Passaggi successivi

Se non si risolve il problema, vedere le opzioni seguenti.

* Altri argomenti sulla risoluzione dei problemi del registro di sistema includono:
  * [Risolvere i problemi di accesso al registro](container-registry-troubleshoot-login.md)
  * [Risolvere i problemi di rete con il registro di sistema](container-registry-troubleshoot-access.md)
* Opzioni di [supporto della community](https://azure.microsoft.com/support/community/)
* [Domane e risposte Microsoft](https://docs.microsoft.com/answers/products/)
* [Aprire un ticket di supporto](https://azure.microsoft.com/support/create-ticket/)


