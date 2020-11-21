---
title: Creazione rapida di parole chiave-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il dispositivo è sempre in ascolto di una parola chiave (o frase). Quando l'utente dice la parola chiave, il dispositivo invia tutti i file audio successivi al cloud, fino a quando l'utente non smette di parlare. Personalizzare la parola chiave è un modo efficace per distinguere il dispositivo e rafforzare la personalizzazione.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 49ac70b6881085f48c8bc3a12e31e4a1aa220c6a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021950"
---
# <a name="get-started-with-custom-keyword"></a>Introduzione alla parola chiave personalizzata

Questa Guida introduttiva illustra le nozioni di base sull'uso di parole chiave personalizzate, con speech studio e l'SDK di riconoscimento vocale. Una parola chiave è una parola o una frase breve che consente di attivare la voce del prodotto. È possibile creare modelli di parole chiave in speech studio, quindi esportare un file di modello usato con l'SDK di riconoscimento vocale nelle applicazioni.

## <a name="prerequisites"></a>Prerequisiti

I passaggi descritti in questo articolo richiedono una sottoscrizione vocale e l'SDK vocale. Se non si ha già una sottoscrizione, [provare il servizio di riconoscimento vocale gratuitamente](overview.md#try-the-speech-service-for-free). Per ottenere l'SDK, vedere la [Guida all'installazione](quickstarts/setup-platform.md) per la piattaforma in uso.

## <a name="create-a-keyword-in-speech-studio"></a>Creare una parola chiave in speech studio

Prima di poter usare una parola chiave personalizzata, è necessario creare una parola chiave usando la pagina delle [parole chiave personalizzata](https://aka.ms/sdsdk-wakewordportal) in [speech studio](https://aka.ms/sdsdk-speechportal). Dopo aver fornito una parola chiave, viene prodotto un `.table` file che è possibile usare con l'SDK di riconoscimento vocale.

> [!IMPORTANT]
> I modelli di parole chiave personalizzate e i file risultanti `.table` possono essere creati **solo** in speech studio.
> Non è possibile creare parole chiave personalizzate dall'SDK o con chiamate REST.

1. Passare a [speech studio](https://aka.ms/sdsdk-speechportal) e **accedere** oppure, se non si dispone ancora di una sottoscrizione vocale, scegliere [**Crea una sottoscrizione**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Nella pagina della [parola chiave Custom](https://aka.ms/sdsdk-wakewordportal) creare un **nuovo progetto**. 

1. Immettere un **nome**, una **Descrizione** facoltativa e selezionare la lingua. È necessario un progetto per ogni lingua e il supporto è attualmente limitato al `en-US` linguaggio.

    ![Descrivere il progetto di parola chiave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selezionare il progetto dall'elenco. 

    ![Selezionare il progetto di parola chiave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Per creare un nuovo modello di parola chiave, fare clic su **Train Model**.

1. Immettere un **nome** per il modello, una **Descrizione** facoltativa e la **parola chiave** desiderata, quindi fare clic su **Avanti**. Vedere le [linee guida](./custom-keyword-overview.md#choose-an-effective-keyword) sulla scelta di una parola chiave valida.

    ![Immettere la parola chiave](media/custom-keyword/custom-kws-portal-new-model.png)

1. Il portale crea le pronunce candidate per la parola chiave. Ascoltare ogni candidato facendo clic sui pulsanti di riproduzione e rimuovere i controlli accanto a tutte le pronunce non corrette. Una volta selezionate solo le pronunce valide, fare clic su **Train** per iniziare a generare il modello di parola chiave. 

    ![Screenshot che mostra dove si sceglie il pronounciations corretto.](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Potrebbero essere necessari fino a trenta minuti per la generazione del modello. L'elenco delle parole chiave verrà modificato da **elaborazione** **a completata** quando il modello è completo. È quindi possibile scaricare il file.

    ![Esaminare la parola chiave](media/custom-keyword/custom-kws-portal-download-model.png)

1. Il file scaricato è un `.zip` archivio. Estrarre l'archivio. viene visualizzato un file con `.table` estensione. Si tratta del file usato con l'SDK nella sezione successiva, quindi assicurarsi di annotarne il percorso. il nome del file rispecchia il nome della parola chiave, ad esempio una parola chiave **Activate Device** ha il nome file `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Usare un modello di parola chiave con l'SDK

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Testare la parola chiave personalizzata con la [Guida introduttiva all'SDK dei dispositivi vocali](./speech-devices-sdk-quickstart.md?pivots=platform-android).