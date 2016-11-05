---
title: Informazioni generali sulla sicurezza di Internet delle cose | Microsoft Docs
description: " I servizi di Azure IoT (Internet delle cose) offrono una vasta gamma di funzionalità. Questo articolo illustra come proteggere le soluzioni IoT in Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh

ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2016
ms.author: terrylan

---
# Informazioni generali sulla sicurezza di Internet delle cose
I servizi di Azure IoT (Internet delle cose) offrono una vasta gamma di funzionalità. Questi servizi di livello aziendale consentono di:

* Raccogliere dati dai dispositivi
* Analizzare i flussi dei dati in movimento
* Archiviare ed eseguire query su set di dati di grandi dimensioni
* Visualizzare i dati in tempo reale e cronologici
* Eseguire l'integrazione con i sistemi back-office

Per offrire queste funzionalità, Azure IoT Suite include più servizi di Azure con estensioni personalizzate come soluzioni preconfigurate. Queste soluzioni preconfigurate sono implementazioni di base dei modelli di soluzione IoT comuni che contribuiscono a ridurre i tempi di distribuzione delle soluzioni IoT. I Software Development Kit IoT consentono di personalizzare ed estendere queste soluzioni per soddisfare le proprie esigenze. È anche possibile usare queste soluzioni come esempi o modelli durante lo sviluppo di nuove soluzioni IoT.

Azure IoT Suite è una potente soluzione per le esigenze di IoT. È tuttavia fondamentale che le soluzioni IoT siano progettate sin dall'inizio pensando alla sicurezza. A causa del numero elevato di dispositivi IoT, qualsiasi evento imprevisto riguardante la sicurezza può diventare rapidamente un evento esteso con conseguenze significative.

Le informazioni seguenti sono utili per comprendere come proteggere le soluzioni IoT.

## Architettura di sicurezza
Quando si progetta un sistema, è importante comprendere le potenziali minacce e aggiungere le difese appropriate di conseguenza, perché il sistema è definito da una progettazione e un'architettura specifiche. È particolarmente importante progettare il prodotto tenendo conto della sicurezza, perché comprendere in che modo un utente malintenzionato potrebbe compromettere un sistema aiuta ad implementare le misure appropriate sin dall'inizio.

Informazioni sull'architettura di sicurezza IoT sono disponibili in [Architettura di sicurezza di Internet delle cose](../iot-suite/iot-security-architecture.md).

Questo articolo tratta gli argomenti seguenti:

* [La sicurezza inizia con un modello di rischio](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
* [Sicurezza in IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
* [Modellazione delle minacce nell'architettura di riferimento di Azure IoT](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## Sicurezza sin dall'inizio
Internet delle cose pone numerose sfide in termini di sicurezza, privacy e conformità per le aziende di tutto il mondo. A differenza delle tecnologie informatiche tradizionali in cui questi problemi riguardano il software e la relativa implementazione, l'IoT riguarda le sfide poste dalla convergenza tra il mondo fisico e quello informatico. Per proteggere le soluzioni IoT, è necessario garantire il provisioning sicuro dei dispositivi, la connettività protetta tra questi dispositivi e il cloud e la protezione dei dati nel cloud durante l'elaborazione e l'archiviazione. A sfavore di queste funzionalità, tuttavia, giocano fattori quali dispositivi con risorse limitate, la distribuzione geografica delle implementazioni e un numero elevato di dispositivi all'interno di un'unica soluzione.

Informazioni su come gestire la sicurezza in queste aree sono disponibili in [Sicurezza di Internet delle cose sin dall'inizio](../iot-suite/securing-iot-ground-up.md).

L'articolo tratta gli argomenti seguenti:

* [Proteggere l'infrastruttura fin dall'inizio](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure: protezione dell'infrastruttura IoT aziendale](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## Procedure consigliate
Proteggere un'infrastruttura IoT richiede una strategia di sicurezza rigorosa e approfondita. Dalla protezione dei dati nel cloud, alla messa in sicurezza dell'integrità dei dati in transito sulla rete Internet pubblica e alla capacità di eseguire in modo sicuro il provisioning dei dispositivi, ogni strato aumenta il livello di sicurezza dell'intera infrastruttura.

Informazioni sulle procedure consigliate per la sicurezza di Internet delle cose sono disponibili in [Procedure consigliate per la sicurezza di Internet delle cose](../iot-suite/iot-security-best-practices.md).

L'articolo tratta gli argomenti seguenti:

* [Produttore/integratore di hardware IoT](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [Sviluppatore di soluzioni IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
* [Distributore di soluzioni IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
* [Operatore di soluzioni IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)

<!---HONumber=AcomDC_0810_2016-->