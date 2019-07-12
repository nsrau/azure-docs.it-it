---
title: Comprendere il Centro sicurezza di Azure per il modulo di sicurezza IoT gemelli Preview | Microsoft Docs
description: Scopri il concetto di moduli gemelli di sicurezza e su come vengono usati nel Centro sicurezza di Azure per IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 8f964fd2b121c6232081673666f5babb160e1721
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618300"
---
# <a name="security-module"></a>Modulo di sicurezza

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come Azure Security Center (Centro sicurezza di AZURE) per IoT Usa i moduli e ai dispositivi gemelli. 

## <a name="device-twins"></a>Dispositivi gemelli

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi.  

Il Centro sicurezza di Azure per IoT offre l'integrazione completa con la piattaforma di gestione di dispositivi IoT esistente, consentendo di gestire lo stato di sicurezza dei dispositivi, nonché di fare uso delle funzionalità di controllo dispositivo esistenti. L'integrazione è realizzata creando usare dell'IoT Hub twin meccanismo.  

Altre informazioni sui concetti di relativi [sui dispositivi gemelli](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) nell'IoT Hub di Azure. 

## <a name="security-module-twins"></a>Moduli gemelli di sicurezza

Centro sicurezza di AZURE per IoT mantiene un modulo gemello di sicurezza per ogni dispositivo nel servizio.
Il dispositivo gemello del modulo security contiene tutte le informazioni relative alla sicurezza dei dispositivi per ogni dispositivo specifico nella soluzione.
Le proprietà di sicurezza di dispositivi vengono gestite in un modulo gemello sicurezza dedicato per la comunicazione più sicura e per l'abilitazione degli aggiornamenti e manutenzione che richiedono meno risorse.  

Visualizzare [crea dispositivo gemello del modulo security](quickstart-create-security-twin.md) e [configurare gli agenti di protezione](how-to-agent-configuration.md) per imparare a creare, personalizzare e configurare il dispositivo gemello. Visualizzare [moduli gemelli Understanding](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) per approfondire il concetto di moduli gemelli nell'IoT Hub. 
 

## <a name="see-also"></a>Vedere anche
- [Centro sicurezza di AZURE per l'anteprima di IoT](overview.md)
- [Distribuire gli agenti protezione](how-to-deploy-agent.md)
- [Metodi di autenticazione di sicurezza dell'agente](concept-security-agent-authentication-methods.md)