---
title: Contatori e quote del Servizio di riconoscimento vocale personalizzato in Azure | Microsoft Docs
description: Informazioni sui contatori e le quote del Servizio di riconoscimento vocale personalizzato in Azure.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: eb5a9e4a3a27a2a8c044749b8b4df0f198583bde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223664"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Contatori e quote del Servizio di riconoscimento vocale personalizzato

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Il Servizio di riconoscimento vocale personalizzato è un servizio basato sul cloud che consente agli utenti di personalizzare i modelli conversione voce/testo per la trascrizione della voce in testo scritto.

Per iniziare a usare il Servizio di riconoscimento vocale personalizzato, passare al [portale di Servizi di riconoscimento vocale personalizzato](https://cris.ai).

Per i contatori per la determinazione dei prezzi correnti, vedere la pagina [Prezzi di Servizi cognitivi - Servizio riconoscimento vocale personalizzato](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/).

## <a name="tiers-explained"></a>Spiegazione dei livelli
Il livello gratuito (F0) è consigliato solo a scopo di testing e come prototipo. Per i sistemi di produzione è consigliabile usare il livello S2. Questo livello consente di scalare la distribuzione in base al numero di unità di scala richieste dallo specifico scenario.

> [!NOTE]
> La migrazione tra il livello F0 e il livello S2 *non* è consentita.
>

## <a name="meters-explained"></a>Spiegazione dei contatori

### <a name="scale-out"></a>Scalabilità orizzontale
Scalabilità orizzontale è una nuova funzionalità rilasciata con il nuovo modello di prezzi. Consente di controllare il numero di richieste simultanee che possono essere elaborate dal modello.

È possibile impostare le richieste simultanee usando la misura Unità di scala nella visualizzazione **Create Model Deployment** (Crea distribuzione modello). Per altre informazioni, vedere [Creare un endpoint personalizzato per il riconoscimento vocale](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md). A seconda della quantità di traffico che si prevede venga utilizzata dal modello, è possibile scegliere un numero appropriato di unità di scala. 

> [!NOTE]
> Ogni unità di scala garantisce 5 richieste simultanee. È possibile acquistare 1 o più unità di scala, in base alle esigenze. Poiché il numero di unità di scala aumenta in incrementi di 1, è garantito che il numero di richieste simultanee aumenti in incrementi di 5.
>

### <a name="log-management"></a>Gestione log
È possibile scegliere di disattivare le tracce audio per un modello appena distribuito a un costo aggiuntivo. Il Servizio di riconoscimento vocale personalizzato non registra le richieste audio o le trascrizioni di tale modello.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come usare il Servizio di riconoscimento vocale personalizzato, passare al [portale del Servizio di riconoscimento vocale personalizzato](https://cris.ai).

* [Introduzione](cognitive-services-custom-speech-get-started.md)
* [Domande frequenti](cognitive-services-custom-speech-faq.md)
* [Glossario](cognitive-services-custom-speech-glossary.md)
 
