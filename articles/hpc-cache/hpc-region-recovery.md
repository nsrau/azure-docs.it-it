---
title: Ridondanza regionale e ripristino del failover con la cache HPC di AzureRegional redundancy and failover recovery with Azure HPC Cache
description: Tecniche per fornire funzionalità di failover per il ripristino di emergenza con la cache HPC di AzureTechniques to provide failover capabilities for disaster recovery with Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 21074ae6bc4959da031bc7065cd7d0639ec2a14f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537271"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Usare più cache per il ripristino del failover regionaleUse multiple caches for regional failover recovery

Ogni istanza della cache HPC di Azure viene eseguita all'interno di una sottoscrizione specifica e in un'area. Ciò significa che il flusso di lavoro della cache potrebbe essere interrotto se l'area presenta un'interruzione completa.

In questo articolo viene descritta una strategia per ridurre il rischio di interruzione del lavoro utilizzando una seconda area per il failover della cache.

La chiave usa l'archiviazione back-end accessibile da più aree. Questa archiviazione può essere un sistema NAS locale con supporto DNS appropriato o un'archiviazione BLOB di Azure che si trova in un'area diversa dalla cache.

Man mano che il flusso di lavoro procede nell'area primaria, i dati vengono salvati nell'archiviazione a lungo termine al di fuori dell'area. Se l'area della cache non è più disponibile, è possibile creare un'istanza duplicata della cache HPC di Azure in un'area secondaria, connettersi allo stesso archivio e riprendere il lavoro dalla nuova cache.

## <a name="planning-for-regional-failover"></a>Pianificazione del failover regionale

Per configurare una cache preparata per un possibile failover, attenersi alla seguente procedura:

1. Assicurarsi che lo spazio di archiviazione back-end sia accessibile in una seconda area.
1. Quando si pianifica la creazione dell'istanza della cache primaria, è inoltre necessario preparare la replica di questo processo di installazione nella seconda area. Includi questi elementi:

   1. Rete virtuale e struttura di subnetVirtual network and subnet structure
   1. Capacità della cache
   1. Dettagli, nomi e percorsi degli spazi dei nomi della destinazione di archiviazioneStorage target details, names, and namespace paths
   1. Dettagli sui computer client, se si trovano nella stessa area della cache
   1. Comando Mount per l'utilizzo da parte dei client della cache

   > [!NOTE]
   > La cache HPC di Azure può essere creata a livello di codice, tramite un modello di [Azure Resource Manager](../azure-resource-manager/templates/overview.md) o accedendo direttamente alla relativa API. Per informazioni dettagliate, contattare il team della cache HPC di Azure.Contact the Azure HPC Cache team for details.

## <a name="failover-example"></a>Esempio di failover

Ad esempio, si supponga di voler individuare la cache HPC di Azure nell'area Stati Uniti orientali di Azure.As an example, imagine that you want to locate your Azure HPC Cache in Azure's East US region. Accederà ai dati archiviati nel data center locale.

È possibile utilizzare una cache nell'area Stati Uniti occidentali 2 come backup di failover.

Quando si crea la cache negli Stati Uniti orientali, preparare una seconda cache per la distribuzione negli Stati Uniti occidentali 2. È possibile utilizzare script o modelli per automatizzare questa preparazione.

In caso di errore negli Stati Uniti orientali, creare la cache preparata nell'area Stati Uniti occidentali 2.

Dopo aver creato la cache, aggiungere destinazioni di archiviazione che puntano agli stessi archivi dati locali e usare gli stessi percorsi dello spazio dei nomi aggregati delle destinazioni di archiviazione della cache precedente.

Se i client originali sono interessati, creare nuovi client nell'area Stati Uniti occidentali 2 da utilizzare con la nuova cache.

Tutti i client dovranno montare la nuova cache, anche se i client non sono stati interessati dall'interruzione dell'area. La nuova cache ha indirizzi di montaggio diversi da quello precedente.

## <a name="learn-more"></a>Altre informazioni

La guida all'architettura dell'applicazione Azure include ulteriori informazioni su come [eseguire il ripristino da un'interruzione](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)del servizio a livello di area.
