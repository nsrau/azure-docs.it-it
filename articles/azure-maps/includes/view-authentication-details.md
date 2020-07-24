---
title: Visualizzare i dettagli di autenticazione delle mappe di Azure
description: Usare il portale di Azure per visualizzare i dettagli di autenticazione delle mappe di Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6ffcbf5a8c36958dd3ea74de4d826fe25a1139c
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126457"
---
È possibile visualizzare i dettagli di autenticazione dell'account Azure Maps nell'portale di Azure. Nell'account scegliere **autenticazione**dal menu **Impostazioni** .

![Dettagli di autenticazione](../media/how-to-manage-authentication/how-to-view-auth.png)

Una volta creato un account Azure Maps, il valore Azure Maps `x-ms-client-id` è presente nella pagina dei dettagli di autenticazione portale di Azure. Questo valore rappresenta l'account che verrà usato per le richieste dell'API REST. Questo valore deve essere archiviato nella configurazione dell'applicazione e recuperato prima di effettuare richieste HTTP quando si usa l'autenticazione Azure AD con le mappe di Azure.
