---
title: Eseguire la migrazione dal servizio Riconoscimento vocale personalizzato al servizio Voce
titleSuffix: Azure Cognitive Services
description: Il servizio Riconoscimento vocale personalizzato fa ora parte del servizio di riconoscimento vocale. Passa al servizio riconoscimento vocale per trarre vantaggio dagli ultimi aggiornamenti qualitativi e delle funzionalità.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805927"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Eseguire la migrazione dal servizio Riconoscimento vocale personalizzato al servizio riconoscimento vocale

Usare questo articolo per eseguire la migrazione delle applicazioni dal servizio Riconoscimento vocale personalizzato al servizio di riconoscimento vocale.

Il servizio Riconoscimento vocale personalizzato fa ora parte del servizio di riconoscimento vocale. Passa al servizio riconoscimento vocale per trarre vantaggio dagli ultimi aggiornamenti qualitativi e delle funzionalità.

## <a name="migration-for-new-customers"></a>Migrazione per i nuovi clienti

Il modello di determinazione dei prezzi è più semplice, usando un modello di determinazione prezzi basato sull'ora per il servizio di riconoscimento vocale.  

1. Creare una risorsa di Azure in ogni area in cui l'applicazione è disponibile. Il nome della risorsa di Azure è **Voce**. È possibile usare una singola risorsa di Azure per i servizi seguenti nella stessa area, anziché creare risorse distinte:

    * Riconoscimento vocale
    * Riconoscimento vocale personalizzato
    * Sintesi vocale
    * Traduzione vocale

2. Scaricare [Speech SDK](speech-sdk.md).

3. Seguire le guide introduttive e gli esempi di SDK per usare le API corrette. Se si usano le API REST, è necessario anche usare le chiavi di risorsa e gli endpoint corretti.

4. Aggiornare l'applicazione client per usare le API e il servizio Voce.

## <a name="migration-for-existing-customers"></a>Migrazione per i clienti esistenti

Eseguire la migrazione delle chiavi di risorsa esistenti al servizio riconoscimento vocale nel portale del servizio di riconoscimento vocale. Seguire questa procedura:

> [!NOTE]
> La migrazione delle chiavi di risorsa può essere eseguita solo all'interno della stessa area.

1. Accedere al portale [cris.ai](https://cris.ai/Home/CustomSpeech) e selezionare la sottoscrizione nel menu in alto a destra.

2. Selezionare **Migrate selected subscription** (Migrazione sottoscrizione selezionata).

3. Immettere la chiave di sottoscrizione nella casella di testo e selezionare **Esegui la migrazione**.

## <a name="next-steps"></a>Passaggi successivi

* [Prova il servizio di sintesi vocale gratuitamente](get-started.md).
* Apprendere i concetti del [riconoscimento vocale](./speech-to-text.md).

## <a name="see-also"></a>Vedi anche

* [Informazioni sul servizio Voce](overview.md)
* [Documentazione per il servizio riconoscimento vocale e l'SDK vocale](speech-sdk.md#get-the-sdk)
