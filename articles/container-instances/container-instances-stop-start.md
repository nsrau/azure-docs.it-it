---
title: Arresta o Avvia i contenitori in istanze di contenitore di Azure manualmente
description: Informazioni su come arrestare o avviare un gruppo di contenitori in istanze di contenitore di Azure manualmente.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 8e62d106a42dfbec897e5e14cf68fd3d7fd823c4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65070822"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Arresta o Avvia i contenitori in istanze di contenitore di Azure manualmente

Il [criterio di riavvio](container-instances-restart-policy.md) impostazione di un gruppo di contenitori determina come istanze di contenitore di avvio o arresto per impostazione predefinita. È possibile sostituire il valore predefinito impostando manualmente l'arresto o avvio di un gruppo di contenitori.

## <a name="stop"></a>Arresto

Arrestare manualmente, ad esempio, un gruppo di contenitori in esecuzione - usando il [stop di contenitore di az] [ az-container-stop] comando o il portale di Azure. Per alcuni carichi di lavoro contenitore, si potrebbe voler interrompere un gruppo di contenitori con esecuzione prolungata dopo un periodo definito per risparmiare sui costi. 

*Quando un gruppo contenitore passa allo stato Stopped, termina e viene riciclato tutti i contenitori nel gruppo. Lo stato del contenitore non vengono mantenuti.*

Quando i contenitori vengono riciclati, la [risorse](container-instances-container-groups.md#resource-allocation) vengono deallocate e la fatturazione viene arrestata per il gruppo di contenitori.

L'azione di interruzione non ha alcun effetto se il gruppo di contenitori è già terminata (è nello stato Succeeded o Failed). Ad esempio, un gruppo di contenitori con attività contenitore eseguire una volta che è stato eseguito correttamente termina nello stato riuscito. È stato effettuato un tentativo di arrestare il gruppo nel cui stato di non modificare lo stato. 

## <a name="start"></a>Inizia

Quando viene arrestato un gruppo di contenitori - poiché i contenitori terminata in modo indipendente o è stato arrestato manualmente il gruppo - è possibile avviare i contenitori. Ad esempio, usare il [inizio di contenitore di az] [ az-container-start] comando o il portale di Azure per avviare manualmente i contenitori nel gruppo. Se l'immagine di un contenitore viene aggiornata, verrà inserita una nuova immagine. 

L'avvio di un gruppo di contenitori inizia una nuova distribuzione con la stessa configurazione del contenitore. Questa azione consente di riusare rapidamente la configurazione di un gruppo di contenitori nota che funziona come previsto. Non è necessario creare un nuovo gruppo di contenitori per eseguire lo stesso carico di lavoro.

Tutti i contenitori in un gruppo di contenitori vengono avviati da questa azione. È possibile avviare un contenitore specifico nel gruppo.

Dopo aver avviato o riavviato manualmente un gruppo di contenitori, questo funzionerà in base ai criteri di riavvio configurati.
  
## <a name="restart"></a>Riavvio

È possibile riavviare un gruppo di contenitori durante l'esecuzione, ad esempio, tramite il [riavvio di contenitore di az] [ az-container-restart] comando. Questa operazione riavvia tutti i contenitori nel gruppo di contenitori. Se l'immagine di un contenitore viene aggiornata, verrà inserita una nuova immagine. 

Il riavvio di un gruppo di contenitori è utile quando si desidera risolvere un problema di distribuzione. Ad esempio, se una limitazione temporanea delle risorse impedisce la corretta esecuzione dei contenitori, riavviare il gruppo potrebbe risolvere il problema.

Tutti i contenitori in un gruppo contenitore vengono riavviati da questa azione. Nel gruppo non è possibile riavviare un contenitore specifico.

Dopo aver riavviato manualmente un gruppo di contenitori, le esecuzioni di gruppo contenitore in base a configurati criteri di riavvio.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [impostazioni di criteri di riavvio](container-instances-restart-policy.md) in istanze di contenitore di Azure.

Oltre a manualmente l'arresto e avvio di un gruppo di contenitori con la configurazione esistente, è possibile [aggiornare le impostazioni](container-instances-update.md) di un gruppo di contenitori in esecuzione.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
