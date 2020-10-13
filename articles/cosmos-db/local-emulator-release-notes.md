---
title: 'Emulatore Azure Cosmos: download e note sulla versione'
description: Ottenere le note sulla versione dell'emulatore Azure Cosmos per diverse versioni e le informazioni sul download.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: f2a40744053ccc804b2513faf1bc2879d4fde902
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777174"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Emulatore Azure Cosmos - Note sulla versione e informazioni di download

Questo articolo include le note sulla versione dell'emulatore Azure Cosmos con un elenco di aggiornamenti delle funzionalità apportati in ogni versione. Inoltre, include la versione più recente dell'emulatore disponibile per il download.

## <a name="download"></a>Download

| | |
|---------|---------|
|**Download MSI**|[Area download Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Operazioni preliminari**|[Sviluppare in locale con l'emulatore Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Note sulla versione

### <a name="2116-6-october-2020"></a>2.11.6 (6 ottobre 2020)

 - Questa versione risolve un problema correlato alla concorrenza per cui è possibile creare più contenitori contemporaneamente. In questi casi, i dati dell'emulatore vengono lasciati in uno stato danneggiato e le successive richieste API all'endpoint dell'emulatore potrebbero non riuscire con errori di tipo "servizio non disponibile", richiedendo un riavvio e una reimpostazione dei dati locali dell'emulatore.

### <a name="2115-23-august-2020"></a>2.11.5 (23 agosto 2020)

Questa versione aggiunge due nuove opzioni di avvio dell'emulatore Cosmos: 

* "/EnablePreview": abilita le funzionalità di anteprima per l'emulatore. Le funzionalità di anteprima che sono ancora in fase di sviluppo ed è possibile accedervi tramite CI e la scrittura di esempio.
* "/EnableAadAuthentication": consente all'emulatore di accettare token di Azure Active Directory personalizzati come alternativa alle chiavi primarie di Azure Cosmos. Questa funzionalità è ancora in fase di sviluppo; le assegnazioni di ruolo specifiche e altre impostazioni correlate alle autorizzazioni non sono attualmente supportate.

### <a name="2112-07-july-2020"></a>2.11.2 (07 luglio 2020)

- Questa versione modifica la modalità di raccolta delle tracce ETL necessarie per la risoluzione dei problemi relativi all'emulatore Cosmos. Per impostazione predefinita, per l'acquisizione di tracce basate su ETL vengono ora usati gli strumenti WPR (Windows Performance Runtime), mentre l'acquisizione precedente, basata su LOGMAN, è stata deprecata. Questa modifica è necessaria in parte perché gli aggiornamenti della sicurezza di Windows più recenti hanno un effetto imprevisto sul funzionamento di LOGMAN quando viene eseguiti tramite l'emulatore di Cosmos.

### <a name="2111-10-june-2020"></a>2.11.1 (10 giugno 2020)

- In questa versione sono stati corretti un paio di bug relativi a Esplora dati dell'emulatore. In alcuni casi, quando si usa Esplora dati dell'emulatore tramite un Web browser, non è possibile connettersi all'endpoint dell'emulatore Cosmos e a tutte le azioni correlate. Ad esempio la creazione di un database o di un contenitore restituirà un errore. Il secondo problema risolto è relativo alla creazione di un elemento da un file JSON usando l'azione di caricamento di Esplora dati.

### <a name="2110"></a>2.11.0

- Questa versione introduce il supporto per la velocità effettiva con provisioning a scalabilità automatica. Queste nuove funzionalità includono la possibilità di impostare un livello massimo personalizzato di velocità effettiva con provisioning in unità richiesta (UR/sec) e di abilitare la scalabilità automatica su database e contenitori esistenti, oltre a offrire il supporto a livello di codice tramite Azure Cosmos DB SDK.
- Correzione di un errore per cui durante l'esecuzione di query su un numero elevato di documenti (oltre 1 GB), l'emulatore genera un errore con codice di stato interno 500.

### <a name="292"></a>2.9.2

- Questa versione corregge un bug e abilita al tempo stesso il supporto per l'endpoint MongoDb versione 3.2. Aggiunge inoltre il supporto per la generazione di tracce ETL a scopo di risoluzione dei problemi usando WPR invece di LOGMAN.

### <a name="291"></a>2.9.1

- Questa versione corregge alcuni problemi del supporto per l'API di query e ripristina la compatibilità con i sistemi operativi precedenti, ad esempio Windows Server 2012.

### <a name="290"></a>2.9.0

- In questa versione è stata aggiunta l'opzione per impostare la coerenza del prefisso e aumentare i limiti massimi per utenti e autorizzazioni.

### <a name="272"></a>2.7.2

- Questa versione aggiunge il supporto per il server MongoDB versione 3.6 all'emulatore Cosmos. Per avviare un endpoint MongoDB destinato alla versione 3.6 del servizio, avviare l'emulatore dalla riga di comando con privilegi di amministratore con l'opzione "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Questa versione corregge una regressione che impedisce agli utenti di eseguire query sull'account API SQL dall'emulatore quando vengono usati client basati su .NET Core o x86.

### <a name="246"></a>2.4.6

- Questa versione offre le stesse funzionalità incluse nella versione di luglio 2019 del servizio Azure Cosmos, con le eccezioni indicate in [Sviluppare in locale con l'emulatore Azure Cosmos](local-emulator.md). Vengono inoltre corretti diversi bug correlati all'arresto dell'emulatore quando viene richiamato tramite la riga di comando e gli override degli indirizzi IP interni per i client SDK che usano la connettività in modalità diretta.

### <a name="243"></a>2.4.3

- Avvio del servizio MongoDB disattivato per impostazione predefinita. Per impostazione predefinita è abilitato solo l'endpoint SQL. L'utente deve avviare l'endpoint manualmente usando l'opzione "/EnableMongoDbEndpoint" della riga di comando dell'emulatore. Adesso è come tutti gli altri endpoint di servizio, ad esempio Gremlin, Cassandra e Table.
- Risolto un bug nell'emulatore quando si inizia con "/AllowNetworkAccess" in cui gli endpoint Gremlin, Cassandra e Table non gestiscono correttamente le richieste provenienti da client esterni.
- Aggiungere porte di connessione diretta alle impostazioni delle regole del Firewall.

### <a name="240"></a>2.4.0

- Risolto un problema con l'emulatore relativo al mancato avvio se nel computer host sono presenti app di monitoraggio di rete, come Pulse Client.
