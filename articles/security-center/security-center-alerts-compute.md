---
title: Il rilevamento per il calcolo nativo del cloud in Centro sicurezza di Azure per le minacce | Microsoft Docs
description: Questo argomento viene presentata nel cloud gli avvisi di calcolo nativo disponibili nel Centro sicurezza di Azure. nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: bc3cb66d43e71777e06c6bd63dcff35e2ff19df8
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571686"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Rilevamento delle minacce per il cloud calcolo nativo nel Centro sicurezza di Azure

Come provider di cloud, il Centro sicurezza Usa la visibilità univoca per analizzare i log interni e identificare la metodologia di attacco su più destinazioni. Questo argomento vengono illustrati gli avvisi disponibili per i servizi di Azure seguenti:

* [Servizio app di Azure](#app-services)
* [Contenitori](#azure-containers) 

## Servizio App di Azure <a name="app-services"></a>

Il Centro sicurezza Usa la scalabilità del cloud per identificare gli attacchi ai clienti le applicazioni in esecuzione nel servizio App di Azure di destinazione. Con le applicazioni web in corso praticamente in qualsiasi rete moderne, gli utenti malintenzionati di probe per trovarli e sfruttare i punti deboli. Prima che venga indirizzato in ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso i gateway diversi in cui sono controllati e registrati. Questi dati vengono quindi utilizzati per identificare gli attacchi, gli utenti malintenzionati e per apprendere nuovi schemi che verranno usati successivamente.

Sfruttando la visibilità che dispone di Azure come provider di cloud, il Centro sicurezza analizza i log del servizio App interno per identificare la metodologia di attacco su più destinazioni. Ad esempio, l'analisi generalizzata e attacchi distribuiti. Questo tipo di attacco in genere proviene da un piccolo subset di indirizzi IP e Mostra i modelli di ricerca per indicizzazione agli endpoint simile in più host, la ricerca di una pagina vulnerabile o plug-in. Questo può essere rilevato tramite Cloud ma non può essere identificato dal punto di vista di un singolo host.

Il Centro sicurezza ha inoltre accesso a sandbox e le macchine virtuali sottostanti. Insieme a scopi legali di memoria, l'infrastruttura possibile raccontare la storia, da un nuovo attacco circolano in circolazione per compromissioni in macchine dei clienti. Pertanto, il Centro sicurezza può rilevare gli attacchi contro le applicazioni web molto dopo che subisce l'attacco.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Chiamata di tema WordPress sospetta rilevata**|Il log attività di servizio App di Azure indica un'attività di inserimento di codice possibili sulla risorsa del servizio App.<br/> Questa attività sospetta è simile a un'attività che consente di modificare un tema di WordPress per supportare l'esecuzione di server-side di codice, seguito da una richiesta web diretti per richiamare il file di tema manipolato. Questo tipo di attività è verificato in passato come parte di una campagna di attacco su WordPress.|
|**Connessione alla pagina web dall'indirizzo IP anomalo rilevato**|Il log attività di servizio App di Azure indica una connessione a una pagina web sensibili da un indirizzo di origine che mai connessi a prima. Ciò potrebbe indicare che un utente tenta un attacco di forza bruta nelle pagine di amministrazione di App Web. Potrebbe anche essere il risultato di un nuovo indirizzo IP usato da un utente valido.|
|**Un indirizzo IP che connessa all'interfaccia di Azure App Service FTP è stato trovato nell'Intelligence per le minacce**|Analisi di log FTP di servizio App Azure ha rilevato una connessione da un indirizzo di origine che è stato trovato nel feed di Intelligence per le minacce. Durante la connessione, un utente di accedere alle pagine elencate di seguito.|
|**Web di creare impronte digitali rilevato**|Il log attività di servizio App di Azure indica un possibile web attività sulla risorsa del servizio App di creare impronte digitali. <br/>Questa attività sospetta rilevata viene associata uno strumento denominato Elephant nascosta. Lo strumento di impronte digitali server web e prova a rilevare le applicazioni installate e le relative versioni. Gli utenti malintenzionati usano spesso questo strumento per le applicazioni web di probe per individuare le vulnerabilità.|
|**Accesso sospetta a pagina web potenzialmente vulnerabili rilevato**|Il log attività di servizio App di Azure indica che una pagina web che sembra essere sensibili ha effettuato l'accesso. <br/>Questa attività sospetta, originata da un indirizzo di origine il cui modello di accesso è simile a quello di uno scanner di web. Questo tipo di attività è spesso associato a un tentativo per un utente malintenzionato di scansione della rete per tentare di ottenere l'accesso a dati sensibili o vulnerabile pagine web.|
|**File PHP nella cartella di caricamento**|Il log attività di servizio App di Azure indica che un elemento ha eseguito l'accesso a una pagina PHP sospetta che si trova nella cartella di caricamento. <br/>Questo tipo di cartella non contiene in genere file PHP. L'esistenza di questo tipo di file potrebbe indicare un exploit che sfruttano le vulnerabilità di caricamento di file arbitrario.|
|**Un tentativo di eseguire i comandi di Linux in un servizio App di Windows**|Analisi dei processi del servizio App ha rilevato un tentativo di eseguire un comando di Linux in un servizio App di Windows. Questa azione è stata in esecuzione nell'applicazione web. Questo comportamento viene spesso visualizzato durante le campagne che sfrutta una vulnerabilità in un'applicazione web comuni.|
|**Esecuzione di PHP sospetta rilevata**|Macchina log indicano che un sospette processo PHP è in esecuzione. L'azione è incluso un tentativo di eseguire i comandi del sistema operativo o codice PHP dalla riga di comando tramite il processo PHP. Questo comportamento può essere legittimo, nelle applicazioni web questo comportamento è anche osservato in attività dannose, ad esempio i tentativi di infettare i siti Web con Shell web.|
|**Esecuzione del processo dalla cartella temporanea**|Analisi di processi del servizio App ha rilevato un'esecuzione di un processo dalla cartella temporanea dell'app. Questo comportamento può essere legittimo, nelle applicazioni web questo comportamento è anche osservato in attività dannose.|
|**Tenta di eseguire comandi con privilegi elevati ha rilevato**|Analisi dei processi del servizio App ha rilevato un tentativo di eseguire un comando che richiede privilegi elevati. Il comando è stato eseguito nel contesto dell'applicazione web. Questo comportamento può essere legittimo, nelle applicazioni web questo comportamento è anche osservato in attività dannose.|

> [!NOTE]
> Rilevamento delle minacce del Centro sicurezza per il servizio App non è attualmente disponibile in Azure per enti pubblici e aree del cloud con sovranità.

Per altre informazioni sul rilevamento delle minacce nel servizio App avvisi visitare proteggere servizio App con il Centro sicurezza di Azure e verificare come abilitare il monitoraggio e la protezione dei carichi di lavoro del servizio App.

## Contenitori <a name="azure-containers"></a>

Centro sicurezza offre il rilevamento delle minacce in tempo reale per i contenitori in macchine Linux basate su framework auditd. Gli avvisi identificano diverse attività sospette di Docker, ad esempio la creazione di un contenitore con privilegi in un host, un'indicazione dei server Secure Shell (SSH) in esecuzione all'interno di un contenitore Docker o l'uso di miner crypto. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori. Oltre ai rilevamenti di Linux, il Centro sicurezza offre inoltre analitica più specifici per le distribuzioni di contenitori.
