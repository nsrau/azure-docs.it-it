---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471532"
---
Se si vuole che agli utenti venga richiesto un secondo fattore di autenticazione prima di concedere l'accesso, è possibile configurare Azure Multi-Factor Authentication (MFA). È possibile configurare l'autenticazione a più fattori in base all'utente oppure sfruttare l'autenticazione a più fattori tramite [l'accesso condizionale](../articles/active-directory/conditional-access/overview.md).

* L'autenticazione a più fattori per utente può essere abilitata senza costi aggiuntivi. Quando si abilita l'autenticazione a più fattori per utente, all'utente verrà richiesta l'autenticazione del secondo fattore per tutte le applicazioni legate al tenant di Azure AD. Vedere [l'opzione 1](#peruser) per i passaggi.
* L'accesso condizionale consente un controllo più preciso sulla modalità di promozione di un secondo fattore. Può consentire l'assegnazione dell'autenticazione a più fattori solo alla VPN ed escludere altre applicazioni legate al tenant di Azure AD. Vedere [l'opzione 2](#conditional) per i passaggi.