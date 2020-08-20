---
title: Quote e limiti di servizi vocali
titleSuffix: Azure Cognitive Services
description: Riferimento rapido, descrizione dettagliata e procedure consigliate per le quote e i limiti di servizi vocali cognitivi di Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: alexeyo
ms.openlocfilehash: 86182130daf5cbb61df7a751aaeff392dd608a67
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88662587"
---
# <a name="speech-services-quotas-and-limits"></a>Quote e limiti di servizi vocali

Questo articolo contiene un riferimento rapido e la **Descrizione dettagliata** delle quote e dei limiti di servizi vocali cognitivi di Azure per tutti i [piani tariffari](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Contiene anche alcune procedure consigliate per evitare la limitazione delle richieste. 

## <a name="quotas-and-limits-quick-reference"></a>Riferimento rapido a quote e limiti
Passa a [quote e limiti di sintesi vocale](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Quote e limiti di riconoscimento vocale per ogni risorsa vocale
Nella tabella seguente i parametri senza riga "regolabile" **non** sono regolabili per tutti i piani tariffari.

| Quota | Gratuito (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **Limite richieste simultanee trascrizioni online (modelli base e personalizzati)** |  |  |
| Valore predefinito | 1 | 20 |
| Regolabile | No<sup>2</sup> | Sì<sup>2</sup> |
| **Limite richieste API REST (endpoint di[gestione API](../../api-management/api-management-key-concepts.md) )** | 100 richieste per 10 secondi | 100 richieste per 10 secondi |
| **Dimensioni massime file di set di dati per l'importazione dati** | 2 GB | 2 GB |
| **Dimensioni massime del BLOB di input per la trascrizione batch** | N/D | 2,5 GB |
| **Dimensioni massime del contenitore BLOB per la trascrizione batch** | N/D | 5 GB |
| **Numero massimo di BLOB per contenitore per la trascrizione batch** | N/D | 10000 |
| **Numero massimo di processi in esecuzione simultanea per la trascrizione batch** | N/D | 2000  |

<sup>1</sup> per il piano tariffario **gratuito (F0)** , vedere anche le indennità mensile nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>2</sup> vedere [spiegazioni aggiuntive](#detailed-description-quota-adjustment-and-best-practices), [procedure consigliate](#general-best-practices-to-mitigate-throttling-during-autoscaling)e [istruzioni per la regolazione](#speech-to-text-increasing-online-transcription-concurrent-request-limit).<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Quote e limiti di sintesi vocale per ogni risorsa vocale
Nella tabella seguente i parametri senza riga "regolabile" **non** sono regolabili per tutti i piani tariffari.

| Quota | Gratuito (F0)<sup>3</sup> | Standard (S0) |
|--|--|--|
| **Numero massimo di transazioni al secondo (TPS) per le voci standard e neurali** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **Limite di richieste simultanee per la voce personalizzata** |  |  |
| Valore predefinito | 10 | 10 |
| Regolabile | N.<sup>5</sup> | Sì<sup>5</sup> |
| **Quote specifiche di HTTP** |  |
| Lunghezza massima audio prodotta per richiesta | 10 min | 10 min |
| Numero massimo di `<voice>` tag DISTINCT in SSML | 50 | 50 |
| **Quote specifiche di WebSocket** |  |  |
|Lunghezza massima audio prodotta per turno | 10 min | 10 min |
|Dimensioni massime del messaggio SSML per turno |64 KB |64 KB |
| **Limite richieste API REST** | 20 richieste al minuto | 25 richieste per 5 secondi |


<sup>3</sup> per il piano tariffario **gratuito (F0)** , vedere anche le indennità mensile nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>4</sup> vedere [spiegazioni aggiuntive](#detailed-description-quota-adjustment-and-best-practices) e [procedure consigliate](#general-best-practices-to-mitigate-throttling-during-autoscaling).<br/>
<sup>5</sup> vedere [spiegazioni aggiuntive](#detailed-description-quota-adjustment-and-best-practices), [procedure consigliate](#general-best-practices-to-mitigate-throttling-during-autoscaling)e [istruzioni per la regolazione](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice).<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Descrizione dettagliata, regolazione della quota e procedure consigliate
Prima di richiedere un aumento della quota (se applicabile), verificare che sia necessario. Il servizio di riconoscimento vocale usa le tecnologie di scalabilità automatica per portare le risorse di calcolo necessarie in modalità "on demand" e, allo stesso tempo, per mantenere i costi dei clienti Bassi, non mantenendo una quantità eccessiva di capacità hardware. Ogni volta che l'applicazione riceve un codice di risposta 429 ("troppe richieste") mentre il carico di lavoro è entro i limiti definiti (vedere [quote e limiti riferimento rapido](#quotas-and-limits-quick-reference)) la spiegazione più probabile è che il servizio aumenti fino alla domanda e non ha ancora raggiunto la scala necessaria, quindi non dispone immediatamente di risorse sufficienti per soddisfare la richiesta. Questo stato è in genere temporaneo e non deve durare a lungo.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Procedure consigliate generali per attenuare la limitazione delle richieste durante la scalabilità automatica
Per ridurre al minimo i problemi relativi alla limitazione (codice di risposta 429), è consigliabile usare le tecniche seguenti:
- Implementare la logica di ripetizione tentativi nell'applicazione
- Evitare le modifiche apportate al carico di lavoro. Aumentare gradualmente il carico di lavoro <br/>
*Esempio.* L'applicazione usa la sintesi vocale e il carico di lavoro corrente è 5 TPS (transazioni al secondo). Il secondo successivo si aumenta il carico a 20 TPS (quattro volte più). Il servizio inizia immediatamente a eseguire la scalabilità verticale per soddisfare il nuovo carico, ma probabilmente non sarà in grado di eseguire questa operazione entro un secondo, quindi alcune richieste otterranno il codice di risposta 429.   
- Testare modelli di aumento del carico diversi
  - Vedere l' [esempio di riconoscimento vocale](#speech-to-text-example-of-a-workload-pattern-best-practice)
- Creare risorse vocali aggiuntive nella stessa area o in aree diverse e distribuire il carico di lavoro tra di essi usando la tecnica "Round Robin". Questo è particolarmente importante per il parametro **di sintesi vocale (transazioni al secondo)** che viene impostato come 200 per ogni risorsa vocale e che non può essere modificato  

Nelle sezioni successive vengono descritti casi specifici di regolazione delle quote.<br/>
Passa a sintesi [vocale. Aumento del limite di richieste simultanee di trascrizione per la voce personalizzata](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>Riconoscimento vocale: aumento del limite di richieste simultanee di trascrizione online
Per impostazione predefinita, il numero di richieste simultanee è limitato a 20 per ogni risorsa vocale (modello di base) o per endpoint personalizzato (modello personalizzato). Per il piano tariffario standard questa quantità può essere aumentata. Prima di inviare la richiesta, assicurarsi di avere acquisito familiarità con il materiale contenuto in [questa sezione](#detailed-description-quota-adjustment-and-best-practices) e con la conoscenza di queste [procedure consigliate](#general-best-practices-to-mitigate-throttling-during-autoscaling).

L'aumento del limite di richieste simultanee **non** influisce direttamente sui costi. Servizi vocali usa il modello "pagamento solo per le risorse usate". Il limite definisce il livello di scalabilità del servizio prima che venga avviata la limitazione delle richieste.

I limiti delle richieste simultanee per i modelli di **base** e **personalizzati** devono essere modificati **separatamente**.

Il valore esistente del parametro del limite della richiesta simultanea **non** è visibile tramite portale di Azure, gli strumenti da riga di comando o le richieste API. Per verificare il valore esistente, creare una richiesta di supporto di Azure.

>[!NOTE]
>I [contenitori di sintesi vocale](speech-container-howto.md) non richiedono aumenti di limite di richieste simultanee, in quanto i contenitori sono limitati solo dalle CPU dell'hardware in cui sono ospitati.

#### <a name="have-the-required-information-ready"></a>Preparare le informazioni necessarie:
- Per il **modello di base**:
  - ID risorsa vocale
  - Region
- Per il **modello personalizzato**: 
  - Region
  - ID endpoint personalizzato

- **Come ottenere informazioni (modello di base)**:  
  - Vai a [portale di Azure](https://portal.azure.com/)
  - Selezionare la risorsa vocale per cui si vuole aumentare il limite della richiesta di concorrenza
  - Selezione *Proprietà* (gruppo di*Gestione risorse* ) 
  - Copiare e salvare i valori dei campi seguenti:
    - **ID risorsa**
    - **Località** (area dell'endpoint)

- **Come ottenere informazioni (modello personalizzato)**:
  - Vai al portale di [speech studio](https://speech.microsoft.com/)
  - Accedi se necessario
  - Vai a Riconoscimento vocale personalizzato
  - Selezionare il progetto
  - Vai a *distribuzione*
  - Selezionare l'endpoint obbligatorio
  - Copiare e salvare i valori dei campi seguenti:
    - **Area servizio** (area dell'endpoint)
    - **ID endpoint**
  
#### <a name="create-and-submit-support-request"></a>Creare e inviare una richiesta di supporto
Avviare l'aumento del limite di richieste simultanee per la risorsa o, se necessario, verificare il limite odierno inviando la richiesta di supporto:

- Assicurarsi di disporre delle [informazioni necessarie](#have-the-required-information-ready)
- Vai a [portale di Azure](https://portal.azure.com/)
- Selezionare la risorsa vocale per cui si vuole aumentare o controllare il limite di richieste di concorrenza
- Selezionare una *nuova richiesta di supporto* (supporto e gruppo di*risoluzione dei problemi* ) 
- Verrà visualizzata una nuova finestra con informazioni popolate automaticamente sulla sottoscrizione di Azure e sulla risorsa di Azure
- Immettere il *Riepilogo* (ad esempio "aumenta il limite della richiesta di concorrenza STT")
- In *tipo di problema* selezionare "problemi di quota o sottoscrizione"
- Nel *sottotipo di problema* apparso selezionare:
  - "Aumento della quota o delle richieste simultanee"-per una richiesta di aumento
  - "Convalida della quota o dell'utilizzo" per verificare il limite esistente
- Fare clic su *Avanti: soluzioni*
- Continua con la creazione della richiesta
- Quando in campi *Dettagli* immettere nel campo *Descrizione* :
  - Si noti che la richiesta è relativa **alla quota vocale-testo**
  - Modello di **base** o **personalizzato**
  - Informazioni sulle risorse di Azure [raccolte prima](#have-the-required-information-ready) 
  - Completare l'immissione delle informazioni necessarie e fare clic sul pulsante *Crea* nella scheda *Verifica + crea*
  - Prendere nota del numero di richiesta di supporto nelle notifiche portale di Azure. Si verrà contattati a breve per un'ulteriore elaborazione

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>Riconoscimento vocale: esempio di modello di carico di lavoro procedura consigliata
Questo esempio illustra l'approccio consigliato in seguito per attenuare la possibile limitazione delle richieste a causa della [scalabilità automatica in corso](#detailed-description-quota-adjustment-and-best-practices). Non si tratta di una "ricetta esatta", ma solo di un modello che si invita a seguire e modificare in modo necessario.

Supponiamo che una risorsa vocale abbia il limite di richieste simultanee impostato su 300. Avviare il carico di lavoro da 20 connessioni simultanee e aumentare il carico di 20 connessioni simultanee ogni 1,5-2 minuti. Controllare le risposte del servizio e implementare la logica che esegue il fallback (riduce il carico) se si ottengono troppi codici di risposta 429. Quindi riprovare in un modello di 1-2-4-4 minuti. Ovvero ritentare l'aumento del carico in 1 minuto, se ancora non funziona, quindi in 2 min e così via.

In genere, è consigliabile testare il carico di lavoro e i modelli di carico di lavoro prima di passare alla produzione.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>Sintesi vocale: aumento del limite di richieste simultanee di trascrizione per la voce personalizzata
Per impostazione predefinita, il numero di richieste simultanee per un endpoint Voice personalizzato è limitato a 10. Per il piano tariffario standard questa quantità può essere aumentata. Prima di inviare la richiesta, assicurarsi di avere acquisito familiarità con il materiale contenuto in [questa sezione](#detailed-description-quota-adjustment-and-best-practices) e con la conoscenza di queste [procedure consigliate](#general-best-practices-to-mitigate-throttling-during-autoscaling).

L'aumento del limite di richieste simultanee **non** influisce direttamente sui costi. Servizi vocali usa il modello "pagamento solo per le risorse usate". Il limite definisce il livello di scalabilità del servizio prima che venga avviata la limitazione delle richieste.

Il valore esistente del parametro del limite della richiesta simultanea **non** è visibile tramite portale di Azure, gli strumenti da riga di comando o le richieste API. Per verificare il valore esistente, creare una richiesta di supporto di Azure.

>[!NOTE]
>I [contenitori di sintesi vocale](speech-container-howto.md) non richiedono aumenti di limite di richieste simultanee, in quanto i contenitori sono limitati solo dalle CPU dell'hardware in cui sono ospitati.

#### <a name="prepare-the-required-information"></a>Preparare le informazioni necessarie:
Per creare una richiesta di aumento, è necessario specificare l'area di distribuzione e l'ID dell'endpoint personalizzato. Per ottenerlo, eseguire le azioni seguenti: 

- Vai al portale di [speech studio](https://speech.microsoft.com/)
- Accedi se necessario
- Passa a *voce personalizzata*
- Selezionare il progetto
- Vai a *distribuzione*
- Selezionare l'endpoint obbligatorio
- Copiare e salvare i valori dei campi seguenti:
    - **Area servizio** (area dell'endpoint)
    - **ID endpoint**
  
#### <a name="create-and-submit-support-request"></a>Creare e inviare una richiesta di supporto
Avviare l'aumento del limite di richieste simultanee per la risorsa o, se necessario, verificare il limite odierno inviando la richiesta di supporto:

- Assicurarsi di disporre delle [informazioni necessarie](#prepare-the-required-information)
- Vai a [portale di Azure](https://portal.azure.com/)
- Selezionare la risorsa vocale per cui si vuole aumentare o controllare il limite di richieste di concorrenza
- Selezionare una *nuova richiesta di supporto* (supporto e gruppo di*risoluzione dei problemi* ) 
- Verrà visualizzata una nuova finestra con informazioni popolate automaticamente sulla sottoscrizione di Azure e sulla risorsa di Azure
- Immettere il *Riepilogo* (ad esempio "aumenta il limite di richieste di concorrenza per l'endpoint personalizzato")
- In *tipo di problema* selezionare "problemi di quota o sottoscrizione"
- Nel *sottotipo di problema* apparso selezionare:
  - "Aumento della quota o delle richieste simultanee"-per una richiesta di aumento
  - "Convalida della quota o dell'utilizzo" per verificare il limite esistente
- Fare clic su *Avanti: soluzioni*
- Continua con la creazione della richiesta
- Quando in campi *Dettagli* immettere nel campo *Descrizione* :
  - Si noti che la richiesta è relativa **alla quota di sintesi vocale**
  - Informazioni sulle risorse di Azure [raccolte prima](#prepare-the-required-information) 
  - Completare l'immissione delle informazioni necessarie e fare clic sul pulsante *Crea* nella scheda *Verifica + crea*
  - Prendere nota del numero di richiesta di supporto nelle notifiche portale di Azure. Si verrà contattati a breve per un'ulteriore elaborazione

