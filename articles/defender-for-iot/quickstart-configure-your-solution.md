---
title: 'Guida introduttiva: Configurare la soluzione'
description: In questo avvio rapido sono disponibili informazioni su come configurare la soluzione IoT end-to-end con Azure Defender per IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: f949d52d4ba461da94153cbbd17ca5fb3eb214d7
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092087"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Avvio rapido: Configurare la soluzione Azure Defender per IoT

Questo articolo descrive come eseguire la configurazione iniziale della soluzione di sicurezza IoT con Defender per IoT.

## <a name="what-is-defender-for-iot"></a>Che cos'è l'offerta Defender per IoT?

Defender per IoT offre sicurezza end-to-end completa per soluzioni IoT basate su Azure.

Con Defender per IoT è possibile monitorare l'intera soluzione IoT in un unico dashboard, visualizzando tutti i dispositivi IoT, le piattaforme IoT e le risorse di back-end in Azure.

Dopo l'abilitazione sull'hub IoT, Defender per IoT identifica automaticamente gli altri servizi di Azure, anch'essi connessi all'hub IoT e correlati alla soluzione IoT.

Oltre al rilevamento automatico delle relazioni, è anche possibile scegliere quali altri gruppi di risorse di Azure contrassegnare come parte della soluzione IoT.

Le opzioni selezionate consentono di aggiungere intere sottoscrizioni, gruppi di risorse o singole risorse.

Dopo aver definito tutte le relazioni delle risorse, Defender per IoT sfrutta Defender per fornire raccomandazioni e avvisi di sicurezza per tali risorse.

## <a name="add-azure-resources-to-your-iot-solution"></a>Aggiungere le risorse di Azure alla soluzione IoT

Per aggiungere una nuova risorsa alla soluzione IoT, eseguire le operazioni seguenti:

1. Aprire l' **hub IoT** nel portale di Azure.
1. In **Sicurezza** selezionare **Panoramica** , **Impostazioni** e quindi **Risorse monitorate** .
1. Selezionare **Modifica** e scegliere le risorse monitorate che appartengono alla soluzione IoT.
1. Scegliere **Aggiungi** .

Congratulazioni! È stato aggiunto un nuovo gruppo di risorse alla soluzione IoT.

Defender per IoT ora monitora i gruppi di risorse appena aggiunti e visualizza le raccomandazioni e gli avvisi di sicurezza pertinenti come parte della soluzione IoT.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare moduli di sicurezza, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Creare moduli di sicurezza](quickstart-create-security-twin.md)
