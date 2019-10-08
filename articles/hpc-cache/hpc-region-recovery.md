---
title: Ridondanza a livello di area e ripristino del failover con la cache HPC di Azure (anteprima)
description: Tecniche per fornire funzionalità di failover per il ripristino di emergenza con cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: rohogue
ms.openlocfilehash: ca771ceed433c6eb0ed26bba6fcb1bf11db211f0
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031617"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Usare più cache per il ripristino del failover a livello di area

Ogni istanza della cache HPC di Azure viene eseguita in una sottoscrizione specifica e in un'area. Ciò significa che è possibile che il flusso di lavoro della cache venga interrotta se l'area presenta un'interruzione completa.

Questo articolo descrive una strategia per ridurre il rischio di rotture del lavoro usando una seconda area per il failover della cache.

La chiave usa l'archiviazione back-end accessibile da più aree. Questa risorsa di archiviazione può essere un sistema NAS locale con supporto DNS appropriato o un archivio BLOB di Azure che risiede in un'area diversa dalla cache.

Quando il flusso di lavoro procede nell'area primaria, i dati vengono salvati nell'archiviazione a lungo termine al di fuori dell'area. Se l'area della cache diventa non disponibile, è possibile creare un'istanza di cache HPC di Azure duplicata in un'area secondaria, connettersi alla stessa risorsa di archiviazione e riprendere il lavoro dalla nuova cache.

## <a name="planning-for-regional-failover"></a>Pianificazione del failover a livello di area

Per configurare una cache preparata per il failover possibile, attenersi alla procedura seguente:

1. Assicurarsi che l'archiviazione back-end sia accessibile in una seconda area.
1. Quando si pianifica la creazione dell'istanza della cache primaria, è necessario preparare anche la replica del processo di installazione nella seconda area. Includi gli elementi seguenti:

   1. Struttura della subnet e della rete virtuale
   1. Capacità cache
   1. Dettagli destinazione archiviazione, nomi e percorsi dello spazio dei nomi
   1. Dettagli sui computer client, se si trovano nella stessa area della cache
   1. Comando di montaggio per l'uso da parte dei client della cache

   > [!NOTE]
   > È possibile creare la cache HPC di Azure a livello di codice, tramite un [modello di Azure Resource Manager](../azure-resource-manager/template-deployment-overview.md) o accedendo direttamente alla relativa API. Per informazioni dettagliate, contattare il team di cache HPC di Azure.

## <a name="failover-example"></a>Esempio di failover

Si supponga, ad esempio, di voler individuare la cache HPC di Azure nell'area Stati Uniti orientali di Azure. Accederà ai dati archiviati nel data center locale.

È possibile usare una cache nell'area Stati Uniti occidentali 2 come backup di failover.

Quando si crea la cache negli Stati Uniti orientali, preparare una seconda cache per la distribuzione negli Stati Uniti occidentali 2. Per automatizzare questa preparazione, è possibile usare gli script o i modelli.

Nel caso di un errore a livello di area negli Stati Uniti orientali, creare la cache preparata nell'area Stati Uniti occidentali 2.

Dopo la creazione della cache, aggiungere destinazioni di archiviazione che puntano agli stessi archivi dati locali e usano gli stessi percorsi dello spazio dei nomi aggregati delle destinazioni di archiviazione della cache precedente.

Se i client originali sono interessati, creare nuovi client nell'area Stati Uniti occidentali 2 da usare con la nuova cache.

Tutti i client dovranno montare la nuova cache anche se i client non sono stati interessati dall'interruzione dell'area. La nuova cache ha indirizzi di montaggio diversi da quelli precedenti.

## <a name="learn-more"></a>Altre informazioni

La Guida all'architettura delle applicazioni di Azure include ulteriori informazioni su come eseguire il [ripristino da un'interferenza dei servizi a livello di area](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>).
<!-- this should be an internal link instead of a URL but I can't find the tree  -->
