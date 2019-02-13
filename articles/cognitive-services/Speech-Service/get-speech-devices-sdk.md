---
title: Ottenere Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Il servizio Voce è compatibile con una vasta gamma di dispositivi e sorgenti audio. Ora è possibile portare le applicazioni vocali a un livello superiore con hardware e software abbinati. In questo articolo si apprenderà come ottenere l'accesso a Speech Devices SDK e iniziare a sviluppare.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: f8861b581c0e02bb78d80817cb2242a38e8aac40
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733468"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Ottenere Speech Devices SDK di Servizi cognitivi

Speech Devices SDK è disponibile in anteprima con restrizioni e richiede la registrazione nel programma. Attualmente, Microsoft preferisce le grandi aziende come candidati per l'accesso a questo prodotto.

## <a name="request-access"></a>Richiedere l'accesso

Per ottenere l'accesso a Speech Devices SDK:

1. Accedere al [modulo di registrazione](https://aka.ms/sdsdk-signup) di Microsoft Speech Devices SDK.
1. Leggere il [Contratto di Licenza](speech-devices-sdk-license.md).
1. Se si accettano le condizioni del contratto di licenza, selezionare **Accetto**.
1. Rispondere alle domande nel modulo.
1. Inviare il modulo.
1. Se l'indirizzo email non fa già parte di Azure Active Directory (Azure AD), dopo l'approvazione dell'accesso si riceverà un'e-mail di invito come quella nell'esempio seguente. Se l'indirizzo e-mail è già presente in Azure AD, si riceverà un messaggio di posta elettronica dal team di Microsoft Speech dopo l'approvazione e si potrà passare a [Scaricare Speech Devices SDK](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>Approvazione posta elettronica

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com>
Subject: You're invited to the Microsoft organization
```

![Messaggio di posta elettronica](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Accettare l'accesso

Completare i passaggi seguenti per aggiungersi ad Azure AD con l'indirizzo e-mail fornito durante la registrazione. Questo processo consente di accedere al [sito di download](https://shares.datatransfer.microsoft.com/) di Speech Devices SDK.

1. Fare clic su **Configurazione iniziale** nel messaggio di posta elettronica ricevuto. Se l'organizzazione è già cliente di Office 365, verrà richiesto di eseguire l'accesso ed è possibile passare al passaggio 7.

2. Nella finestra del browser che si apre, selezionare **Avanti**.

    ![Finestra di autenticazione](media/speech-devices-sdk/get-sdk-2.png)

3. Creare un account Microsoft, se non è già disponibile. Inserire lo stesso indirizzo e-mail al quale si è ricevuta l'e-mail di invito.

    ![Creare un account Microsoft](media/speech-devices-sdk/get-sdk-3.png)

4. Selezionare **Avanti** per creare una password.

5. Quando viene richiesto di verificare l'e-mail, recuperare il codice di verifica dal messaggio di invito.

7. Incollare o digitare il codice di sicurezza indicato nel messaggio di posta elettronica nella finestra di dialogo. In questo esempio il codice di sicurezza è **8406**. Selezionare **Avanti**.

    ![Verifica e-mail](media/speech-devices-sdk/get-sdk-6.png)

8. Quando nel browser viene visualizzata l'applicazione Pannello di accesso, è stato confermato che il proprio indirizzo e-mail fa parte di Azure Active Directory. È ora possibile accedere al sito di download Speech Devices SDK.

## <a name="download-the-speech-devices-sdk"></a>Scaricare Speech Devices SDK

Passare al [sito di download Speech Devices SDK](https://shares.datatransfer.microsoft.com/). Accedere con l'account Microsoft creato in precedenza.

![Sito di download sdk](media/speech-devices-sdk/get-sdk-7.png)

Per scaricare Speech Devices SDK, il codice di esempio associato e il materiale di riferimento:

1. Scaricare e installare lo strumento Aspera Connect quando richiesto nel browser.

    ![Scaricare Aspera Connect](media/speech-devices-sdk/get-sdk-8.png)

1. Selezionare **Sì** per passare ad Aspera Connect.

    ![Passare ad Aspera Connect](media/speech-devices-sdk/get-sdk-9.png)

1. Selezionare **Consenti** per confermare il download dei file con Aspera Connect.

    ![Download con Aspera Connect](media/speech-devices-sdk/get-sdk-10.png)

1. Dopo che sono stati scaricati i file, chiudere la finestra di trasferimento di Aspera Connect.

    ![Finestra di trasferimento di Aspera Connect](media/speech-devices-sdk/get-sdk-11.png)

Per impostazione predefinita, i file vengono scaricati nella cartella **Download**. Ora è possibile disconnettersi da questo sito.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione a Speech Devices SDK](speech-devices-sdk-qsg.md)
