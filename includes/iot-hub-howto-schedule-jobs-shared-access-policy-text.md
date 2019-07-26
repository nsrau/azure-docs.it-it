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
ms.openlocfilehash: 47d46cf4b400e29de8c526f750a10f57d6400220
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402678"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

In questo articolo viene creato un servizio back-end che pianifica un processo per richiamare un metodo diretto in un dispositivo, pianifica un processo per l'aggiornamento del dispositivo gemello e monitora lo stato di ogni processo. Per eseguire queste operazioni, il servizio richiede le autorizzazioni di **lettura** del registro di sistema e di **scrittura del registro** di sistema. Per impostazione predefinita, ogni hub tutto viene creato con un criterio di accesso condiviso denominato **registryReadWrite** che concede queste autorizzazioni.
