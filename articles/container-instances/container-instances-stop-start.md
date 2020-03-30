---
title: Arrestare o avviare manualmente il gruppo di contenitori
description: Informazioni su come arrestare o avviare manualmente un gruppo di contenitori nelle istanze del contenitore di Azure.Learn how to manually stop or start a container group in Azure Container Instances.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533432"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Arrestare o avviare manualmente i contenitori nelle istanze del contenitore di AzureManually stop or start containers in Azure Container Instances

L'impostazione dei criteri di [riavvio](container-instances-restart-policy.md) di un gruppo di contenitori determina la modalità di avvio o arresto delle istanze del contenitore per impostazione predefinita. È possibile ignorare l'impostazione predefinita arrestando o avviando manualmente un gruppo di contenitori.

## <a name="stop"></a>Arresto

Arrestare manualmente un gruppo di contenitori in esecuzione, ad esempio usando il comando az container stop o il portale di Azure.Manually stop a running container group - for example, by using the [az container stop][az-container-stop] command or Azure portal. Per alcuni carichi di lavoro di contenitore, è possibile arrestare un gruppo di contenitori a esecuzione prolungata dopo un periodo definito per risparmiare sui costi. 

*Quando un gruppo di contenitori entra nello stato Arrestato, termina e ricicla tutti i contenitori del gruppo. Non mantiene lo stato del contenitore.*

Quando i contenitori vengono riciclati, le [risorse](container-instances-container-groups.md#resource-allocation) vengono deallocate e la fatturazione viene interrotta per il gruppo di contenitori.

L'azione di arresto non ha effetto se il gruppo di contenitori è già terminato (si trova nello stato Riuscito o Non riuscito). Ad esempio, un gruppo di contenitori con attività del contenitore run-once eseguite correttamente termina lo stato Succeeded.For example, a container group with run-once container tasks that ran successfully terminates in the Succeeded state. I tentativi di arrestare il gruppo in tale stato non modificano lo stato. 

## <a name="start"></a>Inizia

Quando un gruppo di contenitori viene arrestato, perché i contenitori sono terminati autonomamente o il gruppo è stato arrestato manualmente, è possibile avviare i contenitori. Ad esempio, usare il comando [az container start][az-container-start] o il portale di Azure per avviare manualmente i contenitori nel gruppo. Se l'immagine di un contenitore viene aggiornata, verrà inserita una nuova immagine. 

L'avvio di un gruppo di contenitori inizia una nuova distribuzione con la stessa configurazione del contenitore. Questa azione consente di riusare rapidamente la configurazione di un gruppo di contenitori nota che funziona come previsto. Non è necessario creare un nuovo gruppo di contenitori per eseguire lo stesso carico di lavoro.

Tutti i contenitori in un gruppo di contenitori vengono avviati da questa azione. Non è possibile avviare un contenitore specifico nel gruppo.

Dopo aver avviato o riavviato manualmente un gruppo di contenitori, questo funzionerà in base ai criteri di riavvio configurati.
  
## <a name="restart"></a>Riavvio

È possibile riavviare un gruppo di contenitori mentre è in esecuzione, ad esempio usando il comando [az container restart.][az-container-restart] Questa operazione riavvia tutti i contenitori nel gruppo di contenitori. Se l'immagine di un contenitore viene aggiornata, verrà inserita una nuova immagine. 

Il riavvio di un gruppo di contenitori è utile quando si desidera risolvere un problema di distribuzione. Ad esempio, se una limitazione temporanea delle risorse impedisce la corretta esecuzione dei contenitori, riavviare il gruppo potrebbe risolvere il problema.

Tutti i contenitori in un gruppo di contenitori vengono riavviati da questa azione. Non è possibile riavviare un contenitore specifico nel gruppo.

Dopo aver riavviato manualmente un gruppo di contenitori, il gruppo di contenitori viene eseguito in base ai criteri di riavvio configurati.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle impostazioni dei criteri di [riavvio](container-instances-restart-policy.md) nelle istanze del contenitore di Azure.Learn more about restart policy settings in Azure Container Instances.

Oltre a arrestare e avviare manualmente un gruppo di contenitori con la configurazione esistente, è possibile [aggiornare le impostazioni](container-instances-update.md) di un gruppo di contenitori in esecuzione.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
