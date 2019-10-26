---
title: Cenni preliminari sulla trasformazione
description: Informazioni su come trasformare
author: viv-liu
ms.author: viviali
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 629ae6f0e8b50eae5e791cafa6c67b638685ecb3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952511"
---
# <a name="transform-your-iot-data-preview-features"></a>Trasformare i dati di Internet delle cose (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In qualità di piattaforma applicativa, IoT Central offre diversi facet chiave che consentono di trasformare i dati di Internet delle cose in informazioni aziendali che consentono di ottenere risultati di utilità pratica. IoT Central offre modi per estendere i dati delle cose Internet ai sistemi esterni per creare integrazioni con applicazioni line-of-business e applicazioni personalizzate. È possibile monitorare l'integrità e le operazioni del dispositivo creando regole che inviano notifiche ai tecnici tramite un servizio mobile. È possibile generare informazioni aziendali specifiche con analisi personalizzate e Machine Learning esportando i dati di telemetria non elaborati nei servizi di Azure. È possibile creare servizi e strumenti per monitorare e controllare i dispositivi e gestire l'app IoT Central usando le API pubbliche. 

![Cenni preliminari sulla trasformazione in IoT Central](media/overview-iot-central-transform/transform.PNG)

## <a name="monitor-device-health-and-operations-using-rules"></a>Monitorare lo stato e le operazioni del dispositivo mediante le regole
Quando i computer sono connessi e inviano i dati, è possibile identificare i computer in cui si verificano problemi o inviare messaggi di errore in modo che possano essere corretti per l'esecuzione normale con tempi di inattività minimi. È possibile creare regole nell'app IoT Central per monitorare i dati di telemetria che i dispositivi inviano e ricevere un avviso quando una soglia è stata superata oppure è stato inviato un messaggio di evento specifico. È possibile configurare azioni come le azioni di posta elettronica e i webhook per le regole in modo da notificare alle persone corrette e ai sistemi downstream appropriati. Altre informazioni sulle regole sono disponibili qui.

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Eseguire l'analisi e l'elaborazione personalizzate sui dati esportati
È possibile generare informazioni dettagliate aziendali altamente personalizzate, ad esempio determinare le tendenze di efficienza del computer o prevedere un utilizzo futuro dell'energia in una fabbrica, creando pipeline di analisi personalizzate per elaborare i dati di telemetria degli elementi non elaborati e archiviare il risultato finale. È possibile creare esportazioni di dati nell'app IoT Central per esportare i dati di telemetria, le proprietà e il ciclo di vita del dispositivo e le informazioni sulle modifiche del modello di dispositivo in altri servizi di Azure, in modo da poter analizzare, archiviare e visualizzare i dati negli strumenti più utili. Altre informazioni sull'esportazione dei dati sono disponibili qui.

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>Creazione di soluzioni e integrazioni di Internet per l'uso delle API
Puoi creare soluzioni di Internet delle cose come app per dispositivi mobili che consentono di controllare in remoto i dispositivi e di configurare nuovi dispositivi o integrazioni personalizzate con le applicazioni line-of-business esistenti per interagire con i dispositivi e i dati dell'it personalizzati per la tua azienda. È possibile usare IoT Central come backbone per la modellazione dei dispositivi, l'onboarding, la gestione e l'accesso ai dati. Scopri di più sull'API pubblica in questo modulo di apprendimento.
