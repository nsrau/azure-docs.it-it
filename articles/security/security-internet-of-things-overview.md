---
title: Proteggere le soluzioni IoT (Internet delle cose) in Azure | Documentazione Microsoft
description: " I servizi di Azure IoT (Internet delle cose) offrono una vasta gamma di funzionalità. Questo articolo illustra come proteggere le soluzioni IoT in Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: db1c2b9c852479b9af3674f6c5e1f1135ee289f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62107439"
---
# <a name="internet-of-things-security-overview"></a>Informazioni generali sulla sicurezza di Internet delle cose
I servizi di Azure IoT (Internet delle cose) offrono una vasta gamma di funzionalità. Questi servizi di livello aziendale consentono di:

* Raccogliere dati dai dispositivi
* Analizzare i flussi dei dati in movimento
* Archiviare ed eseguire query su set di dati di grandi dimensioni
* Visualizzare i dati in tempo reale e cronologici
* Eseguire l'integrazione con i sistemi back-office

Per offrire queste funzionalità, gli acceleratori di soluzioni di Azure IoT includono più servizi di Azure con estensioni personalizzate come soluzioni preconfigurate. Queste soluzioni preconfigurate sono implementazioni di base dei modelli di soluzione IoT comuni che contribuiscono a ridurre i tempi di distribuzione delle soluzioni IoT. I Software Development Kit IoT consentono di personalizzare ed estendere queste soluzioni per soddisfare le proprie esigenze. È anche possibile usare queste soluzioni come esempi o modelli durante lo sviluppo di nuove soluzioni IoT.

Gli acceleratori di soluzioni di Azure IoT sono potenti soluzioni per le proprie esigenze IoT. È tuttavia fondamentale che le soluzioni IoT siano progettate sin dall'inizio pensando alla sicurezza. A causa del numero elevato di dispositivi IoT, qualsiasi evento imprevisto riguardante la sicurezza può diventare rapidamente un evento esteso con conseguenze significative.

Le informazioni seguenti sono utili per comprendere come proteggere le soluzioni IoT.

## <a name="security-architecture"></a>Architettura di sicurezza
Quando si progetta un sistema, è importante comprendere le potenziali minacce e aggiungere le difese appropriate di conseguenza, perché il sistema è definito da una progettazione e un'architettura specifiche. È importante progettare il prodotto tenendo conto della sicurezza, perché comprendere in che modo un utente malintenzionato potrebbe compromettere un sistema aiuta ad implementare le misure appropriate fin dall'inizio.

Informazioni sull'architettura di sicurezza IoT sono disponibili in [Architettura di sicurezza di Internet delle cose](/azure/iot-fundamentals/iot-security-architecture).

Questo articolo tratta gli argomenti seguenti:

* [La sicurezza inizia con un modello di rischio](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Sicurezza in IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Modellazione delle minacce nell'architettura di riferimento di Azure IoT](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>Sicurezza sin dall'inizio
Internet delle cose pone numerose sfide in termini di sicurezza, privacy e conformità per le aziende di tutto il mondo. A differenza delle tecnologie informatiche tradizionali in cui questi problemi riguardano il software e la relativa implementazione, l'IoT riguarda le sfide poste dalla convergenza tra il mondo fisico e quello informatico. Per proteggere le soluzioni IoT, è necessario garantire il provisioning sicuro dei dispositivi, la connettività protetta tra questi dispositivi e il cloud e la protezione dei dati nel cloud durante l'elaborazione e l'archiviazione. A sfavore di queste funzionalità, tuttavia, giocano fattori quali dispositivi vincolati alle risorse, la distribuzione geografica delle implementazioni e l'inclusione di numerosi dispositivi in un'unica soluzione.

Informazioni su come gestire la sicurezza in queste aree sono disponibili in [Sicurezza di Internet delle cose sin dall'inizio](/azure/iot-fundamentals/iot-security-ground-up).

L'articolo tratta gli argomenti seguenti:

* [Proteggere l'infrastruttura fin dall'inizio](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure: protezione dell'infrastruttura IoT aziendale](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Procedure consigliate
Proteggere un'infrastruttura IoT richiede una strategia di sicurezza rigorosa e approfondita. Dalla protezione dei dati nel cloud alla protezione dell'integrità dei dati in transito sulla rete Internet pubblica e al provisioning sicuro dei dispositivi, ogni strato aumenta il livello di sicurezza dell'intera infrastruttura.

Informazioni sulle procedure consigliate per la sicurezza di Internet delle cose sono disponibili in [Procedure consigliate per la sicurezza di Internet delle cose](/azure/iot-fundamentals/iot-security-best-practices).

L'articolo tratta gli argomenti seguenti:

* [Produttore/integratore di hardware IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [Sviluppatore di soluzioni IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [Distributore di soluzioni IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [Operatore di soluzioni IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
