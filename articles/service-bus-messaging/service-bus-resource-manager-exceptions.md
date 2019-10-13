---
title: Eccezioni Gestione risorse del bus di servizio di Azure | Microsoft Docs
description: Elenco di eccezioni del bus di servizio emerse da Azure Resource Manager e le azioni consigliate.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: 9a2d25aba03156d6d14fe5ef9aa58b3748033b85
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296378"
---
# <a name="service-bus-resource-manager-exceptions"></a>Eccezioni Gestione risorse del bus di servizio

Questo articolo elenca le eccezioni generate durante l'interazione con il bus di servizio di Azure usando modelli di Azure Resource Manager tramite o chiamate dirette.

> [!IMPORTANT]
> Questo documento viene aggiornato di frequente. Verificare la disponibilità di aggiornamenti.

Di seguito sono riportate le varie eccezioni/errori che vengono rilevati tramite la Azure Resource Manager.

## <a name="error-bad-request"></a>Errore: Bad Request

"Richiesta non valida" indica che la richiesta ricevuta dalla Gestione risorse non è riuscita.

| Codice di errore | Codice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Bad Request | 40000 | Sottocodice = 40000. Impossibile impostare la proprietà *' Property Name '* durante la creazione di una coda perché lo spazio dei nomi *' namespace name '* utilizza il livello ' Basic '. Questa operazione è supportata solo nel livello ' standard ' o ' Premium '. | Nel livello Basic del bus di servizio di Azure non è possibile impostare o aggiornare le proprietà seguenti: <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Argomenti </li> </ul> | Per usare questa funzionalità, provare a eseguire l'aggiornamento dal livello Basic al livello standard o Premium. |
| Bad Request | 40000 | Sottocodice = 40000. Impossibile modificare il valore della proprietà' requiresDuplicateDetection ' di una coda o di un argomento esistente. | Il rilevamento dei duplicati deve essere abilitato o disabilitato al momento della creazione dell'entità. Il parametro di configurazione del rilevamento duplicato non può essere modificato dopo la creazione. | Per abilitare il rilevamento dei duplicati in una coda o un argomento creato in precedenza, è possibile creare una nuova coda o un nuovo argomento con rilevamento duplicato e quindi procedere dalla coda originale alla nuova coda/argomento. |
| Bad Request | 40000 | Sottocodice = 40000. Il valore 16384 specificato non è valido. La proprietà' MaxSizeInMegabytes ' deve essere uno dei valori seguenti: 1024; 2048; 3072; 4096; 5120. | Il valore MaxSizeInMegabytes non è valido. | Verificare che MaxSizeInMegabytes sia uno dei seguenti: 1024, 2048, 3072, 4096, 5120. |
| Bad Request | 40000 | Sottocodice = 40000. Non è possibile modificare il partizionamento per la coda o l'argomento. | Non è possibile modificare il partizionamento per l'entità. | Creare una nuova entità (coda o argomento) e abilitare le partizioni. | 
| Bad Request | Nessuno | Lo spazio dei nomi *' namespace name '* non esiste. | Lo spazio dei nomi non esiste nella sottoscrizione di Azure. | Per correggere l'errore, provare il seguente <ul> <li> Assicurarsi che la sottoscrizione di Azure sia corretta. </li> <li> Verificare che lo spazio dei nomi esista. </li> <li> Verificare che il nome dello spazio dei nomi sia corretto (nessun errore di ortografia o stringa null). </li> </ul> | 
| Bad Request | 40400 | Sottocodice = 40400. L'entità di destinazione dell'invio automatico non esiste. | La destinazione per l'entità di destinazione di autoinoltring non esiste. | L'entità di destinazione (coda o argomento) deve esistere prima della creazione dell'origine. Riprovare dopo aver creato l'entità di destinazione. |
| Bad Request | 40000 | Sottocodice = 40000. Il tempo di blocco specificato supera il limite massimo consentito di "5" minuti. | Il tempo per il quale un messaggio può essere bloccato deve essere compreso tra 1 minuto (minimo) e 5 minuti (massimo). | Verificare che il tempo di blocco specificato sia compreso tra 1 min e 5 minuti. |
| Bad Request | 40000 | Sottocodice = 40000. Non è possibile abilitare contemporaneamente entrambe le proprietà DelayedPersistence e RequiresDuplicateDetection. | Le entità con rilevamento dei duplicati abilitato devono essere permanenti, quindi la persistenza non può essere posticipata. | Altre informazioni sul [rilevamento dei duplicati](duplicate-detection.md) |
| Bad Request | 40000 | Sottocodice = 40000. Impossibile modificare il valore della proprietà RequiresSession di una coda esistente. | Il supporto per le sessioni deve essere abilitato al momento della creazione dell'entità. Una volta creato, non è possibile abilitare o disabilitare le sessioni in un'entità esistente (coda o sottoscrizione) | Eliminare e ricreare una nuova coda (o sottoscrizione) con la proprietà "RequiresSession" abilitata. |
| Bad Request | 40000 | Sottocodice = 40000. ' URI_PATH ' contiene caratteri non consentiti dal bus di servizio. I segmenti di entità possono contenere solo lettere, numeri, punti (.), trattini (-) e caratteri di sottolineatura (_). | I segmenti di entità possono contenere solo lettere, numeri, punti (.), trattini (-) e caratteri di sottolineatura (_). Qualsiasi altro carattere comporta l'esito negativo della richiesta. | Verificare che non siano presenti caratteri non validi nel percorso URI. |


