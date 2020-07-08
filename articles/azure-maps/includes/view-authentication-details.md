---
title: Visualizzare i dettagli di autenticazione delle mappe di Azure
description: Usare il portale di Azure per visualizzare i dettagli di autenticazione delle mappe di Azure.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988435"
---
È possibile visualizzare i dettagli di autenticazione dell'account Azure Maps nell'portale di Azure. Nell'account scegliere **autenticazione**dal menu **Impostazioni** .

![Dettagli di autenticazione](../media/how-to-manage-authentication/how-to-view-auth.png)

Una volta creato un account Azure Maps, il valore Azure Maps `x-ms-client-id` è presente nella pagina dei dettagli di autenticazione portale di Azure. Questo valore rappresenta l'account che verrà usato per le richieste dell'API REST. Questo valore deve essere archiviato nella configurazione dell'applicazione e recuperato prima di effettuare richieste HTTP quando si usa l'autenticazione Azure AD con le mappe di Azure.
