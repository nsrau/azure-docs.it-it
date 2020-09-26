---
title: Configurare regole e azioni in Azure IoT Central | Microsoft Docs
description: Questo articolo di procedure illustra come gli autori possono configurare regole e azioni basate su dati di telemetria nell'applicazione Azure IoT Central.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8391202e991c240b03bd79b947c1c3c6dcc9afd7
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362538"
---
# <a name="configure-rules"></a>Configurare le regole

*Questo articolo è rivolto a operatori, autori e amministratori.*

Le regole in IoT Central fungono da strumento di risposta personalizzabile che attiva gli eventi monitorati attivamente dai dispositivi connessi. Le sezioni seguenti descrivono come vengono valutate le regole.

## <a name="select-target-devices"></a>Selezionare i dispositivi di destinazione

Usare la sezione Dispositivi di destinazione per selezionare il tipo di dispositivo a cui verrà applicata questa regola. I filtri consentono di limitare ulteriormente i dispositivi da includere. I filtri usano le proprietà nel modello di dispositivo per filtrare il set di dispositivi. I filtri in sé non attivano un'azione. Nello screenshot seguente i dispositivi di destinazione sono del tipo di modello di dispositivo **Refrigerator** (Frigorifero). Il filtro indica che la regola deve includere solo i **frigoriferi** la cui proprietà **Manufactured State** (Stato di produzione) è uguale a **Washington**.

![Condizioni](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Usare più condizioni

Le condizioni sono i fattori in base ai quali vengono attivate le regole. Attualmente, quando si aggiungono più condizioni a una regola, le varie condizioni sono unite in modo logico tramite l'operatore AND. In altre parole, tutte le condizioni devono essere soddisfatte affinché la regola restituisca true.  

Nello screenshot seguente le condizioni verificano quando la temperatura è superiore a 70 &deg;F e l'umidità è minore di 10. Quando entrambe le condizioni sono vere, la regola restituisce true e attiva un'azione.

![Screenshot mostra un monitoraggio frigorifero con le condizioni specificate per temperatura e umidità.](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>Usare una proprietà cloud in un campo Valore

È possibile fare riferimento a una proprietà cloud dal modello di dispositivo nel campo **Valore** di una condizione. Il valore della proprietà cloud e quello dei dati di telemetria devono essere di tipi simili. Se, ad esempio, **Temperatura** è un valore Double, nell'elenco a discesa **Valore** vengono visualizzate come opzioni solo le proprietà cloud di tipo Double.

Se si sceglie un valore di telemetria di tipo evento, l'elenco a discesa **Valore** include l'opzione **Qualsiasi**. L'opzione **Qualsiasi** indica che la regola viene attivata quando l'applicazione riceve un evento di quel tipo, indipendentemente dal payload.

## <a name="use-aggregate-windowing"></a>Usare gli intervalli di tempo di aggregazione

Le regole valutano gli intervalli di tempo di aggregazione come finestre a cascata. Nello screenshot seguente l'intervallo di tempo è di cinque minuti. Ogni cinque minuti la regola valuta gli ultimi cinque minuti di dati. I dati vengono valutati solo una volta nell'intervallo di tempo a cui corrispondono.

![Finestre a cascata](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Usare le regole con i moduli IoT Edge

Le regole applicate ai moduli IoT Edge sono soggette a una restrizione. Le regole sui dati di telemetria di moduli diversi non vengono valutate come regole valide. Ad esempio, si supponga che la prima condizione della regola siano i dati di telemetria della temperatura del modulo A e che la seconda condizione siano i dati di telemetria dell'umidità nel modulo B. Poiché le due condizioni provengono da moduli diversi, questo set di condizioni non è valido. La regola non è valida e genera un errore quando si prova a salvarla.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare una regola nell'applicazione Azure IoT Central, è possibile apprendere come [configurare le regole avanzate](howto-configure-rules-advanced.md) usando Power Automate o App per la logica di Azure.
