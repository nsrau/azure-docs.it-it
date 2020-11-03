---
title: Residenza dei dati dei clienti in Azure IoT Central | Microsoft Docs
description: Questo articolo descrive la residenza dei dati dei clienti in Azure IoT Central applicazioni.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280689"
---
# <a name="azure-iot-central-customer-data-residency"></a>Residenza dei dati dei clienti di Azure IoT Central

IoT Central non archivia i dati del cliente all'esterno dell'area geografica specificata dal cliente, fatta eccezione per gli scenari seguenti:

- Quando un nuovo utente viene aggiunto a un'applicazione IoT Central esistente, l'ID di posta elettronica dell'utente può essere archiviato al di fuori della geografia finché l'utente invitato non accede all'applicazione per la prima volta.

- IoT Central le tessere mappa del dashboard usano le [mappe di Azure](../../azure-maps/about-azure-maps.md). Quando si aggiunge una tessera mappa a un'applicazione IoT Central esistente, i dati relativi alla posizione possono essere elaborati o archiviati in base alle regole di georilevazione del servizio mappe di Azure.
