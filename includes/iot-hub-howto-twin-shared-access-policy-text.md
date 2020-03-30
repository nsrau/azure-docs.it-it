---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050415"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

In questo articolo viene creato un servizio back-end che aggiunge le proprietà desiderate a un dispositivo gemello e quindi si esegue una query nel Registro di sistema delle identità per trovare tutti i dispositivi con proprietà segnalate che sono state aggiornate di conseguenza. Il servizio necessita dell'autorizzazione di connessione del **servizio** per modificare le proprietà desiderate di un dispositivo gemello e richiede l'autorizzazione di **lettura del Registro** di sistema per eseguire query sul Registro di sistema delle identità. Non esiste un criterio di accesso condiviso predefinito che contiene solo queste due autorizzazioni, pertanto è necessario crearne una.
