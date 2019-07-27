---
title: Eseguire la migrazione dal servizio Riconoscimento vocale personalizzato al servizio Voce
titleSuffix: Azure Cognitive Services
description: Il servizio Riconoscimento vocale personalizzato fa ora parte del servizio Voce. Passare al servizio Voce per trarre vantaggio dagli ultimi aggiornamenti in termini di qualità e funzionalità.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 01b853c59723a8ed79cb32b0ee9c245c9c3ffb3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562762"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Eseguire la migrazione dal Servizio di riconoscimento vocale personalizzato al servizio Voce

Usare le informazioni in questo articolo per eseguire la migrazione dal Servizio di riconoscimento vocale personalizzato al servizio Voce.

Il servizio Riconoscimento vocale personalizzato fa ora parte del servizio Voce. Passa ai servizi di riconoscimento vocale per trarre vantaggio dagli ultimi aggiornamenti qualitativi e delle funzionalità.

## <a name="migration-for-new-customers"></a>Migrazione per i nuovi clienti

Il modello di determinazione dei prezzi del servizio Voce è più semplice e definito su base oraria.  

1. Creare una risorsa di Azure in ogni area in cui l'applicazione è disponibile. Il nome della risorsa di Azure è **Voce**. È possibile usare una singola risorsa di Azure per i servizi seguenti nella stessa area, anziché creare risorse distinte:

    * Riconoscimento vocale
    * Riconoscimento vocale personalizzato
    * Sintesi vocale
    * Traduzione vocale

2. Scaricare [Speech SDK](speech-sdk.md).

3. Seguire le guide introduttive e gli esempi di SDK per usare le API corrette. Se si usano le API REST, è necessario anche usare le chiavi di risorsa e gli endpoint corretti.

4. Aggiornare l'applicazione client per l'uso delle API e dei servizi di riconoscimento vocale.

## <a name="migration-for-existing-customers"></a>Migrazione per i clienti esistenti

Eseguire la migrazione delle chiavi di risorsa esistenti ai servizi di riconoscimento vocale nel portale di servizi vocali. Seguire questa procedura:

> [!NOTE]
> La migrazione delle chiavi di risorsa può essere eseguita solo all'interno della stessa area.

1. Accedere al portale [cris.ai](https://cris.ai/Home/CustomSpeech) e selezionare la sottoscrizione nel menu in alto a destra.

2. Selezionare **Migrate selected subscription** (Migrazione sottoscrizione selezionata).

3. Immettere la chiave di sottoscrizione nella casella di testo e selezionare **Esegui la migrazione**.

## <a name="next-steps"></a>Passaggi successivi

* [Prova gratuitamente i servizi vocali](get-started.md).
* Apprendere i concetti del [riconoscimento vocale](./speech-to-text.md).

## <a name="see-also"></a>Vedere anche

* [Informazioni sul servizio Voce](overview.md)
* [Documentazione di Speech Services e Speech SDK](speech-sdk.md#get-the-sdk)
