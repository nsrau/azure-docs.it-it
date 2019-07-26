---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404007"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

In questo articolo viene creato un servizio back-end che aggiunge le proprietà desiderate a un dispositivo gemello e quindi esegue una query nel registro delle identità per trovare tutti i dispositivi con le proprietà segnalate che sono state aggiornate di conseguenza. Il servizio deve disporre dell'autorizzazione **Connect** per modificare le proprietà desiderate di un dispositivo gemello e richiede l'autorizzazione di **lettura del registro** di sistema per eseguire query sul registro delle identità. Non sono presenti criteri di accesso condiviso predefiniti che contengono solo queste due autorizzazioni, quindi è necessario crearne uno.
