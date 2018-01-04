---
title: Replica geografica di un registro contenitori di Azure
description: Introduzione alla creazione e gestione dei registri contenitori di Azure con replica geografica
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: overview-article
ms.date: 10/24/2017
ms.author: stevelas
ms.openlocfilehash: 92df5a37d62dc9731842a4312339aa571072a487
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="geo-replication-in-azure-container-registry"></a>Replica geografica nel servizio Registro contenitori di Azure

Le aziende con esigenze di presenza online locale o di backup a caldo scelgono di eseguire i servizi da più aree di Azure. Come procedura consigliata, l'inserimento di un registro contenitori in ogni area in cui vengono eseguite le immagini consente l'esecuzione di operazioni in posizioni di rete vicine e quindi di trasferimenti di livelli di immagine più veloci e affidabili.

La replica geografica consente al registro contenitori di Azure di fungere da singolo registro in modo da servire più aree con registri regionali multimaster.

> [!IMPORTANT]
> La funzionalità di replica geografica del servizio Registro contenitori di Azure è attualmente in versione di **anteprima**. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

Un registro con replica geografica è caratterizzato dai vantaggi seguenti:

* I nomi di registro/immagine/tag singoli possono essere usati in più aree
* Accesso al registro in una posizione di rete vicina da distribuzioni internazionali
* Nessun costo aggiuntivo per il traffico in uscita perché viene eseguito il pull delle immagini da un registro replicato in locale nella stessa area dell'host del contenitore
* Gestione unica di registro in più aree

## <a name="example-use-case"></a>Esempio di caso d'uso
Contoso gestisce un sito Web per la presenza online pubblica esteso a Stati Uniti, Canada ed Europa. Per gestire questi mercati con contenuti in locale e in una posizione di rete vicina, Contoso esegue cluster Kubernetes del [servizio contenitore di Azure](/azure/container-service/kubernetes/) nelle aree Stati Uniti occidentali, Stati Uniti orientali, Canada centrale ed Europa occidentale. L'applicazione del sito Web, distribuita come un'immagine Docker, usa lo stesso codice e la stessa immagine in tutte le aree. Il contenuto locale per un'area specifica viene recuperato da un database, di cui viene eseguito il provisioning in modo univoco in ogni area. Ogni distribuzione regionale dispone della relativa configurazione univoca per risorse quali, ad esempio, il database locale.

Il team di sviluppo si trova a Seattle (Washington) e usa il data center degli Stati Uniti occidentali.

