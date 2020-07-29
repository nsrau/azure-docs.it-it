---
title: Risoluzione dei problemi dell'hub Azure Internet Error 400027 ConnectionForcefullyClosedOnNewConnection
description: Informazioni su come correggere l'errore 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960529"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Questo articolo descrive le cause e le soluzioni per gli errori di **400027 ConnectionForcefullyClosedOnNewConnection** .

## <a name="symptoms"></a>Sintomi

L'operazione del dispositivo gemello da dispositivo a cloud (ad esempio, le proprietà di lettura o di correzione) o la chiamata diretta al metodo ha esito negativo con il codice di errore **400027**.

## <a name="cause"></a>Causa

Un altro client ha creato una nuova connessione all'hub Internet con le stesse credenziali, quindi l'hub Internet ha chiuso la connessione precedente. L'hub Internet delle cose non consente a più di un client di connettersi usando lo stesso set di credenziali.

## <a name="solution"></a>Soluzione

Verificare che ogni client si connetta all'hub Internet con la propria identità.