---
title: Architettura di Azure FarmBeats
description: Descrive l'architettura di Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8eaba4c2a987f47c56169af138ef380835d56740
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797985"
---
# <a name="integration-patterns"></a>Modelli di integrazione

Azure FarmBeats è un'offerta business-to-business, disponibile in Azure Marketplace. FarmBeats consente l'aggregazione dei set di dati di agricoltura tra i provider e la generazione di informazioni dettagliate di utilità pratica mediante la creazione di modelli di intelligenza artificiale (AI) o di Machine Learning (ML) fondendo i set di dati.

![Beat Farm progetto](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Le sezioni seguenti descrivono il modello di integrazione per Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Perché eseguire l'integrazione con Azure FarmBeats?

Questa sezione è incentrata sui partner che vogliono integrare i propri sistemi di dati (ad esempio sensori, droni, stazioni meteo) in Azure FarmBeats.

Azure FarmBeats è un'offerta estendibile, che consente alle aziende agricole di aggiungere i set di set di impostazioni cronologici e in tempo reale diversi in un'unica piattaforma. Azure FarmBeats consente a un'azienda agricola di normalizzare, contestualizzare e aggregare i dati nel contesto di una farm.

Diventando un partner dati con Azure FarmBeats, è possibile aprire i sistemi a un'adozione più ampia e rivolgersi a più clienti con le offerte di dati. Azure FarmBeats offre un livello API estensibile denominato Data Hub, che consente di inserire i dati dai dispositivi in modo sistematico e in uno schema standardizzato.

Quando i dati sono disponibili nell'istanza di Azure FarmBeats dei clienti, i clienti possono creare strumenti e analisi più completi sui dati.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione dei dati dei sensori, vedere [integrazione dei dati dei sensori](sensor-partner-integration.md) e per l'integrazione con i partner di immagini, vedere [integrazione del partner immagini](imagery-partner-integration.md).
