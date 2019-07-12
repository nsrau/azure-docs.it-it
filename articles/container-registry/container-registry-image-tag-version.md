---
title: Tag e versione immagini nel registro contenitori di Azure
description: Procedure consigliate per l'assegnazione di tag e controllo delle versioni di Docker immagini del contenitore
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: steve.lasker
ms.openlocfilehash: bd00fd4f8dd247c766eb34849ecf9de603c5171b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800393"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Consigli per l'assegnazione di tag e controllo delle versioni delle immagini del contenitore

Quando il push di distribuzione delle immagini del contenitore in un registro contenitori e quindi distribuirli, necessaria una strategia per l'assegnazione di tag di immagine e il controllo delle versioni. Questo articolo descrive due approcci e in cui ogni adatta durante il ciclo di vita del contenitore:

* **I tag di stabilità** -i tag che si riutilizza, ad esempio, per indicare un'importante o la versione secondaria, ad esempio *mycontainerimage:1.0*.
* **Tag univoci** -un tag diverso per ogni immagine che si esegue il push in un registro, ad esempio *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Tag stabili

**Consiglio**: Usare i tag stabili per mantenere **le immagini di base** per le compilazioni del contenitore. Evitare le distribuzioni con tag stabile, perché questi tag continuino a ricevere gli aggiornamenti e possono introdurre incoerenze negli ambienti di produzione.

*I tag di stabilità* significa che uno sviluppatore o un sistema di compilazione, è possibile continuare a eseguire il pull un tag specifico, che continua a ottenere gli aggiornamenti. Stabile non significa che sono bloccati i contenuti. Piuttosto, stabile implica che l'immagine deve essere stabile per lo scopo di tale versione. Per rimanere "stabile", potrebbe essere soddisfatte per applicare le patch di sicurezza o gli aggiornamenti di framework.

### <a name="example"></a>Esempio

Un team framework è disponibile la versione 1.0. Sanno che sono verranno inclusi gli aggiornamenti, inclusi gli aggiornamenti secondari. Per supportare i tag stabili per una determinata versione principale e secondaria, hanno due set di tag stabile.

* `:1` -un tag stabile per la versione principale. `1` rappresenta la versione 1. * "più recente" o "latest".
* `:1.0`-un tag stabile per la versione 1.0, che consente agli sviluppatori di eseguire l'associazione agli aggiornamenti pari a 1,0 e non eseguire il rollforward a 1.1 quando viene rilasciato.

Il team Usa anche il `:latest` tag, che fa riferimento al tag di stabile più recente, indipendentemente da quale versione principale corrente.

Quando sono disponibili aggiornamenti di immagine di base, o qualsiasi tipo di versione del framework, le immagini con i tag stabile di manutenzione vengono aggiornati per il digest della più recente che rappresenta la versione stabile più recente di quella versione.

In questo caso, entrambi i tag principali e secondari vengono continuamente serviti. Da uno scenario di immagine di base, in questo modo il proprietario di immagine per fornire immagini servite.

## <a name="unique-tags"></a>Tag univoci

**Consiglio**: Usare i tag univoci per **distribuzioni**, soprattutto in un ambiente che possono essere scalate in più nodi. Si può essere opportuno distribuzioni intenzionale di una versione coerente dei componenti. Se il contenitore viene riavviato o un agente di orchestrazione viene scalata orizzontalmente più istanze, gli host non pull accidentalmente una versione più recente, coerente con gli altri nodi.

L'assegnazione di tag univoco indica semplicemente che ogni immagine inserita in un registro contiene un tag univoco. I tag non vengono riutilizzati. Sono disponibili diversi modelli, che è possibile seguire per generare tag univoco, ad esempio:

* **Indicatore data e ora** -questo approccio è abbastanza comune, poiché è possibile indicare chiaramente quando l'immagine è stata creata. Ma, come per metterli al sistema di compilazione? È necessario cercare la compilazione è stata completata nello stesso momento? Quale fuso orario ci sarai? Tutti i sistemi di compilazione vengono calibrati in ora UTC?
* **Commit GIT** – questo approccio funziona fino all'avvio che supportano gli aggiornamenti di immagine di base. Se si verifica un aggiornamento dell'immagine di base, il sistema di compilazione viene avviato con lo stesso commit Git della compilazione precedente. Tuttavia, l'immagine di base presenta nuovi contenuti. In generale, un'operazione di commit Git fornisce una *semi*-stabile di tag.
* **Digest manifesto** -ogni immagine del contenitore inserita in un registro contenitori è associato con un manifesto, identificato da un hash SHA-256 univoca o del digest. Mentre univoco, il digest è molto difficile la lettura e non correlate con l'ambiente di compilazione.
* **ID compilazione** -questa opzione potrebbe essere preferibile perché è probabile che incrementale, e consente di correlare nuovamente per la compilazione specifica per trovare tutti gli elementi e i log. Tuttavia, ad esempio un digest di manifesto, potrebbero essere difficile di lettura.

  Se l'organizzazione dispone di diversi sistemi di compilazione, facendolo precedere il tag con il nome di sistema di compilazione è una variante di questa opzione: `<build-system>-<build-id>`. Ad esempio, è impossibile distinguere le compilazioni dal sistema di compilazione del team API Jenkins e sistema di compilazione le pipeline del team web di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per una descrizione più dettagliata dei concetti in questo articolo, vedere il post di blog [l'assegnazione di tag Docker: Procedure consigliate per l'assegnazione di tag e controllo delle versioni delle immagini docker](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Per ottimizzare le prestazioni e la convenienza del registro contenitori di Azure, vedere [procedure consigliate per registro contenitori di Azure](container-registry-best-practices.md).

<!-- IMAGES -->


<!-- LINKS - Internal -->

