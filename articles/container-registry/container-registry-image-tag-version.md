---
title: Procedure consigliate per i tag di immagine
description: Procedure consigliate per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore Docker durante il push di immagini e il pull di immagini da un registro contenitori di Azure
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75445747"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Suggerimenti per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore

Quando si esegue il push delle immagini del contenitore in un registro contenitori e quindi la si distribuisce, è necessaria una strategia per l'assegnazione di tag alle immagini e il controllo delle versioni. Questo articolo illustra due approcci e ognuno si integra durante il ciclo di vita del contenitore:

* **Tag stabili** -Tag riutilizzabili, ad esempio, per indicare una versione principale o secondaria, ad esempio *mycontainerimage: 1.0*.
* **Tag univoci** : un tag diverso per ogni immagine di cui si esegue il push in un registro, ad esempio *mycontainerimage: abc123*.

## <a name="stable-tags"></a>Tag stabili

**Raccomandazione**: usare i tag stabili per gestire le **Immagini di base** per le compilazioni di contenitori. Evitare le distribuzioni con tag stabili, perché tali tag continuano a ricevere aggiornamenti e possono introdurre incoerenze negli ambienti di produzione.

I *tag stabili* significano che uno sviluppatore o un sistema di compilazione può continuare a eseguire il pull di un tag specifico, che continua a ricevere gli aggiornamenti. Stabile non significa che il contenuto è bloccato. Piuttosto stabile implica che l'immagine deve essere stabile per lo scopo di tale versione. Per rimanere "stabile", potrebbe essere servito per applicare patch di sicurezza o aggiornamenti del Framework.

### <a name="example"></a>Esempio

Un team del Framework è dotato della versione 1,0. Sanno che saranno disponibili aggiornamenti, inclusi gli aggiornamenti secondari. Per supportare i tag stabili per una determinata versione principale e secondaria, sono disponibili due set di tag stabili.

* `:1`: un tag stabile per la versione principale. `1`rappresenta la versione "più recente" o "più recente" 1. *.
* `:1.0`-un tag stabile per la versione 1,0, che consente a uno sviluppatore di eseguire l'associazione agli aggiornamenti di 1,0 e non viene eseguito il rollforward a 1,1 quando viene rilasciato.

Il team USA anche il `:latest` tag, che punta al tag stabile più recente, indipendentemente dalla versione principale corrente.

Quando sono disponibili aggiornamenti di immagini di base o qualsiasi tipo di versione di manutenzione del Framework, le immagini con i tag stabili vengono aggiornate al digest più recente che rappresenta la versione stabile più recente di tale versione.

In questo caso, vengono continuamente serviti sia i tag principali che quelli secondari. Da uno scenario di immagine di base, ciò consente al proprietario dell'immagine di fornire immagini gestite.

### <a name="delete-untagged-manifests"></a>Elimina manifesti senza tag

Se viene aggiornata un'immagine con un tag stabile, l'immagine contrassegnata in precedenza non viene contrassegnata, ottenendo un'immagine orfana. Il manifesto dell'immagine precedente e i dati di livello univoco rimangono nel registro di sistema. Per mantenere le dimensioni del registro di sistema, è possibile eliminare periodicamente manifesti senza tag derivanti da aggiornamenti di immagini stabili. Ad esempio, [ripulire automaticamente](container-registry-auto-purge.md) i manifesti senza tag più vecchi di una durata specificata o impostare un [criterio di conservazione](container-registry-retention-policy.md) per i manifesti senza tag.

## <a name="unique-tags"></a>Tag univoci

**Raccomandazione**: usare tag univoci per le **distribuzioni**, soprattutto in un ambiente che può essere ridimensionato in più nodi. È probabile che si desiderino distribuzioni intenzionali di una versione coerente dei componenti. Se il contenitore viene riavviato o un agente di orchestrazione scala più istanze, gli host non eseguiranno accidentalmente il pull di una versione più recente, incoerente con gli altri nodi.

Il contrassegno univoco significa semplicemente che ogni immagine inserita in un registro ha un tag univoco. I tag non vengono riutilizzati. Sono disponibili diversi modelli che è possibile seguire per generare tag univoci, tra cui:

* **Indicatore di data e ora** : questo approccio è piuttosto comune, dal momento che è possibile stabilire chiaramente quando è stata compilata l'immagine. Tuttavia, come correlarlo di nuovo al sistema di compilazione? È necessario trovare la compilazione completata nello stesso momento? Quale fuso orario si trova? Tutti i sistemi di compilazione sono calibrati per l'ora UTC?
* **Commit Git** : questo approccio funziona fino a quando non si inizia a supportare gli aggiornamenti delle immagini di base. Se si verifica un aggiornamento di un'immagine di base, il sistema di compilazione avvia con lo stesso commit Git della compilazione precedente. Tuttavia, l'immagine di base dispone di nuovo contenuto. In generale, un commit Git fornisce un tag *semi*-stabile.
* **Digest del manifesto** : ogni immagine del contenitore di cui è stato eseguito il push in un registro contenitori è associata a un manifesto, identificato da un hash SHA-256 univoco o digest. Sebbene sia univoco, il digest è lungo, difficile da leggere e non correlato con l'ambiente di compilazione.
* **ID compilazione** : questa opzione può essere ottimale poiché è probabilmente incrementale e consente di eseguire la correlazione alla compilazione specifica per trovare tutti gli elementi e i log. Tuttavia, come un digest del manifesto, potrebbe essere difficile da leggere.

  Se l'organizzazione dispone di diversi sistemi di compilazione, il prefisso del tag con il nome del sistema di compilazione è una variante `<build-system>-<build-id>`di questa opzione:. È possibile, ad esempio, distinguere le compilazioni dal sistema di compilazione Jenkins del team API e dal sistema Azure Pipelines build del team Web.

### <a name="lock-deployed-image-tags"></a>Blocca tag immagine distribuiti

Come procedura consigliata, è consigliabile [bloccare](container-registry-image-lock.md) qualsiasi tag immagine distribuito, impostando il relativo `write-enabled` attributo su. `false` Questa procedura impedisce di rimuovere inavvertitamente un'immagine dal registro di sistema e probabilmente di danneggiare le distribuzioni. È possibile includere il passaggio di blocco nella pipeline di rilascio.

Il blocco di un'immagine distribuita consente comunque di rimuovere altre immagini non distribuite dal registro di sistema usando le funzionalità di Container Registry di Azure per gestire il registro di sistema. Ad esempio, [ripulire automaticamente](container-registry-auto-purge.md) i manifesti senza tag o le immagini sbloccate anteriori a una durata specificata oppure impostare un [criterio di conservazione](container-registry-retention-policy.md) per i manifesti senza tag.

## <a name="next-steps"></a>Passaggi successivi

Per una descrizione più dettagliata dei concetti di questo articolo, vedere il post di Blog relativo all' [assegnazione di tag a docker: procedure consigliate per l'assegnazione di tag e il controllo delle versioni delle immagini Docker](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Per ottimizzare le prestazioni e l'uso conveniente del registro contenitori di Azure, vedere [procedure consigliate per azure container Registry](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

