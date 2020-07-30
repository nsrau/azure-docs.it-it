---
title: File di inclusione
description: File di inclusione
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.openlocfilehash: d03d904de68720874ea175c95244ba80c586df82
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133852"
---
## <a name="change-the-model-update-frequency"></a>Modificare la frequenza di aggiornamento del modello

Nella pagina **Configurazione** della risorsa di personalizza esperienze nel portale di Azure impostare **Frequenza di aggiornamento del modello** su 10 secondi. Con questa breve durata il training del servizio verrà eseguito rapidamente e sarà possibile visualizzare il cambiamento dell'azione più alta in classifica per ogni iterazione.

![Modificare la frequenza di aggiornamento del modello](../media/settings/configure-model-update-frequency-settings.png)

Quando viene creata per la prima volta un'istanza di un ciclo di Personalizza esperienze, non è disponibile alcun modello, perché non sono state effettuate chiamate all'API per le ricompense da cui eseguire il training. Le chiamate a Classifica restituiranno uguali probabilità per ogni elemento. L'applicazione dovrà sempre classificare il contenuto usando l'output di RewardActionId.