![Esecuzione del push in più registri](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Esecuzione del push in più registri*

Prima di usare le funzionalità di replica geografica, Contoso dispone di un registro situato negli Stati Uniti e di un registro aggiuntivo situato in Europa occidentale. Per gestire queste due aree, il team di sviluppo deve eseguire il push delle immagini da due registri diversi.

```bash
docker push contoso.azurecr.io/pubic/products/web:1.2
docker push contosowesteu.azurecr.io/pubic/products/web:1.2
```
![Esecuzione del pool da più registri](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Esecuzione del pool da più registri*

Le tipiche problematiche relative alla presenza di più registri sono descritte di seguito:

* I cluster Stati Uniti orientali, Stati Uniti occidentali e Canada centrale eseguono il pull dal registro nell'area Stati Uniti occidentali. Ciò comporta costi per il traffico in uscita in quanto ciascuno di questi host di contenitori remoti esegue il pull delle immagini dai data center presenti nell'area Stati Uniti occidentali.
* Il team di sviluppo deve eseguire il push delle immagini nei registri presenti nelle aree Stati Uniti occidentali ed Europa occidentale.
* Il team di sviluppo deve configurare e gestire ogni distribuzione regionale mediante nomi di immagini che fanno riferimento al registro in locale.
* L'accesso al registro deve essere configurato per ogni area.

## <a name="benefits-of-geo-replication"></a>Vantaggi della replica geografica

![Esecuzione del pull da un registro con replica geografica](media/container-registry-geo-replication/after-geo-replicate-pull.png)

L'uso della funzionalità di replica geografica del Registro contenitori di Azure è caratterizzato dai vantaggi descritti di seguito:

* Gestione di un unico registro per tutte le aree: `contoso.azurecr.io`
* Gestione di un'unica configurazione per le distribuzioni delle immagini in quanto tutte le aree usano lo stesso URL immagine: `contoso.azurecr.io/public/products/web:1.2`
* Esecuzione del push in un unico registro, mentre il servizio Registro contenitori di Azure gestisce la replica geografica, compresi gli webhook internazionali per le notifiche locali

## <a name="configure-geo-replication"></a>Configurare la replica geografica
La configurazione della replica geografica è un'operazione semplice basata sulla selezione delle aree mediante clic su una mappa.

La replica geografica è una funzionalità disponibile solo per i [registri Premium](container-registry-skus.md). Se la versione del registro non è Premium, è possibile passare dalla versione Basic e quella Standard e infine a quella Premium nel [portale di Azure](https://portal.azure.com):

![Cambio di SKU nel portale di Azure](media/container-registry-skus/update-registry-sku.png)

Per configurare la replica geografica per il registro Premium, accedere al portale di Azure all'indirizzo http://portal.azure.com.

Passare al Registro contenitori di Azure e selezionare **Repliche**:

![Repliche nell'interfaccia utente del registro contenitori del portale di Azure](media/container-registry-geo-replication/registry-services.png)

Viene visualizzata una mappa che mostra tutte le aree correnti di Azure:

 ![Mappa delle aree nel portale di Azure](media/container-registry-geo-replication/registry-geo-map.png)

* Gli esagoni blu rappresentano le repliche correnti
* Gli esagoni verdi rappresentano le possibili aree di replica
* Gli esagoni grigi rappresentano le aree di Azure non ancora disponibili per la replica

Per configurare una replica, selezionare un esagono verde e quindi fare clic su **Crea**:

 ![Interfaccia utente Crea replica nel portale di Azure](media/container-registry-geo-replication/create-replication.png)

Per configurare repliche aggiuntive, selezionare gli esagoni verdi per le altre aree e quindi fare clic su **Crea**.

Il servizio Registro contenitori di Azure inizia a sincronizzare le immagine tra le repliche configurate. Al termine dell'operazione, nel portale viene visualizzata la dicitura *Pronto*. Lo stato della replica nel portale non viene aggiornato automaticamente. Usare il pulsante Aggiorna per visualizzare lo stato aggiornato.

## <a name="geo-replication-pricing"></a>Prezzi della replica geografica

La replica geografica è una funzionalità dello [SKU Premium](container-registry-skus.md) del Registro contenitori di Azure. Quando viene eseguita la replica di un registro nelle aree desiderate, si devono sostenere i costi relativi a un registro Premium per ogni area.

Nell'esempio precedente, Contoso ha unificato due registri mediante il consolidamento e ha aggiunto repliche per le aree Stati Uniti orientali, Canada centrale ed Europa occidentale. Contoso dovrà pagare quattro tariffe Premium al mese, senza costi aggiuntivi per la configurazione e la gestione. Ogni area esegue ora il pull delle relative immagini in locale, migliorando in questo modo prestazioni e affidabilità senza alcun costo aggiuntivo per il traffico in uscita dagli Stati Uniti occidentali al Canada e agli Stati Uniti orientali.

## <a name="summary"></a>Summary

Grazie alla replica geografica è possibile gestire data center regionali come un cloud globale. Poiché le immagini vengono usate in molti servizi di Azure, è possibile avvalersi dei vantaggi di un unico piano di gestione durante l'uso di pull di immagini più rapidi e affidabili e in posizioni di rete più vicine.

## <a name="next-steps"></a>Passaggi successivi

Fare riferimento alla serie di esercitazioni in tre parti relativa alla [replica geografica nel Registro contenitori di Azure](container-registry-tutorial-prepare-registry.md). Eseguire la procedura dettagliata relativa alla creazione di un registro con replica geografica, alla compilazione di un contenitore e alla sua distribuzione mediante un unico comando `docker push` in più istanze regionali delle app Web per contenitori.

> [!div class="nextstepaction"]
> [Replica geografica nel servizio Registro contenitori di Azure](container-registry-tutorial-prepare-registry.md)