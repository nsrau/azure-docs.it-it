---
title: includere file
description: includere file
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005632"
---
## <a name="message-headers"></a>Intestazioni del messaggio
Queste sono le proprietà ricevute nelle intestazioni dei messaggi:

| Nome proprietà | Descrizione |
| ------------- | ----------- | 
| aeg-subscription-name | Nome della sottoscrizione di eventi. |
| aeg-delivery-count | Numero di tentativi effettuati per l'evento. |
| aeg-event-type | <p>Tipo dell'evento.</p><p>Può essere uno dei valori seguenti:</p><ul><li>SubscriptionValidation</li><li>Notifica</li><li>SubscriptionDeletion</li></ul> | 
| aeg-metadata-version | <p>Versione dei metadati dell'evento.<p> Per lo **schema di eventi di Griglia di eventi**, questa proprietà rappresenta la versione dei metadati e per lo **schema di eventi cloud** rappresenta la **versione della specifica**. </p>|
| aeg-data-version | <p>Versione dei dati dell'evento.</p><p>Per lo **schema di eventi di Griglia di eventi**, questa proprietà rappresenta la versione dei dati e per lo **schema di eventi cloud** non è applicabile.</p> |
| aeg-output-event-id | ID dell'evento di Griglia di eventi. |


