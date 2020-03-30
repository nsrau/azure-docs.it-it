---
title: Azure FarmBeats Architecture
description: Descrive l'architettura di Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482374"
---
# <a name="integration-patterns"></a>Modelli di integrazione

Azure FarmBeats è un'offerta business-to-business, disponibile in Azure Marketplace. FarmBeats consente l'aggregazione di set di dati di agricoltura tra i provider e la generazione di informazioni utili creando modelli di Intelligenza Artificiale (AI) o Machine Learning (ML) fondendo i set di dati.

![Progetto FarmBeats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

The following sections describe the integration pattern for Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Perché integrarsi con Azure FarmBeats?

Questa sezione è incentrata sui partner che desiderano integrare i propri sistemi di dati (come sensori, droni, stazioni meteorologiche) in Azure FarmBeats.

Azure FarmBeats è un'offerta estensibile, che consente alle aziende agricole di aggiungere i diversi set di dati agricoli storici e in tempo reale in un'unica piattaforma. Azure FarmBeats consente a un'azienda agricola di normalizzare, contestualizzare e aggregare i dati nel contesto di una farm.

Diventando un partner di dati con Azure FarmBeats, è possibile aprire i sistemi per un'adozione più ampia e raggiungere un maggior numero di clienti con le offerte di dati. Azure FarmBeats offre un livello API estensibile denominato Datahub, che consente di inserire i dati dai dispositivi in modo sistematico e in uno schema standardizzato.

Quando i dati sono disponibili nell'istanza di Azure FarmBeats dei clienti, i clienti possono creare strumenti e analisi più dettagliati sui dati.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'integrazione dei dati dei sensori, vedere [Integrazione dei dati dei sensori](sensor-partner-integration-in-azure-farmbeats.md) e per l'integrazione dei partner di immagini, vedere [Integrazione dei partner](imagery-partner-integration-in-azure-farmbeats.md)di immagini .
