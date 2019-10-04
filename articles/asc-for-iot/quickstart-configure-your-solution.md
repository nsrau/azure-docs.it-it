---
title: Configurare la soluzione Centro sicurezza di Azure per IoT | Microsoft Docs
description: Informazioni su come configurare la soluzione IoT end-to-end con il Centro sicurezza di Azure per IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: a546d153c6fe4f14ccc8c21308bd4a33385870c3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299696"
---
# <a name="quickstart-configure-your-iot-solution"></a>Guida introduttiva: Configurare la soluzione IoT

Questo articolo spiega come eseguire la configurazione iniziale della soluzione di sicurezza IoT con il Centro sicurezza di Azure per IoT. 

## <a name="azure-security-center-for-iot"></a>Centro sicurezza di Azure per IoT

Il Centro sicurezza di Azure per IoT offre sicurezza end-to-end completa per soluzioni IoT basate su Azure.

Con il Centro sicurezza di Azure per IoT è possibile monitorare l'intera soluzione IoT in un unico dashboard, visualizzando tutti i dispositivi IoT, le piattaforme IoT e le risorse di back-end in Azure.

Dopo l'abilitazione nell'hub IoT, il Centro sicurezza di Azure per IoT identifica automaticamente gli altri servizi di Azure, anch'essi connessi all'hub IoT e correlati alla soluzione IoT.

Oltre al rilevamento automatico delle relazioni, è anche possibile scegliere quali altri gruppi di risorse di Azure contrassegnare come parte della soluzione IoT. 

Le opzioni selezionate consentono di aggiungere intere sottoscrizioni, gruppi di risorse o singole risorse. 

Dopo aver definito tutte le relazioni delle risorse, il Centro sicurezza di Azure per IoT sfrutta il Centro sicurezza di Azure per fornire raccomandazioni e avvisi di sicurezza per tali risorse.

## <a name="add-azure-resources-to-your-iot-solution"></a>Aggiungere le risorse di Azure alla soluzione IoT

Per aggiungere una nuova risorsa alla soluzione IoT, eseguire le operazioni seguenti: 

1. Aprire l'**hub IoT** nel portale di Azure. 
1. Selezionare e aprire **Risorse** in **Sicurezza** nel menu a sinistra. 
1. Selezionare **Modifica** e scegliere i gruppi di risorse che appartengono alla soluzione IoT.
1. Fare clic su **Aggiungi**. 

Congratulazioni! È stato aggiunto un nuovo gruppo di risorse alla soluzione IoT.

Il Centro sicurezza di Azure per IoT ora monitora i gruppi di risorse appena aggiunti e visualizza le raccomandazioni e gli avvisi di sicurezza pertinenti come parte della soluzione IoT.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare moduli di sicurezza, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Creare moduli di sicurezza](quickstart-create-security-twin.md)