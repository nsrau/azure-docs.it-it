---
title: Creare parole chiave personalizzate - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il dispositivo è sempre in ascolto di una parola chiave (o frase). Quando l'utente dice la parola chiave, il dispositivo invia tutto l'audio successivo al cloud, fino a quando l'utente smette di parlare. Personalizzare la parola chiave è un modo efficace per differenziare il tuo dispositivo e rafforzare il tuo branding.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400071"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Creare una parola chiave personalizzata con Speech Studio

Il dispositivo è sempre in ascolto di una parola chiave (o frase). Ad esempio, "Ehi Cortana" è una parola chiave per l'assistente Cortana. Quando l'utente dice la parola chiave, il dispositivo invia tutto l'audio successivo al cloud, fino a quando l'utente smette di parlare. Personalizzare la parola chiave è un modo efficace per differenziare il tuo dispositivo e rafforzare il tuo branding.

In questo articolo viene illustrato come creare una parola chiave personalizzata per il dispositivo.

## <a name="create-your-keyword"></a>Crea la tua parola chiave

Prima di poter utilizzare una parola chiave personalizzata, è necessario creare una parola chiave utilizzando la pagina [Parola chiave personalizzata](https://aka.ms/sdsdk-wakewordportal) in [Speech Studio](https://aka.ms/sdsdk-speechportal). Dopo aver fornito una parola chiave, viene generato un file distribuito nel dispositivo.

1. Passare a [Speech Studio](https://aka.ms/sdsdk-speechportal) e **accedere** oppure, se non si dispone ancora di una sottoscrizione vocale, scegliere Crea [**una sottoscrizione**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Nella pagina [Parola chiave personalizzata](https://aka.ms/sdsdk-wakewordportal) creare un **nuovo progetto**. 

1. Immettere un **Nome**, una **Descrizione**facoltativa e selezionare la lingua. Avrete bisogno di un progetto per lingua e il supporto è attualmente limitato alla lingua en-US.

    ![Descrivi il tuo progetto di parola chiave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selezionare il progetto dall'elenco. 

    ![Selezionare il progetto di parole chiave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Per iniziare un nuovo modello di parole chiave, fare clic su **Formato modello**.

1. Immettere un **Nome** per il modello di parole chiave e **una descrizione** facoltativa e digitare la **parola chiave** desiderata, quindi fare clic su **Avanti**. Abbiamo alcune [linee guida](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) per aiutarti a scegliere una parola chiave efficace.

    ![Inserisci la tua parola chiave](media/custom-keyword/custom-kws-portal-new-model.png)

1. Il portale creerà ora pronunce candidate per la parola chiave. Ascoltare ogni candidato facendo clic sui pulsanti di riproduzione e rimuovere i controlli accanto a eventuali pronunce non corrette. Una volta selezionate solo le pronunce buone, fare clic su **Aggiungi** per iniziare a generare la parola chiave. 

    ![Esaminare la parola chiave](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. La generazione del modello può richiedere fino a trenta minuti. L'elenco di parole chiave cambierà da **Elaborazione** a **Completato** al termine del modello. È quindi possibile scaricare il file.

    ![Esaminare la parola chiave](media/custom-keyword/custom-kws-portal-download-model.png)

1. Salvare il file ZIP nel computer. Questo file è necessario per distribuire la parola chiave personalizzata nel dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Testare la parola chiave personalizzata con [speech Devices SDK Quickstart](https://aka.ms/sdsdk-quickstart).
