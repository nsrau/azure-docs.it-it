---
title: Modelli di consumo del veicolo per il routing | Mappe Microsoft Azure
description: Questo articolo illustra i modelli di consumo dei veicoli per il routing in Microsoft Azure maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77190242"
---
# <a name="consumption-model"></a>Modello di utilizzo

Il servizio di routing fornisce un set di parametri per una descrizione dettagliata del modello a consumo specifico del veicolo.
A seconda del valore di **vehicleEngineType**, sono supportati due modelli di consumo principali: _Combustione_ e _Elettrico_. Non è corretto specificare parametri che appartengono a modelli diversi nella stessa richiesta. Inoltre, i parametri del modello a consumo non possono essere usati con i valori **travelMode** seguenti: _bicicletta_ e _pedonale_.

## <a name="parameter-constraints-for-consumption-model"></a>Vincoli dei parametri per il modello di consumo

In entrambi i modelli a consumo sono presenti alcune dipendenze quando si specificano i parametri. Il che significa che, specificando alcuni parametri in modo esplicito, potrebbe essere necessario specificare altri parametri. Di seguito sono riportate le dipendenze di cui tenere conto:

* Tutti i parametri richiedono che l'utente specifichi **constantSpeedConsumption**. Non è possibile specificare un altro parametro del modello a consumo, se **constantSpeedConsumption** non è specificato. Il parametro **vehicleWeight** è un'eccezione per questo requisito.
* **accelerationEfficiency** e **decelerationEfficiency** devono essere sempre specificati come coppia (ovvero, entrambi o nessuno).
* Se vengono specificati **accelerationEfficiency** e **decelerationEfficiency**, il prodotto dei loro valori non deve essere maggiore di 1 per evitare il moto perpetuo.
* **uphillEfficiency** e **downhillEfficiency** devono essere sempre specificati come coppia (ovvero, both o None).
* Se vengono specificati **uphillEfficiency** e **downhillEfficiency** , il prodotto dei loro valori non deve essere maggiore di 1 per evitare il moto perpetuo.
* Se l'utente specifica i parametri \*__Efficiency__, è necessario specificare anche **vehicleWeight**. Quando **vehicleEngineType** è _combustion_, è necessario specificare anche **fuelEnergyDensityInMJoulesPerLiter**.
* **maxChargeInkWh** e **currentChargeInkWh** devono essere sempre specificati come coppia (ovvero, entrambi o nessuno).

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

Un particolare set di parametri di consumo può essere rifiutato, anche se il set può soddisfare tutti i requisiti espliciti. Si verifica quando il valore di un parametro specifico, o una combinazione di valori di diversi parametri, viene considerato come un'entità di valori di consumo non ragionevole. Se ciò accade, è molto probabile che ci sia un errore di input, in quanto tutti i valori sensibili dei parametri di consumo vengono generalmente presi in considerazione. Nel caso in cui un determinato set di parametri di consumo venga rifiutato, il messaggio di errore visualizzato conterrà una spiegazione testuale dei motivi.
Le descrizioni dettagliate dei parametri contengono esempi di valori sensibili per entrambi i modelli.
