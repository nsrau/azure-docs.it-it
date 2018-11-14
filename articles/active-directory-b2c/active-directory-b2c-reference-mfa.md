---
title: Multi-Factor Authentication in Azure Active Directory B2C | Microsoft Docs
description: Come abilitare la Multi-Factor Authentication in applicazioni rivolte agli utenti finali protette da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: eabae0f3575719c6cb93affefe0a393dd13d1439
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014007"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Abilitare l'autenticazione a più fattori in Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C si integra direttamente con [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) per consentire l'aggiunta di un secondo livello di protezione alle esperienze di iscrizione e di accesso nelle applicazioni. È possibile abilitare l'autenticazione a più fattori senza scrivere una singola riga di codice. Se i criteri di iscrizione e accesso sono già stati creati, è ancora possibile abilitare l'autenticazione a più fattori.

Questa funzionalità risulta utile per la gestione di scenari simili ai seguenti:

- L'autenticazione a più fattori non viene richiesta per accedere a una sola applicazione, ma viene richiesta per accedere a un'ulteriore applicazione. Ad esempio, l'utente può accedere a un'applicazione di assicurazione auto con un account locale o di social networking, ma deve verificare il numero di telefono prima di accedere all'applicazione di assicurazione casa registrata nella stessa directory.
- L'autenticazione a più fattori non viene richiesta per accedere a un'applicazione in generale, ma viene richiesta per l'accesso alle aree sensibili. Ad esempio, l'utente può accedere a un'applicazione bancaria con un account locale o di social networking e controllare il saldo del conto, ma deve verificare il numero di telefono prima di effettuare un bonifico.

## <a name="set-multi-factor-authentication"></a>Impostare l'autenticazione a più fattori

Quando si crea un criterio, è possibile abilitare l'autenticazione a più fattori.

![Impostare l'autenticazione a più fattori](./media/active-directory-b2c-reference-mfa/add-policy.png)

Impostare **Stato** su **On**.

È possibile usare **Esegui ora** nei criteri per verificare l'esperienza. Confermare lo scenario seguente:

Un account utente viene creato nel tenant prima che venga eseguito il passaggio di autenticazione a più fattori. Durante il passaggio, all'utente viene richiesto di fornire un numero di telefono e di verificarlo. Se la verifica ha esito positivo, il numero di telefono viene associato all'account per un utilizzo successivo. Anche se l'utente annulla o esce, al successivo accesso può venire richiesto all'utente di verificare nuovamente un numero di telefono (con l'autenticazione a più fattori abilitata).

## <a name="add-multi-factor-authentication"></a>Aggiungere l'autenticazione a più fattori

È possibile abilitare l'autenticazione a più fattori in un criterio precedentemente creato. 

Per abilitare l'autenticazione a più fattori:

1. Aprire il criterio e quindi selezionare **Modifica**. 
2. Selezionare **Autenticazione a più fattori**
3. Impostare **Stato** su **On**.
4. Fare clic su **Salva** nella parte superiore della pagina.


