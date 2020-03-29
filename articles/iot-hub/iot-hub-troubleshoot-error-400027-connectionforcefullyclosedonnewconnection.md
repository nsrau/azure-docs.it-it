---
title: Risoluzione dei problemi dell'errore dell'hub IoT di Azure 400027 ConnectionForcefullyClosedOnNewConnection
description: Informazioni su come correggere l'errore 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960529"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

In questo articolo vengono descritte le cause e le soluzioni per **400027 ConnectionForcefullyClosedOnNewConnection** errori.

## <a name="symptoms"></a>Sintomi

L'operazione di doppia da dispositivo a cloud (ad esempio le proprietà segnalate di lettura o patch) o la chiamata diretta al metodo ha esito negativo con il codice di errore **400027**.

## <a name="cause"></a>Causa

Un altro client ha creato una nuova connessione all'hub IoT usando le stesse credenziali, pertanto l'hub IoT ha chiuso la connessione precedente. L'hub IoT non consente a più di un client di connettersi usando lo stesso set di credenziali.

## <a name="solution"></a>Soluzione

Assicurati che ogni client si connetta all'hub IoT usando la propria identità.