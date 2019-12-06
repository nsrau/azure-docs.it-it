---
title: Come escludere password vulnerabili in Azure AD-Azure Active Directory
description: Escludere password non appropriate dall'ambiente con le password escluse dinamicamente di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c58371edffa4400c2c2b3efd37c3ce6d7c3cad95
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847916"
---
# <a name="configuring-the-custom-banned-password-list"></a>Configurazione dell'elenco personalizzato di password escluse

Molte organizzazioni riscontrano spesso il fatto che gli utenti creano password usando parole locali comuni, come una scuola, una squadra sportiva o una persona famosa, rendendole facili da indovinare. Oltre all'elenco globale di password escluse, l'elenco personalizzato di password escluse permette alle organizzazioni di aggiungere stringhe per valutare e bloccare i casi in cui utenti e amministratori tentano di modificare o reimpostare una password.

## <a name="add-to-the-custom-list"></a>Aggiungere password all'elenco personalizzato

Per configurare l'elenco personalizzato di password escluse, è necessaria una licenza di Azure Active Directory Premium P1 o P2. Per informazioni più dettagliate sulle licenze Azure Active Directory, vedere la [pagina relativa ai prezzi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory**, metodi di **autenticazione**e quindi **proteggere con password**.
1. Impostare l'opzione **Enforce custom list** (Applica elenco personalizzato) su **Sì**.
1. Aggiungere stringhe per **Custom banned password list** (Elenco personalizzato di password escluse), una stringa per riga
   * L'elenco delle password con divieto personalizzato può contenere un massimo di 1000 termini.
   * L'elenco personalizzato di password escluse fa distinzione tra maiuscole e minuscole.
   * L'elenco personalizzato di password escluse tiene conto della sostituzione dei caratteri comuni,
      * ad esempio: "o" e "0" o "a" e "\@"
   * La lunghezza minima delle stringhe è quattro caratteri, la massima è 16 caratteri.
1. Dopo aver aggiunto tutte le stringhe, fare clic su **Salva**.

> [!NOTE]
> Potrebbero essere necessarie diverse ore prima che l'aggiornamento dell'elenco personalizzato di password escluse venga applicato.

> [!NOTE]
> L'elenco delle password con divieto personalizzato è limitato a un massimo di 1000 termini. Non è progettato per il blocco di elenchi di password estremamente grandi. Per sfruttare appieno i vantaggi dell'elenco delle password con Banned personalizzato, è consigliabile prima di tutto esaminare e comprendere la progettazione e l'utilizzo previsti per l'elenco delle password escluse da quelle personalizzate (vedere l'elenco delle [password escluse](concept-password-ban-bad.md#custom-banned-password-list)) e anche l'algoritmo di valutazione delle password (vedere [come vengono valutate le password](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Modificare l'elenco personalizzato di password escluse nella sezione Metodi di autenticazione nel portale di Azure](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Come funziona

Ogni volta che un utente o un amministratore reimposta o modifica una password di Azure AD, vengono controllati gli elenchi di password escluse per confermare che non includano questa parola. Questo controllo è incluso in tutte le password impostate o modificate tramite Azure AD.

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Quando un utente tenta di reimpostare una password per un elemento vietato, viene visualizzato uno dei messaggi di errore seguenti:

* La password contiene una parola o una frase o segue uno schema che la rende facile da indovinare. Riprovare con una password diversa.
* Sfortunatamente, non è possibile usare la password perché contiene parole o caratteri che sono stati bloccati dall'amministratore. Riprovare con una password diversa.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica dei concetti relativi agli elenchi di password escluse](concept-password-ban-bad.md)

[Panoramica dei concetti relativi alla protezione password di Azure AD](concept-password-ban-bad-on-premises.md)

[Abilitare l'integrazione locale con gli elenchi di password escluse](howto-password-ban-bad-on-premises.md)
