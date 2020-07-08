---
title: includere file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70050415"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

In questo articolo viene creato un servizio back-end che aggiunge le proprietà desiderate a un dispositivo gemello e quindi esegue una query nel registro delle identità per trovare tutti i dispositivi con le proprietà segnalate che sono stati aggiornati di conseguenza. Il servizio richiede l’autorizzazione di **connessione al servizio** per modificare le proprietà desiderate di un dispositivo gemello ed è necessaria l’autorizzazione di **lettura del registro di sistema** per eseguire query sul registro delle identità. Non esistono criteri di accesso condiviso predefiniti che contengono solo queste due autorizzazioni, quindi è necessario crearne uno.