## <a name="error-code-429"></a>Codice errore: 429

Analogamente a HTTP, "codice errore 429" indica "numero eccessivo di richieste". Implica che la risorsa specifica (spazio dei nomi) viene limitata a causa di un numero eccessivo di richieste (o a causa di operazioni in conflitto) su tale risorsa.

| Codice di errore | Codice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Sottocodice = 50004. La richiesta è stata terminata perché *è in* corso la limitazione dello spazio dei nomi. | Questa condizione di errore viene raggiunta quando il numero di richieste in ingresso supera il limite della risorsa. | Attendere alcuni secondi e riprovare. <br/> <br/> Altre informazioni sulle [quote](service-bus-quotas.md) e sui [limiti delle richieste di Azure Resource Manager](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Sottocodice = 40901. È in corso un'altra operazione in conflitto. | È in corso un'altra operazione in conflitto sulla stessa risorsa/entità | Attendere il completamento dell'operazione corrente in corso prima di riprovare. |
| 429 | 40900 | Sottocodice = 40900. Conflitto. Si sta richiedendo un'operazione non consentita nello stato corrente della risorsa. | Questa condizione può essere raggiunta quando vengono effettuate più richieste per eseguire le operazioni sulla stessa entità (coda, argomento, sottoscrizione o regola) nello stesso momento. | Attendere alcuni secondi e riprovare |
| 429 | 40901 | Richiesta nell'entità *' nome entità'* in conflitto con un'altra richiesta | È in corso un'altra operazione in conflitto sulla stessa risorsa/entità | Attendere il completamento dell'operazione precedente prima di riprovare |
| 429 | 40901 | È in corso un'altra richiesta di aggiornamento per l'entità *"nome entità"* . | È in corso un'altra operazione in conflitto sulla stessa risorsa/entità | Attendere il completamento dell'operazione precedente prima di riprovare |
| 429 | Nessuno | Si è verificato un conflitto di risorse. È possibile che sia in corso un'altra operazione in conflitto. Se si tenta di eseguire un'operazione non riuscita, la pulitura in background è ancora in sospeso. Riprovare. | Questa condizione può essere raggiunta quando si verifica un'operazione in sospeso sulla stessa entità. | Attendere il completamento dell'operazione precedente prima di riprovare. |


## <a name="error-code-not-found"></a>Codice errore: Non trovato

Questa classe di errori indica che la risorsa non è stata trovata.

| Codice di errore | Codice di errore | Messaggio di errore | Descrizione | Recommendation |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Non trovato | Nessuno | L'entità *' nome entità'* non è stata trovata. | Entità sulla quale l'operazione non è stata trovata. | Verificare che l'entità esista, quindi riprovare. |
| Non trovato | Nessuno | Non trovato. L'operazione non esiste. | L'operazione che si sta tentando di eseguire non esiste. | Controllare l'operazione e riprovare. |
| Non trovato | Nessuno | La richiesta in ingresso non è riconosciuta come richiesta PUT del criterio dello spazio dei nomi. | Il corpo della richiesta in ingresso è null e pertanto non può essere eseguito come richiesta PUT. | Verificare il corpo della richiesta per assicurarsi che non sia null. | 
| Non trovato | Nessuno | Impossibile trovare l'entità di messaggistica *"nome entità"* . | Impossibile trovare l'entità su cui si sta tentando di eseguire l'operazione. | Verificare se l'entità esiste, quindi riprovare. |
