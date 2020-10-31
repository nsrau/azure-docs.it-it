---
title: Protezione dei dati in analisi di flusso di Azure
description: Questo articolo illustra come crittografare i dati privati usati da un processo di analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: e823322803958f092cee3b6d77e6a0ca7bc6e3f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074251"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Protezione dei dati in analisi di flusso di Azure 

Analisi di flusso di Azure è una piattaforma distribuita come servizio completamente gestita che consente di compilare pipeline di analisi in tempo reale. Tutto il lavoro pesante, ad esempio il provisioning del cluster, il ridimensionamento dei nodi per adattarlo all'utilizzo e la gestione dei checkpoint interni, viene gestito in background.

## <a name="private-data-assets-that-are-stored"></a>Asset di dati privati archiviati

Analisi di flusso di Azure rende permanente i metadati e i dati seguenti per l'esecuzione: 

* Definizione della query e configurazione correlata  

* Funzioni o aggregazioni definite dall'utente  

* Checkpoint necessari per il runtime di analisi di flusso

* Snapshot dei dati di riferimento 

* Dettagli della connessione delle risorse usate dal processo di analisi di flusso

Per soddisfare gli obblighi di conformità in qualsiasi settore o ambiente regolamentato, è possibile leggere altre informazioni sulle [offerte di conformità di Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="in-region-data-residency"></a>In-Region residenza dei dati
Analisi di flusso di Azure archivia i dati dei clienti e altri metadati descritti in precedenza. Per impostazione predefinita, i dati dei clienti vengono archiviati da analisi di flusso di Azure in una singola area, in modo che questo servizio soddisfi automaticamente i requisiti di residenza dei dati dell'area, inclusi quelli specificati nel [Centro protezione](https://azuredatacentermap.azurewebsites.net/).
Inoltre, è possibile scegliere di archiviare tutti gli asset di dati (dati del cliente e altri metadati) correlati al processo di analisi di flusso in un'unica area crittografando i dati in un account di archiviazione di propria scelta.

## <a name="encrypt-your-data"></a>Crittografare i dati

Analisi di flusso usa automaticamente gli standard di crittografia migliori all'interno dell'infrastruttura per crittografare e proteggere i dati. È possibile considerare semplicemente attendibile analisi di flusso per archiviare in modo sicuro tutti i dati, in modo da non dover preoccuparsi di gestire l'infrastruttura.

Se si vuole usare chiavi gestite dal cliente (CMK) per crittografare i dati, è possibile usare il proprio account di archiviazione (utilizzo generico V1 o V2) per archiviare gli asset di dati privati richiesti dal runtime di analisi di flusso. L'account di archiviazione può essere crittografato in base alle esigenze. Nessuno degli asset di dati privati viene archiviato in modo permanente dall'infrastruttura di analisi di flusso. 

Questa impostazione deve essere configurata al momento della creazione del processo di analisi di flusso e non può essere modificata nel corso del ciclo di vita del processo. La modifica o l'eliminazione dello spazio di archiviazione usato dall'analisi di flusso non è consigliata. Se si elimina l'account di archiviazione, verranno eliminati definitivamente tutti gli asset di dati privati, causando un errore nel processo. 

Non è possibile aggiornare o ruotare le chiavi nell'account di archiviazione usando il portale di analisi di flusso. È possibile aggiornare le chiavi usando le API REST.


### <a name="configure-storage-account-for-private-data"></a>Configurare l'account di archiviazione per i dati privati 


Crittografare l'account di archiviazione per proteggere tutti i dati e scegliere in modo esplicito il percorso dei dati privati. 

Per soddisfare gli obblighi di conformità in qualsiasi settore o ambiente regolamentato, è possibile leggere altre informazioni sulle [offerte di conformità di Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 



Usare la procedura seguente per configurare l'account di archiviazione per gli asset di dati privati. Questa configurazione viene eseguita dal processo di analisi di flusso, non dall'account di archiviazione.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Crea risorsa** nell'angolo superiore sinistro del portale di Azure. 

1. Selezionare **Analytics analisi**   > di **flusso processo**   dall'elenco risultati. 

1. Compilare la pagina del processo di analisi di flusso con i dettagli necessari, ad esempio il nome, l'area e la scala. 

1. Selezionare la casella di controllo per *proteggere tutti gli asset di dati privati necessari per questo processo nell'account di archiviazione* .

1. Selezionare un account di archiviazione dalla sottoscrizione. Si noti che questa impostazione non può essere modificata durante tutto il ciclo di vita del processo. 

   ![Impostazioni dell'account di archiviazione dati privato](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Asset di dati privati archiviati

Tutti i dati privati che devono essere salvati in modo permanente da analisi di flusso vengono archiviati nell'account di archiviazione. Esempi di asset di dati privati includono: 

* Query create e relative configurazioni  

* Funzioni definite dall'utente 

* Checkpoint necessari per il runtime di analisi di flusso

* Snapshot dei dati di riferimento 

Vengono archiviati anche i dettagli della connessione delle risorse, usati dal processo di analisi di flusso. Crittografare l'account di archiviazione per proteggere tutti i dati. 

Per soddisfare gli obblighi di conformità in qualsiasi settore o ambiente regolamentato, è possibile leggere altre informazioni sulle [offerte di conformità di Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="enables-data-residency"></a>Abilita la residenza dei dati 
Questa funzionalità può essere usata per applicare eventuali requisiti di residenza dei dati, fornendo un account di archiviazione di conseguenza.

## <a name="known-issues"></a>Problemi noti
Si verifica un problema noto in cui un processo che usa la chiave gestita dal cliente si verifica in caso di errori quando si usa l'identità gestita per l'autenticazione a qualsiasi input o output. 

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md)
* [Informazioni sugli input per Analisi di flusso di Azure](stream-analytics-add-inputs.md)
* [Concetti di checkpoint e riproduzione nei processi di Analisi di flusso di Azure](stream-analytics-concepts-checkpoint-replay.md)
* [Uso dei dati di riferimento per le ricerche in Analisi di flusso](stream-analytics-use-reference-data.md)
