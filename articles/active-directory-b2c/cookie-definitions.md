---
title: Definizioni dei cookie-Azure Active Directory B2C | Microsoft Docs
description: Fornisce le definizioni per i cookie utilizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930903"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Definizioni dei cookie per Azure Active Directory B2C

Nella tabella seguente sono elencati i cookie utilizzati in Azure Active Directory B2C.

| Nome | Dominio | Scadenza | Finalità |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Fine della [sessione del browser](session-behavior.md) | Include i dati di appartenenza degli utenti tra i tenant. I tenant di cui un utente è membro e il livello di appartenenza (amministratore o utente). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Utilizzato per indirizzare le richieste all'istanza di produzione appropriata. |
| x-ms-CPIM-Trans | login.microsoftonline.com, b2clogin.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Utilizzato per tenere traccia delle transazioni (numero di richieste di autenticazione a Azure AD B2C) e della transazione corrente. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Utilizzato per la gestione della sessione SSO. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md), autenticazione riuscita | Utilizzato per mantenere lo stato della richiesta. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Richiesta tra siti: token di falsificazione usato per la protezione tipo. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Usato per Azure AD B2C il routing di rete. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Context |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Usato per archiviare i dati di appartenenza per il tenant del provider di risorse. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Utilizzato per archiviare il cookie di inoltro. |
