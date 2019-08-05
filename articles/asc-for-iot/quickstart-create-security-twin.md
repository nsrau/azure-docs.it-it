---
title: Creare un modulo gemello di sicurezza per il Centro sicurezza di Azure per IoT | Microsoft Docs
description: Informazioni su come creare un modulo gemello del Centro sicurezza di Azure per IoT per l'uso con il Centro sicurezza di Azure per IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c782692e-1284-4c54-9d76-567bc13787cc
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 92bf79aa5ae55bad16d68a26dc13d292285a4d46
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597070"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Guida introduttiva: Creare un modulo gemello azureiotsecurity

Questo argomento di avvio rapido spiega come creare singoli moduli gemelli _azureiotsecurity_ per i nuovi dispositivi oppure creare moduli gemelli in batch per tutti i dispositivi in un hub IoT.  

## <a name="understanding-azureiotsecurity-module-twins"></a>Informazioni sui moduli gemelli azureiotsecurity 

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi. 

Il Centro sicurezza di Azure per IoT offre l'integrazione completa con la piattaforma di gestione dispositivi IoT esistente, consentendo di gestire lo stato di sicurezza dei dispositivi, nonché di usufruire delle funzionalità di controllo dispositivi esistenti.
L'integrazione del Centro sicurezza di Azure per IoT si ottiene usando il meccanismo del dispositivo gemello dell'hub IoT.  

Per informazioni sul concetto generale dei moduli gemelli nell'hub IoT di Azure, vedere [Moduli gemelli nell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins). 
 
Il Centro sicurezza di Azure per IoT usa il meccanismo dei moduli gemelli, mantenendo un modulo gemello di sicurezza denominato _azureiotsecurity_ per ogni dispositivo.

Il modulo gemello di sicurezza contiene tutte le informazioni pertinenti alla sicurezza del dispositivo per ognuno dei dispositivi. 
 
Per avvalersi di tutte le funzionalità del Centro sicurezza di Azure per IoT, è necessario creare, configurare e usare questi moduli gemelli di sicurezza per ogni dispositivo nel servizio.  

## <a name="create-azureiotsecurity-module-twin"></a>Creare un modulo gemello azureiotsecurity 

I moduli gemelli _azureiotsecurity_ possono essere creati in due modi:
1. [Script batch del modulo](https://aka.ms/iot-security-github-create-module): crea automaticamente il modulo gemello per i nuovi dispositivi o per quelli che ne sono privi usando la configurazione predefinita.
2. Modifica manuale di ogni singolo modulo gemello con configurazioni specifiche per ogni dispositivo.

>[!NOTE] 
> L'uso del metodo batch non sovrascrive i moduli gemelli azureiotsecurity esistenti, ma crea SOLO nuovi moduli gemelli per i dispositivi che ne sono privi. 

Per informazioni su come modificare o cambiare la configurazione di un modulo gemello esistente, vedere l'articolo sulla [configurazione degli agenti](how-to-agent-configuration.md). 

Per creare manualmente un nuovo modulo gemello _azureiotsecurity_ per un dispositivo, seguire queste istruzioni: 

1. Nell'hub IoT trovare e selezionare il dispositivo per cui si vuole creare un modulo gemello di sicurezza.
1. Fare clic sul dispositivo e quindi su **Aggiungi identità modulo**.
1. Nel campo **Nome identità del modulo** immettere **azureiotsecurity**.

1. Fare clic su **Save**. 

## <a name="verify-creation-of-a-module-twin"></a>Verificare la creazione di un modulo gemello

Per verificare se è presente un modulo gemello di sicurezza per un dispositivo specifico:

1. Nell'hub IoT di Azure, selezionare **Dispositivi IoT** dal menu **Strumenti di esplorazione**.    
1. Immettere l'ID del dispositivo o selezionare un'opzione nel **campo Esegui query sui dispositivi** e fare clic su **Esegui query sui dispositivi**. 
    ![Query su dispositivi](./media/quickstart/verify-security-module-twin.png)
1. Selezionare il dispositivo oppure fare doppio clic per aprire la pagina Dettagli dispositivo. 
1. Selezionare il menu **Identità del modulo** e verificare l'esistenza del modulo **azureiotsecurity** nell'elenco delle identità dei moduli associate al dispositivo. 
    ![Moduli associati a un dispositivo](./media/quickstart/verify-security-module-twin-3.png)


Per altre informazioni sulla personalizzazione delle proprietà dei moduli gemelli del Centro sicurezza di Azure per IoT, vedere [Configurazione dell'agente](how-to-agent-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare gli avvisi personalizzati, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Configurare avvisi personalizzati](quickstart-create-custom-alerts.md)
