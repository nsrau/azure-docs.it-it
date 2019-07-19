---
title: Rilevamento delle minacce per il calcolo nativo del cloud nel centro sicurezza di Azure | Microsoft Docs
description: Questo argomento presenta gli avvisi di calcolo nativi cloud disponibili nel centro sicurezza di Azure. nel centro sicurezza di Azure.
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
ms.author: v-mohabe
ms.openlocfilehash: 14433806a28e31cef1a278e16cb69e7c9b1a2458
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295834"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Rilevamento delle minacce per il calcolo nativo del cloud nel centro sicurezza di Azure

Come provider di servizi cloud, il Centro sicurezza usa la visibilità univoca per analizzare i log interni e identificare la metodologia di attacco su più destinazioni. Questo argomento presenta gli avvisi disponibili per i servizi di Azure seguenti:

* [Servizio app di Azure](#app-services)
* [Contenitori](#azure-containers) 

## Servizio app Azure<a name="app-services"></a>

Il Centro sicurezza sfrutta la scalabilità del cloud per identificare gli attacchi destinati alle applicazioni dei clienti in esecuzione sul servizio app Azure. Con le applicazioni Web praticamente in qualsiasi rete moderna, gli utenti malintenzionati si interrogano per trovare questi e svantaggi di exploit. Prima di essere indirizzati ad ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso diversi gateway dove vengono ispezionate e registrate. Questi dati vengono quindi usati per identificare gli exploit, gli utenti malintenzionati e per apprendere nuovi modelli che verranno usati in un secondo momento.

Sfruttando la visibilità di Azure come provider di servizi cloud, il Centro sicurezza analizza i log interni del servizio app per identificare la metodologia di attacco su più destinazioni. Ad esempio, analisi generalizzata e attacchi distribuiti. Questo tipo di attacco in genere deriva da un piccolo subset di indirizzi IP ed espone modelli di ricerca per indicizzazione in endpoint simili su più host, cercando una pagina o un plug-in vulnerabile. Questa operazione può essere rilevata utilizzando il cloud, ma non può essere identificata dal punto di vista di un singolo host.

Il Centro sicurezza ha anche accesso ai sandbox e alle VM sottostanti. Insieme alle analisi della memoria, l'infrastruttura può raccontare la storia, da un nuovo attacco che circola in natura a compromessi nei computer dei clienti. Il Centro sicurezza può quindi rilevare attacchi contro le applicazioni Web a lungo, dopo essere stato sfruttato.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Rilevata chiamata del tema WordPress sospetta**|Il log attività del servizio app Azure indica una possibile attività di inserimento di codice nella risorsa del servizio app.<br/> Questa attività sospetta è simile all'attività che manipola un tema WordPress per supportare l'esecuzione sul lato server del codice, seguito da una richiesta Web diretta per richiamare il file di tema modificato. Questo tipo di attività è stato rilevato nel passato come parte di una campagna di attacco rispetto a WordPress.|
|**Connessione alla pagina Web da un indirizzo IP anomalo rilevato**|Il log attività del servizio app Azure indica una connessione a una pagina Web sensibile da un indirizzo di origine che non si è mai connesso prima. Questo potrebbe indicare che un utente sta tentando un attacco di forza bruta nelle pagine di amministrazione dell'app Web. Potrebbe anche essere il risultato di un nuovo indirizzo IP usato da un utente legittimo.|
|**Nell'Intelligence per le minacce è stato trovato un indirizzo IP connesso all'interfaccia FTP del servizio app Azure**|L'analisi dei log FTP del servizio app Azure ha rilevato una connessione da un indirizzo di origine trovato nel feed di intelligence per le minacce. Durante questa connessione, un utente ha eseguito l'accesso alle pagine elencate di seguito.|
|**Impronta digitale web rilevata**|Il log attività del servizio app Azure indica una possibile attività di impronta digitale Web nella risorsa del servizio app. <br/>Questa attività sospetta rilevata è associata a uno strumento denominato Elephant cieco. Lo strumento consente di eseguire l'impronta digitale dei server Web e tenta di rilevare le applicazioni installate e le relative versioni. Spesso gli utenti malintenzionati usano questo strumento per eseguire il probe delle applicazioni Web per individuare le vulnerabilità.|
|**È stato rilevato un accesso sospetto a una pagina Web potenzialmente vulnerabile**|Il log attività del servizio app Azure indica che è stato eseguito l'accesso a una pagina Web che sembra essere sensibile. <br/>Questa attività sospetta ha origine da un indirizzo di origine il cui modello di accesso è simile a quello di uno scanner Web. Questo tipo di attività è spesso associato a un tentativo da un utente malintenzionato di analizzare la rete per provare a ottenere l'accesso a pagine Web sensibili o vulnerabili.|
|**File PHP nella cartella di caricamento**|Il log attività del servizio app Azure indica che è stato eseguito l'accesso a una pagina PHP sospetta che si trova nella cartella di caricamento. <br/>Questo tipo di cartella non contiene in genere file PHP. L'esistenza di questo tipo di file potrebbe indicare un exploit che sfrutta le vulnerabilità di caricamento arbitrario dei file.|
|**Tentativo di eseguire i comandi di Linux in un servizio app di Windows**|L'analisi dei processi del servizio app ha rilevato un tentativo di esecuzione di un comando Linux in un servizio app di Windows. Questa azione è stata eseguita dall'applicazione Web. Questo comportamento è spesso visibile durante le campagne che sfruttano una vulnerabilità in un'applicazione Web comune.|
|**È stata rilevata un'esecuzione PHP sospetta**|I log del computer indicano un processo PHP sospetto in esecuzione. L'azione includeva un tentativo di eseguire comandi del sistema operativo o codice PHP dalla riga di comando usando il processo PHP. Sebbene questo comportamento possa essere legittimo, nelle applicazioni Web questo comportamento viene osservato anche in attività dannose, ad esempio i tentativi di infettare i siti Web con le shell Web.|
|**Elabora esecuzione da cartella temporanea**|L'analisi dei processi del servizio app ha rilevato un'esecuzione di un processo dalla cartella temporanea dell'app. Sebbene questo comportamento possa essere legittimo, nelle applicazioni Web questo comportamento viene osservato anche in attività dannose.|
|**Il tentativo di eseguire il comando con privilegi elevati è stato rilevato**|L'analisi dei processi del servizio app ha rilevato un tentativo di eseguire un comando che richiede privilegi elevati. Il comando è stato eseguito nel contesto dell'applicazione Web. Sebbene questo comportamento possa essere legittimo, nelle applicazioni Web questo comportamento viene osservato anche in attività dannose.|

> [!NOTE]
> Il rilevamento delle minacce del Centro sicurezza per il servizio app non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Per altre informazioni sugli avvisi di rilevamento delle minacce per il servizio app, vedere proteggere il servizio app con il Centro sicurezza di Azure e vedere come abilitare il monitoraggio e la protezione dei carichi di lavoro del servizio app.

## Contenitori<a name="azure-containers"></a>

Il Centro sicurezza fornisce il rilevamento delle minacce in tempo reale per i contenitori in computer Linux basati sul Framework controllato. Gli avvisi identificano diverse attività di Docker sospette, ad esempio la creazione di un contenitore con privilegi in un host, un'indicazione del server Secure Shell (SSH) in esecuzione all'interno di un contenitore Docker o l'uso dei minatori di crittografia. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori. Oltre ai rilevamenti di Linux, il Centro sicurezza offre anche analisi più specifiche per le distribuzioni di contenitori.
