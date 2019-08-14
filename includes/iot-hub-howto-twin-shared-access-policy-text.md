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
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912401"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

In questo articolo viene creato un servizio back-end che aggiunge le proprietà desiderate a un dispositivo gemello e quindi esegue una query nel registro delle identità per trovare tutti i dispositivi con le proprietà segnalate che sono state aggiornate di conseguenza. Il servizio deve disporre dell'autorizzazione **Connect** per modificare le proprietà desiderate di un dispositivo gemello e richiede l'autorizzazione di **lettura del registro** di sistema per eseguire query sul registro delle identità. Non sono presenti criteri di accesso condiviso predefiniti che contengono solo queste due autorizzazioni, quindi è necessario crearne uno.
