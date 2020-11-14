---
title: includere file
description: includere file
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628025"
---
È possibile usare l'implementazione della cache dei token di MSAL per consentire a app in background, API e servizi di usare la cache dei token di accesso per continuare a agire per conto degli utenti in assenza. Questa operazione è particolarmente utile se le app e i servizi in background devono continuare a lavorare per conto dell'utente dopo che l'utente ha terminato l'app Web front-end.

Attualmente, la maggior parte dei processi in background usa le [autorizzazioni dell'applicazione](/graph/auth/auth-concepts#microsoft-graph-permissions) quando è necessario lavorare con i dati di un utente senza che siano presenti per l'autenticazione o la riautenticazione. Poiché le autorizzazioni dell'applicazione spesso richiedono il consenso dell'amministratore, che richiede l'elevazione dei privilegi, si verifica un attrito inutile perché lo sviluppatore non ha intenzione di ottenere l'autorizzazione oltre a quella che l'utente ha inizialmente acconsentito per la propria app.

Questo esempio di codice su GitHub illustra come evitare questo attrito non necessario accedendo alla cache dei token di MSAL dalle app in background:

 [Accesso alla cache dei token dell'utente connesso da app, API e servizi in background](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)