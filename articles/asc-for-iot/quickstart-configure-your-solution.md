---
title: Configurare la soluzione Centro sicurezza di Azure per IoT - Anteprima | Microsoft Docs
description: Informazioni su come configurare la soluzione IoT end-to-end con il Centro sicurezza di Azure per IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 64a04861b14e48eaa14a369546dd9d54bf59f019
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861922"
---
# <a name="quickstart-configure-your-iot-solution"></a>Guida introduttiva: Configurare la soluzione IoT

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo spiega come eseguire la configurazione iniziale della soluzione di sicurezza IoT con il Centro sicurezza di Azure per IoT. 

## <a name="azure-security-center-asc-for-iot"></a>Centro sicurezza di Azure per IoT

Il Centro sicurezza di AZURE per IoT offre la sicurezza end-to-end completa per soluzioni IoT basate su Azure.

Con il Centro sicurezza di Azure per IoT è possibile monitorare l'intera soluzione IoT in un unico dashboard, visualizzando tutti i dispositivi IoT, le piattaforme IoT e le risorse di back-end in Azure.

Dopo l'abilitazione sull'hub IoT, il Centro sicurezza di Azure per IoT identifica automaticamente gli altri servizi di Azure, anch'essi connessi all'hub IoT e correlati alla soluzione IoT.

Oltre al rilevamento automatico delle relazioni, è anche possibile scegliere quali altre risorse di Azure contrassegnare come parte della soluzione IoT.
Le opzioni selezionate consentono di aggiungere intere sottoscrizioni, gruppi di risorse o singole risorse.

Dopo aver definito tutte le relazioni delle risorse, il Centro sicurezza di Azure per IoT sfrutta il Centro sicurezza di Azure per fornire raccomandazioni e avvisi di sicurezza per tali risorse.

## <a name="add-azure-resources-to-your-iot-solution"></a>Aggiungere le risorse di Azure alla soluzione IoT

Per aggiungere una nuova risorsa alla soluzione IoT, eseguire le operazioni seguenti: 

1. Aprire l'**hub IoT** nel portale di Azure. 
2. Selezionare e aprire **Risorse** in **Sicurezza** nel menu a sinistra. 
3. Selezionare **Aggiungi risorse**.
4. Scegliere le risorse che appartengono alla soluzione IoT.
5. Fare clic su **Aggiungi**. 

Congratulazioni! È stata aggiunta una nuova risorsa alla soluzione IoT.

Il Centro sicurezza di Azure per IoT ora monitora le risorse appena aggiunte e visualizza le raccomandazioni e gli avvisi di sicurezza rilevanti come parte della soluzione IoT.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare moduli di sicurezza, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Creare moduli di sicurezza](quickstart-create-security-twin.md)