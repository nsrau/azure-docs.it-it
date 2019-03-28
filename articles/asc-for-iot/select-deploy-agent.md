---
title: Selezionare e distribuire un Centro sicurezza di AZURE per l'agente di IoT Preview | Microsoft Docs
description: Informazioni su come selezionare e distribuire Centro sicurezza di AZURE per IoT gli agenti protezione nei dispositivi IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541963"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selezionare e distribuire un agente protezione nel dispositivo IoT

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Centro sicurezza di AZURE per IoT fornisce le architetture di riferimento per gli agenti di protezione che monitorano e raccolgono i dati dai dispositivi IoT.

Gli agenti di protezione vengono sviluppati come progetti open source e sono disponibili in due versioni: <br> [C](https://aka.ms/iot-security-github-c), e [ C# ](https://aka.ms/iot-security-github-cs).

## <a name="supported-platforms-for-asc-for-iot-agents"></a>Piattaforme supportate per Centro sicurezza di AZURE per gli agenti di IoT

Nell'elenco seguente include tutte le piattaforme attualmente supportate.  

|Centro sicurezza di AZURE per l'agente di IoT |Sistema operativo |Architettura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core compilazione 17763 |x64|


## <a name="which-flavor-should-i-use"></a>Quale versione usare?

Prendere in considerazione le domande seguenti per quanto riguarda i dispositivi IoT:

- Usi _Windows Server_ oppure _Windows IoT Core_? 

    Uso [Centro sicurezza di AZURE per l'installazione di IoT per Windows con il C#-basato su agente protezione](tutorial-deploy-windows-cs.md).

- Si usa una distribuzione Linux con x86 architettura? 

    Uso [Centro sicurezza di AZURE per l'installazione di IoT per Linux con l'agente protezione basata su C](tutorial-deploy-linux-c.md).
- Si usa una distribuzione Linux con x64 architettura?

    È possibile usare entrambi i tipi. <br>
    [Centro sicurezza di AZURE per l'installazione di IoT per Linux con l'agente protezione basata su C](tutorial-deploy-linux-c.md) e/o [Centro sicurezza di AZURE per l'installazione di IoT per Linux con il C#-basato su agente di protezione](tutorial-deploy-linux-cs.md).

Entrambi i tipi hanno lo stesso set di funzionalità e supportano le opzioni di configurazione simile. L'agente protezione basata su C ha un impatto minore sulla memoria.


## <a name="next-steps"></a>Passaggi successivi
- [Overview](overview.md)
- [Agenti di protezione](security-agent-architecture.md)
- [Metodi di autenticazione di sicurezza dell'agente](concept-security-agent-authentication-methods.md)
- [Centro sicurezza di AZURE per IoT domande frequenti](resources-frequently-asked-questions.md)