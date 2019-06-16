---
title: Domande frequenti sul servizio Sintesi vocale in Azure
titleSuffix: Azure Cognitive Services
description: Ottenere risposte alle domande più frequenti sul servizio Sintesi vocale.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: fd8362748c39389139e8384d0bad7e84d20128a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66751618"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Domande frequenti su Sintesi vocale

Se in questo documento non sono presenti risposte alle domande di proprio interesse, vedere le [altre opzioni di supporto](support.md).

## <a name="general"></a>Generale

**D: qual è la differenza tra un modello di voce standard e un modello di voce personalizzata?**

**R**: il modello di voce standard, definito anche *carattere voce*, è stato sottoposto a training con l'uso di dati di proprietà di Microsoft ed è già distribuito nel cloud. È possibile usare un modello di voce personalizzata per adattare un modello medio e trasferire il timbro e l'espressione dello stile di voce del parlante. In alternativa, è possibile eseguire il training di un modello completamente nuovo, basato sui dati di training preparati dall'utente. Oggi, sempre più clienti vogliono una voce personalizzata per i propri bot. La piattaforma per la creazione di voci personalizzate è la soluzione ideale per rispondere a questa esigenza.

**D: da dove è necessario iniziare per usare un modello di voce standard?**

**R**: attraverso richieste HTTP sono disponibili più di 80 modelli di voce standard in oltre 45 lingue. È prima necessario ottenere una [chiave di sottoscrizione](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Per eseguire chiamate REST ai modelli di voce già distribuiti, vedere l'[API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**D: per usare un modello di voce personalizzata, l'API è la stessa che viene usata per le voci standard?**

**R**: dopo aver creato e distribuito un modello di voce personalizzata, si riceve un endpoint univoco per tale modello. Per usare la voce per parlare nelle app, è necessario specificare l'endpoint nelle richieste HTTP. Le stesse funzionalità presenti nell'API REST per il servizio Sintesi vocale sono disponibili anche per l'endpoint personalizzato. Vedere la procedura per [creare e usare l'endpoint personalizzato](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint).

**D: è necessario preparare i dati di training personalmente per creare modelli di voce personalizzata?**

**R**: sì, per un modello di voce personalizzata è necessario preparare i dati di training personalmente.

Per creare un modello di voce personalizzato è necessaria una raccolta di dati vocali. Questa raccolta è costituita da un set di file audio con registrazioni vocali e da un file di testo con la trascrizione di ogni file audio. Il risultato della voce digitale dipende principalmente dalla qualità dei dati di training. Per produrre una buona sintesi vocale, è importante che le registrazioni vengano eseguite in un ambiente silenzioso con un microfono a stelo di alta qualità. Volume, velocità di pronuncia e tono uniformi e perfino l'uniformità dei manierismi espressivi sono elementi essenziali per la creazione di una voce digitale di buona qualità. È consigliabile registrare le voci in uno studio di registrazione.

Attualmente non viene fornito supporto per la registrazione online né sono disponibili raccomandazioni per gli studi di registrazione. Per i requisiti di formato, vedere [come preparare le registrazioni e le trascrizioni](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**D: quali script è necessario usare per la registrazione dei dati vocali per il training della voce personalizzata?**

**R**: non esistono limiti per gli script della registrazione vocale. È possibile usare i propri script per registrare la voce. Verificare semplicemente di avere a disposizione una copertura fonetica sufficiente nei dati vocali. Per eseguire il training di una voce personalizzata, è possibile iniziare con un volume ridotto di dati vocali, che può essere costituito da 50 frasi diverse (circa 3-5 minuti di parlato). Più dati si forniscono, più naturale risulterà la voce. È possibile iniziare il training di un carattere voce completo quando si forniscono le registrazioni di più di 2000 frasi (circa 3-4 ore di parlato). Per ottenere una voce di qualità elevata, è necessario preparare le registrazioni di oltre 6000 frasi (circa 8-10 ore di parlato).

Offriamo servizi aggiuntivi per agevolare la preparazione degli script per la registrazione. Contattare l'[assistenza clienti per la voce personalizzata](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) per eventuali domande.

**D: cosa è necessario fare se serve una concorrenza più elevata rispetto al valore predefinito o all'offerta presente nel portale?**

**R**: è possibile aumentare le prestazioni del modello in incrementi di 20 richieste simultanee. Contattare l'[assistenza clienti per la voce personalizzata](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) per eventuali domande su un maggiore ridimensionamento.

**D: è possibile scaricare il modello ed eseguirlo in locale?**

**R**: non è possibile scaricare i modelli ed eseguirli in locale.

**D: le richieste sono limitate?**

**R**: l'API REST limita le richieste a 25 ogni 5 secondi. Informazioni dettagliate sono disponibili nelle pagine relative a [Sintesi vocale](text-to-speech.md). 

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi](troubleshooting.md)
* [Note sulla versione](releasenotes.md)
