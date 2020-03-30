---
title: Protezione dei dati in Analisi di flusso di AzureData protection in Azure Stream Analytics
description: Questo articolo illustra come crittografare i dati privati usati da un processo di Analisi di flusso di Azure.This article explains how to encrypt your private data used by an Azure Stream Analytics job.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299397"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Protezione dei dati in Analisi di flusso di AzureData protection in Azure Stream Analytics 

Azure Stream Analytics è una piattaforma come servizio completamente gestita che consente di creare pipeline di analisi in tempo reale. Tutto il lavoro pesante, ad esempio il provisioning del cluster, la scalabilità dei nodi per soddisfare l'utilizzo e la gestione dei checkpoint interni, viene gestito dietro le quinte.

## <a name="encrypt-your-data"></a>Crittografa i tuoi dati

Stream Analytics utilizza automaticamente i migliori standard di crittografia della sua infrastruttura per crittografare e proteggere i tuoi dati. Puoi semplicemente considerare attendibile Analisi di flusso per archiviare in modo sicuro tutti i tuoi dati in modo da non doverti preoccupare della gestione dell'infrastruttura.

Se si desidera utilizzare chiavi gestite dal cliente (CMK) per crittografare i dati, è possibile usare il proprio account di archiviazione (generico V1 o V2) per archiviare tutti gli asset di dati privati richiesti dal runtime di Analisi di flusso. L'account di archiviazione può essere crittografato in base alle esigenze. Nessuno dei tuoi asset di dati privati viene archiviato in modo permanente dall'infrastruttura di Analisi di flusso. 

Questa impostazione deve essere configurata al momento della creazione del processo di Analisi di flusso e non può essere modificata per tutto il ciclo di vita del processo. La modifica o l'eliminazione dello spazio di archiviazione utilizzato da Analisi di flusso non è consigliata. Se si elimina l'account di archiviazione, verranno eliminati definitivamente tutti gli asset di dati privati, con l'esito negativo del processo. 

Non è possibile aggiornare o ruotare le chiavi nell'account di archiviazione usando il portale di Analisi di flusso. È possibile aggiornare le chiavi usando le API REST.


## <a name="configure-storage-account-for-private-data"></a>Configurare l'account di archiviazione per i dati privatiConfigure storage account for private data 

Usare la procedura seguente per configurare l'account di archiviazione per gli asset di dati privati. Questa configurazione viene effettuata dal processo di Analisi di flusso, non dall'account di archiviazione.

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Selezionare **Crea risorsa** nell'angolo superiore sinistro del portale di Azure. 

1. Seleziona Il **processo** Analytics di flusso **di Analytics** >dall'elenco dei risultati. 

1. Compila la pagina del processo di Analisi di flusso con i dettagli necessari, ad esempio nome, area geografica e scala. 

1. Selezionare la casella di controllo *Proteggi tutti gli asset di dati privati necessari per questo processo nell'account di archiviazione.*

1. Selezionare un account di archiviazione dalla sottoscrizione. Si noti che questa impostazione non può essere modificata per tutto il ciclo di vita del processo. 

   ![Impostazioni dell'account di archiviazione dati privatoPrivate data storage account settings](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Asset di dati privati archiviati

Tutti i dati privati che devono essere resi persistenti da Analisi di flusso vengono archiviati nell'account di archiviazione. Esempi di asset di dati privati includono: 

* Query create e relative configurazioni  

* Funzioni definite dall'utente 

* Checkpoint necessari per il runtime di Analisi di flussoCheckpoints needed by the Stream Analytics runtime

* Istantanee dei dati di riferimento 

Vengono archiviati anche i dettagli di connessione delle risorse utilizzate dal processo di Analisi di flusso. Crittografare l'account di archiviazione per proteggere tutti i dati. 

Per aiutarti a soddisfare i tuoi obblighi di conformità in qualsiasi settore o ambiente regolamentato, puoi leggere ulteriori informazioni sulle offerte di [conformità di Microsoft.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account di archiviazione di AzureCreate an Azure Storage account](../storage/common/storage-account-create.md)
* [Informazioni sugli input per Analisi di flusso di Azure](stream-analytics-add-inputs.md)
* [Concetti di checkpoint e riproduzione nei processi di Analisi di flusso di Azure](stream-analytics-concepts-checkpoint-replay.md)
* [Uso dei dati di riferimento per le ricerche in Analisi di flusso](stream-analytics-use-reference-data.md)
