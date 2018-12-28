---
title: Eseguire la migrazione dal servizio Riconoscimento vocale personalizzato ai servizi Voce
titlesuffix: Azure Cognitive Services
description: Il servizio Riconoscimento vocale personalizzato fa ora parte del servizio Voce. Passare al servizio Voce per trarre vantaggio dagli ultimi aggiornamenti in termini di qualità e funzionalità.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b5879fabba70308c33101699dae4443e6b1b7070
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084533"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Eseguire la migrazione dal Servizio di riconoscimento vocale personalizzato al servizio Voce

Usare le informazioni in questo articolo per eseguire la migrazione dal Servizio di riconoscimento vocale personalizzato al servizio Voce.

Il servizio Riconoscimento vocale personalizzato fa ora parte del servizio Voce. Passare al servizio Voce per trarre vantaggio dagli ultimi aggiornamenti in termini di qualità e funzionalità.

## <a name="migration-for-new-customers"></a>Migrazione per i nuovi clienti

Il modello di determinazione dei prezzi del servizio Voce è più semplice e definito su base oraria.  

1. Creare una risorsa di Azure in ogni area in cui l'applicazione è disponibile. Il nome della risorsa di Azure è **Voce**. È possibile usare una singola risorsa di Azure per i servizi seguenti nella stessa area, anziché creare risorse distinte:

    * Riconoscimento vocale
    * Riconoscimento vocale personalizzato
    * Sintesi vocale
    * Traduzione vocale

2. Scaricare [Speech SDK](speech-sdk.md).

3. Seguire le guide introduttive e gli esempi di SDK per usare le API corrette. Se si usano le API REST, è necessario anche usare le chiavi di risorsa e gli endpoint corretti.

4. Aggiornare l'applicazione client per usare le API e il servizio Voce.

> [!NOTE]
> * Se è stato abilitato il servizio Voce in Language Understanding (LUIS), una singola risorsa LUIS nella stessa area funzionerà per LUIS e per tutti i servizi vocali. Per altre informazioni, vedere [Riconoscere le finalità dai contenuti vocali](how-to-recognize-intents-from-speech-csharp.md).
> * La traduzione testuale non fa parte del servizio Voce. Questa funzionalità richiede una sottoscrizione di Azure dedicata.
 


## <a name="migration-for-existing-customers"></a>Migrazione per i clienti esistenti

Eseguire la migrazione delle chiavi di risorsa esistenti al servizio Voce usando il relativo portale. Seguire questa procedura:

> [!NOTE]
> La migrazione delle chiavi di risorsa può essere eseguita solo all'interno della stessa area.

1. Accedere al portale [cris.ai](http://www.cris.ai) e selezionare la sottoscrizione nel menu in alto a destra.

2. Selezionare **Migrate selected subscription** (Migrazione sottoscrizione selezionata).

3. Immettere la chiave di sottoscrizione nella casella di testo e selezionare **Esegui la migrazione**.

## <a name="next-steps"></a>Passaggi successivi

* [Provare gratuitamente il servizio Voce](get-started.md).
* Apprendere i concetti del [riconoscimento vocale](./speech-to-text.md).

## <a name="see-also"></a>Vedere anche 

* [Informazioni sul servizio Voce](overview.md)
* [Documentazione sul Servizio di riconoscimento vocale e Speech SDK](speech-sdk.md#get-the-sdk)
