---
title: Procedure consigliate per i tag immagine
description: Procedure consigliate per il tagging e il controllo delle versioni delle immagini del contenitore durante il push e il pull di immagini da un registro contenitori di AzureBest practices for tagging and versioning Docker container images when push images to and pulling images from an Azure container registry
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445747"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Consigli per l'assegnazione di tag e il controllo delle versioni alle immagini del contenitore

Quando si esegue il push della distribuzione di immagini del contenitore in un registro contenitori e quindi distribuirle, è necessaria una strategia per il tagging e il controllo delle versioni delle immagini. In questo articolo vengono illustrati due approcci e dove ognuno si adatta durante il ciclo di vita del contenitore:This article discusses two approaches and where each fits during the container lifecycle:

* **Tag stabili:** tag riutilizzati, ad esempio, per indicare una versione principale o secondaria, ad esempio *mycontainerimage:1.0*.
* **Tag univoci:** un tag diverso per ogni immagine inserita in un Registro di sistema, ad esempio *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Tag stabili

**Consiglio:** usare i tag stable per gestire **le immagini** di base per le compilazioni contenitore. Evitare le distribuzioni con tag stable, perché tali tag continuano a ricevere aggiornamenti e possono introdurre incoerenze negli ambienti di produzione.

*Tag stabili* significa che uno sviluppatore, o un sistema di compilazione, può continuare a tirare un tag specifico, che continua a ricevere gli aggiornamenti. Stabile non significa che il contenuto sia congelato. Piuttosto, stable implica che l'immagine dovrebbe essere stabile per l'intento di quella versione. Per rimanere "stabile", potrebbe essere necessario applicare patch di sicurezza o aggiornamenti del framework.

### <a name="example"></a>Esempio

Un team di framework viene fornita la versione 1.0.A framework team ships version 1.0. Sanno che spediranno gli aggiornamenti, inclusi gli aggiornamenti minori. Per supportare i tag stable per una determinata versione principale e secondaria, sono disponibili due set di tag stable.

* `:1`– un tag stable per la versione principale. `1`rappresenta la versione "più recente" o "più recente" 1.
* `:1.0`- un tag stable per la versione 1.0, che consente a uno sviluppatore di eseguire il binding agli aggiornamenti di 1.0 e non viene eseguito il rollforward a 1.1 quando viene rilasciato.

Il team utilizza `:latest` anche il tag, che punta all'ultimo tag stable, indipendentemente dalla versione principale corrente.

Quando sono disponibili aggiornamenti delle immagini di base o qualsiasi tipo di versione di manutenzione del framework, le immagini con i tag stable vengono aggiornate al digest più recente che rappresenta la versione stabile più recente di tale versione.

In questo caso, sia i tag principali che quelli secondari vengono continuamente serviti. Da uno scenario di immagine di base, ciò consente al proprietario dell'immagine di fornire immagini servite.

### <a name="delete-untagged-manifests"></a>Eliminare manifesti senza tag

Se un'immagine con un tag stable viene aggiornata, l'immagine con tag precedenti viene senza tag, generando un'immagine orfana. Il manifesto dell'immagine precedente e i dati univoci del livello rimangono nel Registro di sistema. Per mantenere le dimensioni del Registro di sistema, è possibile eliminare periodicamente i manifesti senza tag risultanti da aggiornamenti di immagini stabili. Ad esempio, [eliminare automaticamente](container-registry-auto-purge.md) i manifesti senza tag anteriori a una durata specificata o impostare un criterio di [conservazione](container-registry-retention-policy.md) per i manifesti senza tag.

## <a name="unique-tags"></a>Tag univoci

**Consiglio:** utilizzare tag univoci per **le distribuzioni,** in particolare in un ambiente in grado di essere scalato su più nodi. È probabile che si desideri nozioni intenzionali di una versione coerente dei componenti. Se il contenitore viene riavviato o un agente di orchestrazione scala più istanze, gli host non eseriranno accidentalmente una versione più recente, incoerente con gli altri nodi.

L'assegnazione di tag unica significa semplicemente che ogni immagine inserita in un registro ha un tag univoco. I tag non vengono riutilizzati. Esistono diversi modelli che è possibile seguire per generare tag univoci, tra cui:There are several patterns you can follow to generate unique tags, including:

* **Data-ora** - Questo approccio è abbastanza comune, poiché è possibile indicare chiaramente quando è stata creata l'immagine. Ma, come correlarlo al sistema di compilazione? È necessario trovare la compilazione che è stata completata contemporaneamente? In quale fuso orario ti trovi? Tutti i sistemi di compilazione sono calibrati in UTC?
* **Git commit:** questo approccio funziona fino a quando non si avvia il supporto degli aggiornamenti delle immagini di base. Se si verifica un aggiornamento dell'immagine di base, il sistema di compilazione viene avviato con lo stesso commit Git della compilazione precedente. Tuttavia, l'immagine di base ha nuovo contenuto. In generale, un commit *semi*Git fornisce un tag semi-stabile.
* **Digest del** manifesto: ogni immagine del contenitore inserita in un registro contenitori è associata a un manifesto, identificato da un hash SHA-256 univoco, o digest. Sebbene univoco, il digest è lungo, difficile da leggere e non correlato con l'ambiente di compilazione.
* **ID compilazione:** questa opzione può essere ottimale poiché è probabilmente incrementale e consente di correlare nuovamente alla compilazione specifica per trovare tutti gli elementi e i log. Tuttavia, come un digest manifesto, potrebbe essere difficile da leggere per un essere umano.

  Se l'organizzazione dispone di più sistemi di compilazione, il prefisso `<build-system>-<build-id>`del tag con il nome del sistema di compilazione è una variante di questa opzione: . Ad esempio, è possibile differenziare le compilazioni dal sistema di compilazione Jenkins del team API e dal sistema di compilazione delle pipeline di Azure del team Web.For example, you could differentiate builds from the API team's Jenkins build system and the web team's Azure Pipelines build system.

### <a name="lock-deployed-image-tags"></a>Bloccare i tag delle immagini distribuite

È consigliabile [bloccare](container-registry-image-lock.md) qualsiasi tag immagine distribuita impostandone `write-enabled` l'attributo su `false`. Questa procedura impedisce di rimuovere inavvertitamente un'immagine dal Registro di sistema ed eventualmente interrompere le distribuzioni. È possibile includere il passaggio di blocco nella pipeline di rilascio.

Il blocco di un'immagine distribuita consente comunque di rimuovere altre immagini non distribuite dal Registro di sistema usando le funzionalità del Registro di sistema del contenitore di Azure per gestire il Registro di sistema. Ad esempio, [eliminare automaticamente](container-registry-auto-purge.md) i manifesti senza tag o le immagini sbloccate anteriori a una durata specificata o impostare un criterio di [conservazione](container-registry-retention-policy.md) per i manifesti senza tag.

## <a name="next-steps"></a>Passaggi successivi

Per una descrizione più dettagliata dei concetti illustrati in questo articolo, vedere il post di blog [Docker Tagging: Procedure consigliate per](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)il tagging e il controllo delle versioni delle immagini della finestra mobile.

Per ottimizzare le prestazioni e l'utilizzo economico del registro dei contenitori di Azure, vedere Procedure consigliate per il Registro di sistema [dei contenitori](container-registry-best-practices.md)di Azure.To help maximize the performance and cost-effective use of your Azure container registry, see Best practices for Azure Container Registry .

<!-- IMAGES -->


<!-- LINKS - Internal -->

