---
title: Modelli di consumo di veicoli per il routing Mappe di Microsoft Azure
description: In this article, you'll learn about vehicle consumption models for routing in Microsoft Azure Maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190242"
---
# <a name="consumption-model"></a>Modello di utilizzo

Il servizio Routing fornisce una serie di parametri per una descrizione dettagliata del modello di consumo specifico del veicolo.
A seconda del valore di **vehicleEngineType**, sono supportati due modelli di consumo principali: _Combustione_ e _Elettrico_. Non è corretto specificare parametri che appartengono a modelli diversi nella stessa richiesta. Inoltre, i parametri del modello di consumo non possono essere utilizzati con i seguenti valori **travelMode:** _bicycle_ e _pedestrian_.

## <a name="parameter-constraints-for-consumption-model"></a>Vincoli dei parametri per il modello di consumo

In entrambi i modelli di consumo, esistono alcune dipendenze quando si specificano i parametri. Ciò significa che, specificare in modo esplicito alcuni parametri può richiedere la specifica di alcuni altri parametri. Di seguito sono riportate le dipendenze da tenere presenti:Here are these dependencies to be aware of:

* Tutti i parametri richiedono che l'utente specifichi **constantSpeedConsumption**. È un errore specificare qualsiasi altro parametro del modello di consumo, se **costanteSpeedConsumption** non è specificato. Il **parametro vehicleWeight** è un'eccezione per questo requisito.
* **accelerationEfficiency** e **decelerationEfficiency** deve sempre essere specificato come una coppia (vale a dire, entrambi o nessuno).
* Se vengono specificati **accelerationEfficiency** e **decelerationEfficiency**, il prodotto dei loro valori non deve essere maggiore di 1 per evitare il moto perpetuo.
* **uphillEfficiency** e **downhillEfficiency** devono sempre essere specificati come una coppia (ovvero, entrambi o nessuno).
* Se vengono specificati **uphillEfficiency** e **downhillEfficiency** , il prodotto dei loro valori non deve essere maggiore di 1 per evitare il moto perpetuo.
* Se l'utente specifica i parametri \*__Efficiency__, è necessario specificare anche **vehicleWeight**. Quando **vehicleEngineType** è _combustion_, è necessario specificare anche **fuelEnergyDensityInMJoulesPerLiter**.
* **maxChargeInkWh** e **currentChargeInkWh** devono essere sempre specificati come una coppia (ovvero entrambi o nessuno).

> [!NOTE]
> Se viene specificato solo **constantSpeedConsumption**, per il calcolo dei consumi non vengono presi in considerazione altri aspetti correlati ai consumi, quali le pendenze e l'accelerazione del veicolo.

## <a name="combustion-consumption-model"></a>Modello consumo Combustione

Si usa il modello di consumo Combustione quando **vehicleEngineType** è impostato su _combustion_.
I parametri che appartengono a questo modello sono riportati di seguito. Vedere la sezione Parametri per una descrizione dettagliata.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Modello di consumo Elettrico

Si usa il modello di consumo Elettrico quando **vehicleEngineType** è impostato su _electric_.
I parametri che appartengono a questo modello sono riportati di seguito. Vedere la sezione Parametri per una descrizione dettagliata.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Valori sensibili dei parametri di consumo

Un particolare set di parametri di consumo può essere rifiutato, anche se il set potrebbe soddisfare tutti i requisiti espliciti. Succede quando il valore di un parametro specifico, o una combinazione di valori di diversi parametri, è considerato portare a dimensioni irragionevoli dei valori di consumo. Se ciò accade, è molto probabile che ci sia un errore di input, in quanto tutti i valori sensibili dei parametri di consumo vengono generalmente presi in considerazione. Nel caso in cui un determinato set di parametri di consumo venga rifiutato, il messaggio di errore visualizzato conterrà una spiegazione testuale dei motivi.
Le descrizioni dettagliate dei parametri contengono esempi di valori sensibili per entrambi i modelli.
