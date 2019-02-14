---
title: Come escludere password in Azure AD
description: Escludere password non appropriate dall'ambiente con le password escluse dinamicamente di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30627994a8fe8cfe73e7945dadf2153ea60860b8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175133"
---
# <a name="configuring-the-custom-banned-password-list"></a>Configurazione dell'elenco personalizzato di password escluse

|     |
| --- |
| La protezione password di Azure AD è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Molte organizzazioni riscontrano spesso il fatto che gli utenti creano password usando parole locali comuni, come una scuola, una squadra sportiva o una persona famosa, rendendole facili da indovinare. Oltre all'elenco globale di password escluse, l'elenco personalizzato di password escluse permette alle organizzazioni di aggiungere stringhe per valutare e bloccare i casi in cui utenti e amministratori tentano di modificare o reimpostare una password.

## <a name="add-to-the-custom-list"></a>Aggiungere password all'elenco personalizzato

Per configurare l'elenco personalizzato di password escluse, è necessaria una licenza di Azure Active Directory Premium P1 o P2. Per informazioni più dettagliate sulle licenze di Azure Active Directory, vedere la [pagina dei prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).|

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory**, **Metodi di autenticazione** e quindi **Password protection (Preview)** (Protezione password - Anteprima).
1. Impostare l'opzione **Enforce custom list** (Applica elenco personalizzato) su **Sì**.
1. Aggiungere stringhe per **Custom banned password list** (Elenco personalizzato di password escluse), una stringa per riga
   * L'elenco personalizzato di password escluse può contenere fino a 1000 parole.
   * L'elenco personalizzato di password escluse fa distinzione tra maiuscole e minuscole.
   * L'elenco personalizzato di password escluse tiene conto della sostituzione dei caratteri comuni,
      * ad esempio: "o" e "0" o "a" e "\@"
   * La lunghezza minima delle stringhe è quattro caratteri, la massima è 16 caratteri.
1. Dopo aver aggiunto tutte le stringhe, fare clic su **Salva**.

> [!NOTE]
> Potrebbero essere necessarie diverse ore prima che l'aggiornamento dell'elenco personalizzato di password escluse venga applicato.

![Modificare l'elenco personalizzato di password escluse nella sezione Metodi di autenticazione nel portale di Azure](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Funzionamento

Ogni volta che un utente o un amministratore reimposta o modifica una password di Azure AD, vengono controllati gli elenchi di password escluse per confermare che non includano questa parola. Questo controllo è incluso in tutte le password impostate o modificate tramite Azure AD.

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Quando un utente cerca di reimpostare una password usando una stringa che verrà esclusa, viene visualizzato il messaggio di errore seguente:

La password contiene una parola o una frase o segue uno schema che la rende facile da indovinare. Riprovare con una password diversa.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica dei concetti relativi agli elenchi di password escluse](concept-password-ban-bad.md)

[Panoramica dei concetti relativi alla protezione password di Azure AD](concept-password-ban-bad-on-premises.md)

[Abilitare l'integrazione locale con gli elenchi di password escluse](howto-password-ban-bad-on-premises.md)
