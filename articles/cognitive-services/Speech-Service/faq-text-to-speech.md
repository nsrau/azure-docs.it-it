---
title: Domande frequenti sul servizio di riconoscimento vocale in Azure | Microsoft Docs
description: Di seguito sono riportate le risposte alle domande più frequenti sul riconoscimento vocale.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 4a29435c0ace79fc3a5d3a5a42a0e91bdbc8da5e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082825"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Domande frequenti sulla sintesi vocale

Se le risposte alle proprie domande non sono presenti qui, provare a rivolgersi alla community del Servizio di riconoscimento vocale personalizzato in [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) e [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Generale

**Domanda**: Qual è la differenza tra i modelli di voce standard e personalizzati?

**Risposta**: i modelli di voce standard (noti anche come caratteri voce) sono stati sottoposti a training con dati di proprietà di Microsoft e sono già stati distribuiti nel cloud. I modelli di voce personalizzati consentono all'utente di adattare un modello medio e trasferire il timbro e la modalità di espressione in base allo stile di voce di chi parla o di eseguire il training di un modello completamente nuovo basato sui dati di training preparati dall'utente. Oggi sempre più clienti vogliono avere una voce personalizzata per i propri bot. La piattaforma di creazione delle voci personalizzate è la scelta giusta per tale esigenza.

**Domanda**: Da dove è necessario iniziare per usare un modello di voce standard?

**Risposta**: Attraverso le richieste HTTP sono disponibili più di 80 modelli di voce in oltre 45 lingue. Prima di tutto è necessario ottenere una [chiave di sottoscrizione](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Per effettuare chiamate REST ai modelli di voce predistribuiti, vedere i [dettagli qui](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Domanda**: Per usare un modello di voce personalizzato l'API è la stessa dei modelli di voce standard?

**Risposta**: Dopo aver creato e distribuito il modello di voce personalizzato, si otterrà un endpoint univoco per il modello. È necessario specificare l'endpoint nelle richieste HTTP per la voce da usare nelle app. Le stesse funzionalità disponibili tramite l'API REST per il servizio di sintesi vocale sono disponibili anche per l'endpoint personalizzato. Vedere la procedura per [creare e usare l'endpoint personalizzato](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Domanda**: È necessario preparare personalmente i dati di training per la creazione di modelli di voce personalizzati?

**Risposta**: È necessario preparare i propri dati di training. Per creare un modello di voce personalizzato è necessaria una raccolta di dati vocali. Questa raccolta è costituita da un set di file audio con registrazioni vocali e da un file di testo con le trascrizioni di ciascun file audio. Il risultato della voce digitale si basa principalmente sulla qualità dei dati di training. Per produrre una buona sintesi vocale è importante che le registrazioni vengano eseguite in un ambiente silenzioso con un microfono di alta qualità. Volume uniforme, velocità, tono e manierismi espressivi e anche la coerenza sono elementi essenziali per la creazione di una voce digitale di buona qualità. Si consiglia di registrare le voci in uno studio di registrazione.
Al momento non viene fornito supporto per la registrazione online né sono disponibili consigli relativi agli studi di registrazione. Per i requisiti di formato, vedere [come preparare le registrazioni e le trascrizioni](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Domanda**: Quali script si devono usare per la registrazione dei dati vocali per il training della voce personalizzata? 

**Risposta**: Non ci sono limiti per gli script per la registrazione vocale. È possibile usare i propri script per registrare la voce. Semplicemente assicurarsi di che avere sufficiente copertura fonetica nei dati vocali. Per eseguire il training di una voce personalizzata, è possibile iniziare con un volume ridotto di dati vocali, che può essere 50 frasi diverse (circa 3-5 minuti di voce). Più dati si forniscono, più naturale risulterà la voce. È possibile iniziare a eseguire il training di un carattere voce completo quando si forniscono le registrazioni di più di 2000 frasi (circa 3-4 ore di voce). Per ottenere una voce di qualità elevata, è necessario preparare le registrazioni di oltre 6000 frasi (circa 8-10 ore di voce).  
Offriamo servizi aggiuntivi per agevolare la preparazione degli script per la registrazione. Contattare l'[assistenza clienti per la voce personalizzata](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) per eventuali domande.

**Domanda**: Che cosa è necessario fare se serve una concorrenza più elevata rispetto al valore predefinito o a ciò che viene offerto nel portale?

**Risposta**: È possibile aumentare il modello in incrementi di 20 richieste simultanee. Contattare l'[assistenza clienti per la voce personalizzata](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) per eventuali domande su un maggiore ridimensionamento.

**Domanda**: È possibile scaricare il modello ed eseguirlo in locale?

**Risposta**: Non è possibile scaricare i modelli ed eseguirli in locale.

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi](troubleshooting.md)
* [Note sulla versione](releasenotes.md)
