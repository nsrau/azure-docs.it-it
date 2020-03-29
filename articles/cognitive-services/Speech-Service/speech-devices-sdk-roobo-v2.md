---
title: Speech Devices SDK Roobo Smart Audio Dev Kit v2 - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Prerequisiti e istruzioni per iniziare a utilizzare L'SDK dei dispositivi di riconoscimento vocale, Roobo Smart Audio Dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371588"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Dispositivo: Roobo Smart Audio Dev Kit v2

Questo articolo fornisce informazioni specifiche del dispositivo per Roobo Smart Audio Dev Kit2.

## <a name="set-up-the-development-kit"></a>Configurare il kit di sviluppo

1. Il kit di sviluppo ha due connettori Micro-USB. Il connettore di sinistra alimenta il kit di sviluppo ed è contrassegnato con Power nell'immagine seguente. Il connettore di destra ha la funzione di controllare il kit ed è contrassegnato con Debug nell'immagine. 
    ![Connessione del kit di sviluppo](media/speech-devices-sdk/roobo-v2-connections.png)
1. Alimentare il kit di sviluppo usando un cavo Micro-USB per collegare la porta di alimentazione a un PC o a una presa di alimentazione. Sotto il ripiano superiore si illuminerà la spia verde di accensione.
1. Per controllare il kit di sviluppo, collegare la porta di debug a un computer utilizzando un secondo cavo MICRO USB. È essenziale utilizzare un cavo di alta qualità per garantire comunicazioni affidabili.
1. Orientare il kit di sviluppo Circolare - Verticale, con microfoni rivolti verso il soffitto come mostrato sopra


## <a name="development-information"></a>Informazioni sullo sviluppo

Per ulteriori informazioni sullo sviluppo, vedere la guida allo sviluppo di [Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Registrazione/riproduzione audio

Le operazioni audio DDK2 possono essere eseguite nei seguenti modi:
* Utilizzare le librerie open source ALSA e le relative applicazioni.
* Utilizzare l'interfaccia appmainprog per eseguire lo sviluppo di applicazioni. DDK2 audio - framework software correlato utilizza quadro ALSA standard, è possibile utilizzare libasound. Quindi, per sviluppare il software direttamente. Così è possibile utilizzare arecord di ALSA e aplay direttamente per registrare e riprodurre l'audio.
