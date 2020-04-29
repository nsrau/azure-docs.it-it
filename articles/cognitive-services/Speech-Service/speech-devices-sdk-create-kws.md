---
title: Creazione di parole chiave personalizzate-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il dispositivo è sempre in ascolto di una parola chiave (o frase). Quando l'utente dice la parola chiave, il dispositivo invia tutti i file audio successivi al cloud, fino a quando l'utente non smette di parlare. Personalizzare la parola chiave è un modo efficace per distinguere il dispositivo e rafforzare la personalizzazione.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400071"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Creare una parola chiave personalizzata usando speech studio

Il dispositivo è sempre in ascolto di una parola chiave (o frase). Ad esempio, "Hey Cortana" è una parola chiave per Cortana Assistant. Quando l'utente dice la parola chiave, il dispositivo invia tutti i file audio successivi al cloud, fino a quando l'utente non smette di parlare. Personalizzare la parola chiave è un modo efficace per distinguere il dispositivo e rafforzare la personalizzazione.

Questo articolo illustra come creare una parola chiave personalizzata per il dispositivo.

## <a name="create-your-keyword"></a>Crea la parola chiave

Prima di poter usare una parola chiave personalizzata, è necessario creare una parola chiave usando la pagina delle [parole chiave personalizzata](https://aka.ms/sdsdk-wakewordportal) in [speech studio](https://aka.ms/sdsdk-speechportal). Dopo aver fornito una parola chiave, viene generato un file che viene distribuito nel dispositivo.

1. Passare a [speech studio](https://aka.ms/sdsdk-speechportal) e **accedere** oppure, se non si dispone ancora di una sottoscrizione vocale, scegliere [**Crea una sottoscrizione**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Nella pagina della [parola chiave Custom](https://aka.ms/sdsdk-wakewordportal) creare un **nuovo progetto**. 

1. Immettere un **nome**, una **Descrizione**facoltativa e selezionare la lingua. Sarà necessario un progetto per lingua e il supporto è attualmente limitato al linguaggio en-US.

    ![Descrivere il progetto di parola chiave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selezionare il progetto dall'elenco. 

    ![Selezionare il progetto di parola chiave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Per avviare un nuovo modello di parola chiave, fare clic su **Train Model**.

1. Immettere un **nome** per il modello di parola chiave e una **Descrizione** facoltativa e digitare la **parola chiave** desiderata, quindi fare clic su **Avanti**. Sono disponibili alcune [linee guida](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) che consentono di scegliere una parola chiave efficace.

    ![Immettere la parola chiave](media/custom-keyword/custom-kws-portal-new-model.png)

1. Il portale creerà ora le pronunce candidate per la parola chiave. Ascoltare ogni candidato facendo clic sui pulsanti di riproduzione e rimuovere i controlli accanto a tutte le pronunce non corrette. Una volta selezionate solo le pronunce valide, fare clic su **Train** per iniziare a generare la parola chiave. 

    ![Esaminare la parola chiave](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Potrebbero essere necessari fino a trenta minuti per la generazione del modello. L'elenco delle parole chiave verrà modificato da **elaborazione** **a completata** quando il modello è completo. È quindi possibile scaricare il file.

    ![Esaminare la parola chiave](media/custom-keyword/custom-kws-portal-download-model.png)

1. Salvare il file ZIP nel computer. Questo file sarà necessario per distribuire la parola chiave personalizzata nel dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Testare la parola chiave personalizzata con la [Guida introduttiva all'SDK dei dispositivi vocali](https://aka.ms/sdsdk-quickstart).
