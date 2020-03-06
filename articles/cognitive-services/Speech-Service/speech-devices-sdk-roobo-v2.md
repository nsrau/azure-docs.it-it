---
title: Speech Devices SDK Roobo smart audio Dev Kit V2-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Prerequisiti e istruzioni per iniziare a usare Speech Devices SDK, Roobo smart audio Dev Kit V2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 5cf851bc9333004c0e14713cde44f470fb8c0c02
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304286"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Dispositivo: Roobo smart audio Dev Kit V2

Questo articolo fornisce informazioni specifiche del dispositivo per Roobo smart audio dev Kit2.

## <a name="set-up-the-development-kit"></a>Configurare il kit di sviluppo

1. Il kit di sviluppo ha due connettori Micro-USB. Il connettore di sinistra alimenta il kit di sviluppo ed è contrassegnato con Power nell'immagine seguente. Il connettore di destra ha la funzione di controllare il kit ed è contrassegnato con Debug nell'immagine. 
    ![la connessione del Dev Kit](media/speech-devices-sdk/roobo-v2-connections.png)
1. Alimentare il kit di sviluppo usando un cavo Micro-USB per collegare la porta di alimentazione a un PC o a una presa di alimentazione. Sotto il ripiano superiore si illuminerà la spia verde di accensione.
1. Per controllare il kit di sviluppo, connettere la porta di debug a un computer usando un secondo cavo micro USB. È essenziale usare un cavo di qualità elevata per garantire comunicazioni affidabili.
1. Orientare il kit di sviluppo in modo circolare, con i microfoni sul soffitto, come illustrato in precedenza


## <a name="development-information"></a>Informazioni sullo sviluppo

Per ulteriori informazioni sullo sviluppo, vedere la [Guida allo sviluppo di Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Record audio/riproduzione

Le operazioni di DDK2 audio possono essere eseguite nei modi seguenti:
* Usare librerie open source ALSA e le relative applicazioni.
* Usare l'interfaccia appmainprog per lo sviluppo di applicazioni. Il Framework software correlato all'audio di DDK2 usa il Framework ALSA standard, è possibile usare libasound. Quindi, per sviluppare direttamente il software. È quindi possibile usare arecord e aplay di ALSA direttamente per registrare e riprodurre l'audio.
