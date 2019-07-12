---
title: Come escludere password vulnerabili in Azure AD - Azure Active Directory
description: Escludere password non appropriate dall'ambiente con le password escluse dinamicamente di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f531174c889948308e27109ab4fd80a481ec6bdc
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798194"
---
# <a name="configuring-the-custom-banned-password-list"></a>Configurazione dell'elenco personalizzato di password escluse

Molte organizzazioni riscontrano spesso il fatto che gli utenti creano password usando parole locali comuni, come una scuola, una squadra sportiva o una persona famosa, rendendole facili da indovinare. Oltre all'elenco globale di password escluse, l'elenco personalizzato di password escluse permette alle organizzazioni di aggiungere stringhe per valutare e bloccare i casi in cui utenti e amministratori tentano di modificare o reimpostare una password.

## <a name="add-to-the-custom-list"></a>Aggiungere password all'elenco personalizzato

Per configurare l'elenco personalizzato di password escluse, è necessaria una licenza di Azure Active Directory Premium P1 o P2. Per altre informazioni sulle licenze di Azure Active Directory, vedere la [pagina dei prezzi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

1. Accedi per il [portale di Azure](https://portal.azure.com) e passare alla **Azure Active Directory**, **metodi di autenticazione**, quindi **protezione con Password**.
1. Impostare l'opzione **Enforce custom list** (Applica elenco personalizzato) su **Sì**.
1. Aggiungere stringhe per **Custom banned password list** (Elenco personalizzato di password escluse), una stringa per riga
   * L'elenco di password vietate personalizzato può contenere fino a 1000 termini.
   * L'elenco personalizzato di password escluse fa distinzione tra maiuscole e minuscole.
   * L'elenco personalizzato di password escluse tiene conto della sostituzione dei caratteri comuni,
      * ad esempio: "o" e "0" o "a" e "\@"
   * La lunghezza minima delle stringhe è quattro caratteri, la massima è 16 caratteri.
1. Dopo aver aggiunto tutte le stringhe, fare clic su **Salva**.

> [!NOTE]
> Potrebbero essere necessarie diverse ore prima che l'aggiornamento dell'elenco personalizzato di password escluse venga applicato.

> [!NOTE]
> L'elenco di password vietate personalizzato è limitato alla presenza di un massimo di termini di 1000. Non è progettato per bloccare gli elenchi di dimensioni estremamente grandi di password. Per sfruttare completamente i vantaggi dell'elenco personalizzato di password vietate, Microsoft consiglia di rivedere innanzitutto e comprendere la progettazione desiderata e l'utilizzo dell'elenco di password vietate personalizzato (vedere [Custom vietata elenco password](concept-password-ban-bad.md#custom-banned-password-list)), e anche l'algoritmo di valutazione della password (vedere [modo in cui vengono valutate le password](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Modificare l'elenco personalizzato di password escluse nella sezione Metodi di autenticazione nel portale di Azure](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Funzionamento

Ogni volta che un utente o un amministratore reimposta o modifica una password di Azure AD, vengono controllati gli elenchi di password escluse per confermare che non includano questa parola. Questo controllo è incluso in tutte le password impostate o modificate tramite Azure AD.

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Quando un utente tenta di reimpostare una password per un elemento verrebbe escluso, viene visualizzato uno dei messaggi di errore seguenti:

* La password contiene una parola o una frase o segue uno schema che la rende facile da indovinare. Riprovare con una password diversa.
* Sfortunatamente, è possibile usare la password perché contiene parole o caratteri bloccati dall'amministratore. Riprovare con una password diversa.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica dei concetti relativi agli elenchi di password escluse](concept-password-ban-bad.md)

[Panoramica dei concetti relativi alla protezione password di Azure AD](concept-password-ban-bad-on-premises.md)

[Abilitare l'integrazione locale con gli elenchi di password escluse](howto-password-ban-bad-on-premises.md)
