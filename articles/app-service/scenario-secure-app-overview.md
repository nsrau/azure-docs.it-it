---
title: "Esercitazione: Creare un'app Web sicura nel Servizio app di Azure | Azure"
description: Questa esercitazione illustra come creare un'app Web usando il Servizio app di Azure, abilitare l'autenticazione, chiamare l'archiviazione di Azure e chiamare Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428326"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Esercitazione: Abilitare l'autenticazione nel Servizio app e accedere all'archiviazione e a Microsoft Graph

Questa esercitazione illustra uno scenario di applicazione comune e descrive le procedure per:

- [(A) Configurare l'autenticazione per un'app Web](scenario-secure-app-authentication-app-service.md) e limitare l'accesso agli utenti nell'organizzazione.
- [(B) Accedere in modo sicuro all'archiviazione di Azure](scenario-secure-app-access-storage.md) per conto dell'applicazione Web usando le identità gestite.
- (C) Accedere ai dati in Microsoft Graph [per conto dell'utente connesso](scenario-secure-app-access-microsoft-graph-as-user.md) o [per conto dell'applicazione Web](scenario-secure-app-access-microsoft-graph-as-app.md) usando le identità gestite.
- [Pulire le risorse](scenario-secure-app-clean-up-resources.md) create per questa esercitazione.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Scenari di applicazioni in Microsoft Identity Platform" border="false":::

Per iniziare, vedere come abilitare l'autenticazione per un'app Web.

> [!div class="nextstepaction"]
> [Configurare l'autenticazione per un'app Web](scenario-secure-app-authentication-app-service.md)
