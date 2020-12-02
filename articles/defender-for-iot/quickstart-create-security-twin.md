---
title: 'Guida introduttiva: Creare un modulo gemello di sicurezza'
description: In questo argomento di avvio rapido sono disponibili informazioni su come creare un modulo gemello di Defender per IoT per l'uso con Azure Defender per IoT.
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
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 74e0e8daa662f4dd49f1886972236b5b0a3b100a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348858"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Guida introduttiva: Creare un modulo gemello azureiotsecurity

Questo argomento di avvio rapido spiega come creare singoli moduli gemelli _azureiotsecurity_ per i nuovi dispositivi oppure creare moduli gemelli in batch per tutti i dispositivi in un hub IoT.

## <a name="understanding-azureiotsecurity-module-twins"></a>Informazioni sui moduli gemelli azureiotsecurity

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi.

Defender per IoT offre l'integrazione completa con la piattaforma di gestione di dispositivi IoT esistente, consentendo di gestire lo stato di sicurezza dei dispositivi, nonché di fare uso delle funzionalità di controllo dispositivo esistenti.
L'integrazione di Defender per IoT si ottiene usando il meccanismo del dispositivo gemello dell'hub IoT.

Per informazioni sul concetto generale dei moduli gemelli nell'hub IoT di Azure, vedere [Moduli gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-module-twins.md).

Defender per IoT usa il meccanismo dei moduli gemelli, mantenendo un modulo gemello di sicurezza denominato _azureiotsecurity_ per ogni dispositivo.

Il modulo gemello di sicurezza contiene tutte le informazioni pertinenti alla sicurezza del dispositivo per ognuno dei dispositivi.

Per avvalersi di tutte le funzionalità di Defender per IoT, è necessario creare, configurare e usare questi moduli gemelli di sicurezza per ogni dispositivo nel servizio.

## <a name="create-azureiotsecurity-module-twin"></a>Creare un modulo gemello azureiotsecurity

I moduli gemelli _azureiotsecurity_ possono essere creati in due modi:

1. [Script batch del modulo](https://aka.ms/iot-security-github-create-module): crea automaticamente il modulo gemello per i nuovi dispositivi o per quelli che ne sono privi usando la configurazione predefinita.
1. Modifica manuale di ogni singolo modulo gemello con configurazioni specifiche per ogni dispositivo.

>[!NOTE]
> L'uso del metodo batch non sovrascrive i moduli gemelli azureiotsecurity esistenti, ma crea SOLO nuovi moduli gemelli per i dispositivi che ne sono privi.

Per informazioni su come modificare o cambiare la configurazione di un modulo gemello esistente, vedere l'articolo sulla [configurazione degli agenti](how-to-agent-configuration.md).

Per creare manualmente un nuovo modulo gemello _azureiotsecurity_ per un dispositivo, seguire queste istruzioni:

1. Nell'hub IoT trovare e selezionare il dispositivo per cui si vuole creare un modulo gemello di sicurezza.
1. Fare clic sul dispositivo e quindi su **Aggiungi identità modulo**.
1. Nel campo **Nome identità del modulo** immettere **azureiotsecurity**.

1. Fare clic su **Salva**.

## <a name="verify-creation-of-a-module-twin"></a>Verificare la creazione di un modulo gemello

Per verificare se è presente un modulo gemello di sicurezza per un dispositivo specifico:

1. Nell'hub IoT di Azure, selezionare **Dispositivi IoT** dal menu **Strumenti di esplorazione**.
1. Immettere l'ID del dispositivo o selezionare un'opzione nel **campo Esegui query sui dispositivi** e fare clic su **Esegui query sui dispositivi**.
    ![Query su dispositivi](./media/quickstart/verify-security-module-twin.png)
1. Selezionare il dispositivo oppure fare doppio clic per aprire la pagina Dettagli dispositivo.
1. Selezionare il menu **Identità del modulo** e verificare l'esistenza del modulo **azureiotsecurity** nell'elenco delle identità dei moduli associate al dispositivo.
    ![Moduli associati a un dispositivo](./media/quickstart/verify-security-module-twin-3.png)

Per altre informazioni sulla personalizzazione delle proprietà dei moduli gemelli di Defender per IoT, vedere [Configurazione dell'agente](how-to-agent-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come esaminare le raccomandazioni sulla sicurezza, passare all'articolo successivo:

> [!div class="nextstepaction"]
> [Esaminare le raccomandazioni sulla sicurezza](quickstart-investigate-security-recommendations.md)