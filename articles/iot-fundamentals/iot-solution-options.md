---
title: Opzioni disponibili per le soluzioni Azure IoT (Internet of Things)
description: Linee guida per scegliere tra un approccio basato sui servizi della piattaforma o sulla piattaforma app gestita per la creazione di una soluzione IoT. Nell'approccio basato sui servizi della piattaforma come blocchi predefiniti si usano servizi quali l'hub IoT e Gemelli digitali. Nell'approccio basato sulla piattaforma app gestita si usa invece IoT Central per velocizzare il processo.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 02/05/2020
ms.author: dobett
ms.openlocfilehash: 046116addf643d6c0eb0faf2f821b6ef78d440c6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77050628"
---
# <a name="choose-the-right-iot-solution"></a>Scegliere la soluzione IoT appropriata

Per creare una soluzione IoT aziendale, è in genere possibile scegliere tra un approccio basato sui *servizi della piattaforma* e uno basato sulla *piattaforma app gestita*.

I servizi della piattaforma offrono i blocchi predefiniti per realizzare applicazioni IoT personalizzate e flessibili. Sono disponibili altre opzioni per scegliere e scrivere il codice relativo alla connessione dei dispositivi, nonché per inseriscono, archiviare e analizzare i dati. I servizi della piattaforma Azure IoT includono l'hub IoT di Azure e Gemelli digitali di Azure.

Una piattaforma app gestita consente di iniziare a creare app più rapidamente rispetto ai servizi della piattaforma riducendo il numero di decisioni necessarie per ottenere i risultati desiderati. La piattaforma app gestita si occupa della maggior parte degli elementi della soluzione, consentendo quindi di concentrarsi sull'aggiunta di informazioni sul settore e sulla scalabilità e la connessione dei dispositivi. Azure IoT Central è una piattaforma app gestita.

Per scegliere tra questi due approcci, è necessario prendere in considerazione gli aspetti seguenti:

- Modalità di gestione della soluzione.
- Livello di personalizzazione e controllo sulla soluzione.
- Struttura dei prezzi desiderata.

## <a name="management"></a>Gestione

A quali elementi del sistema di gestione si intende dedicare tempo e risorse? 

- Scegliere l'approccio basato sui servizi della piattaforma per avere il controllo completo sui servizi sottostanti nella soluzione, ad esempio se si vuole:

    - Gestire scalabilità e protezione dei servizi per soddisfare esigenze specifiche.
    - Usare le competenze interne o dei partner per eseguire l'onboarding dei dispositivi ed effettuare il provisioning dei servizi.

- Scegliere l'approccio basato sulla piattaforma app gestita per sfruttare i vantaggi di una piattaforma che si occupa della scalabilità, della sicurezza e della gestione di applicazioni e dispositivi IoT.

## <a name="control"></a>Controllo

Quali elementi della soluzione si intende personalizzare?

- Scegliere l'approccio basato sui servizi della piattaforma per una personalizzazione e un controllo totale sull'architettura della soluzione.

- Scegliere l'approccio basato sulla piattaforma app gestita per controllare la personalizzazione, i dashboard, i ruoli utente, i dispositivi e i dati di telemetria, senza occuparsi, però, del carico di gestione del sistema IoT sottostante.

## <a name="pricing"></a>Prezzi

Quale struttura dei prezzi è più adatta alle proprie esigenze?

- Scegliere l'approccio basato sui servizi della piattaforma per ottimizzare i servizi e controllare i costi complessivi.

- Scegliere l'approccio basato sulla piattaforma app gestita per una struttura dei prezzi semplice e prevedibile.

## <a name="summary"></a>Summary

L'approccio basato sui servizi della piattaforma è ideale per un'azienda che conosce già soluzioni e dispositivi cloud e vuole:

- Ottimizzare i servizi della soluzione.
- Disporre di un elevato livello di controllo sui servizi della soluzione.
- Personalizzare completamente la soluzione.

L'approccio basato sulla piattaforma app gestita è ideale per un'azienda che:

- Non intende dedicare un elevato numero di risorse alla progettazione, allo sviluppo e alla gestione del sistema.
- Vuole una struttura dei prezzi prevedibile.
- Vuole usufruire di alcune funzionalità di personalizzazione.

## <a name="next-steps"></a>Passaggi successivi

Per una spiegazione più completa dei diversi servizi e piattaforme e del modo in cui vengono usati, vedere [Servizi e tecnologie di Azure IoT](iot-services-and-technologies.md).

Per altre informazioni sugli attributi chiave delle soluzioni IoT, vedere il whitepaper sugli [otto attributi delle soluzioni IoT di successo](https://aka.ms/8attributes).

Per un'analisi approfondita dell'architettura IoT, vedere [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Architettura di riferimento di Microsoft Azure IoT).
