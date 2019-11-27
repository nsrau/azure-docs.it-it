---
title: Arrestare o avviare manualmente il gruppo di contenitori
description: Informazioni su come arrestare o avviare manualmente un gruppo di contenitori in istanze di contenitore di Azure.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533432"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Arrestare o avviare manualmente i contenitori nelle istanze di contenitore di Azure

L'impostazione dei [criteri di riavvio](container-instances-restart-policy.md) di un gruppo di contenitori determina il modo in cui le istanze del contenitore vengono avviate o interrotte per È possibile eseguire l'override dell'impostazione predefinita arrestando o avviando manualmente un gruppo di contenitori.

## <a name="stop"></a>Interrompi

Arrestare manualmente un gruppo di contenitori in esecuzione, ad esempio usando il comando [AZ container stop][az-container-stop] o portale di Azure. Per determinati carichi di lavoro del contenitore, potrebbe essere necessario arrestare un gruppo di contenitori con esecuzione prolungata dopo un periodo definito per risparmiare sui costi. 

*Quando un gruppo di contenitori entra nello stato interrotto, termina e ricicla tutti i contenitori del gruppo. Non mantiene lo stato del contenitore.*

Quando i contenitori vengono riciclati, le [risorse](container-instances-container-groups.md#resource-allocation) vengono deallocate e la fatturazione viene arrestata per il gruppo di contenitori.

L'azione di arresto non ha effetto se il gruppo di contenitori è già stato terminato (si trova nello stato SUCCEEDED o Failed). Ad esempio, un gruppo di contenitori con attività del contenitore Run-Once che è stato eseguito correttamente termina con lo stato succeeded. Il tentativo di arrestare il gruppo in tale stato non modifica lo stato. 

## <a name="start"></a>Inizio

Quando un gruppo di contenitori viene arrestato, perché i contenitori sono terminati autonomamente oppure il gruppo è stato arrestato manualmente, è possibile avviare i contenitori. Ad esempio, usare il comando [AZ container Start][az-container-start] o portale di Azure per avviare manualmente i contenitori nel gruppo. Se l'immagine di un contenitore viene aggiornata, verrà inserita una nuova immagine. 

L'avvio di un gruppo di contenitori inizia una nuova distribuzione con la stessa configurazione del contenitore. Questa azione consente di riusare rapidamente la configurazione di un gruppo di contenitori nota che funziona come previsto. Non è necessario creare un nuovo gruppo di contenitori per eseguire lo stesso carico di lavoro.

Tutti i contenitori in un gruppo di contenitori vengono avviati da questa azione. Non è possibile avviare un contenitore specifico nel gruppo.

Dopo aver avviato o riavviato manualmente un gruppo di contenitori, questo funzionerà in base ai criteri di riavvio configurati.
  
## <a name="restart"></a>Riavvio

È possibile riavviare un gruppo di contenitori mentre è in esecuzione, ad esempio usando il comando [AZ container restart][az-container-restart] . Questa operazione riavvia tutti i contenitori nel gruppo di contenitori. Se l'immagine di un contenitore viene aggiornata, verrà inserita una nuova immagine. 

Il riavvio di un gruppo di contenitori è utile quando si desidera risolvere un problema di distribuzione. Ad esempio, se una limitazione temporanea delle risorse impedisce la corretta esecuzione dei contenitori, riavviare il gruppo potrebbe risolvere il problema.

Tutti i contenitori in un gruppo di contenitori vengono riavviati da questa azione. Non è possibile riavviare un contenitore specifico nel gruppo.

Dopo aver riavviato manualmente un gruppo di contenitori, il gruppo di contenitori viene eseguito in base ai criteri di riavvio configurati.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [impostazioni dei criteri di riavvio](container-instances-restart-policy.md) in istanze di contenitore di Azure.

Oltre a arrestare e avviare manualmente un gruppo di contenitori con la configurazione esistente, è possibile [aggiornare le impostazioni](container-instances-update.md) di un gruppo di contenitori in esecuzione.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
