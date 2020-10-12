---
title: includere file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "70049000"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

In questo articolo viene creato un servizio back-end che pianifica un processo per richiamare un metodo diretto in un dispositivo, pianifica un processo per l'aggiornamento del dispositivo gemello e monitora lo stato di ogni processo. Per eseguire queste operazioni, è necessario che il servizio disponga delle autorizzazioni di **lettura del registro di sistema** e **scrittura del registro di sistema**. Per impostazione predefinita, ogni hub IoT viene creato con un **registryReadWrite** con nome di criteri di accesso condiviso che concede tale autorizzazione.i.
