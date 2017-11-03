---
title: "Complessità delle password - Azure AD B2C | Microsoft Docs"
description: "Come configurare i requisiti di complessità delle password specificate dagli utenti in Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 800199b3d1d91d700d26f988a4d49713028de1ce
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2017
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Configurare i requisiti di complessità delle password

> [!NOTE]
> **Questa funzionalità è in anteprima.**  Contattare [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) per abilitare questa funzionalità per il proprio tenant di prova.

Azure Active Directory B2C (Azure AD B2C) supporta la modifica dei requisiti di complessità delle password specificate da un utente finale durante la creazione di un account.  Per impostazione predefinita, Azure AD B2C usa password di tipo `Strong`.  Azure AD B2C supporta anche opzioni di configurazione per controllare la complessità delle password che i clienti possono usare.

## <a name="when-password-rules-are-enforced"></a>Quando vengono applicate regole sulle password

Durante la registrazione o la reimpostazione di una password, l'utente finale deve specificare una password che soddisfi le regole di complessità,  che vengono applicate in base ai criteri di riferimento.  È possibile che, durante la registrazione, alcuni criteri richiedano un pin a quattro cifre, mentre altri una stringa di otto caratteri.  È possibile, ad esempio, usare criteri con una complessità delle password diversa per gli adulti e per i bambini.

La complessità delle password non viene mai applicata durante l'accesso.  Durante la registrazione, infatti, agli utenti non viene mai chiesto di modificare la password perché non soddisfa i requisiti di complessità correnti.

Di seguito sono elencati i tipi di criteri in cui è possibile configurare la complessità delle password:

* Criteri di registrazione o di accesso
* Criteri di reimpostazione delle password
* Criteri personalizzati ([Configurare la complessità delle password nei criteri personalizzati](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Come configurare la complessità delle password

1. Seguire questa procedura per [passare alle impostazioni di Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
1. Aprire **Sign-up or sign-in polices** (Criteri di registrazione o accesso).
1. Selezionare i criteri e fare clic su **Modifica**.
1. Aprire **Complessità password**.
1. Impostare la complessità delle password per questi criteri su **Semplice**, **Alta** o **Personalizzata**.

### <a name="comparison-chart"></a>Grafico di confronto

| Complessità | Descrizione |
| --- | --- |
| Semplice | Una password con un numero di caratteri compreso tra 8 e 64. |
| Assoluta | Una password con un numero di caratteri compreso tra 8 e 64. Richiede almeno tre dei quattro tipi di carattere seguenti: lettere minuscole, lettere maiuscole, numeri e simboli. |
| Personalizzate | Questa opzione offre il massimo controllo sulle regole di complessità delle password.  Consente infatti di configurare una lunghezza personalizzata  o di accettare password solo numeriche (PIN). |

## <a name="options-available-under-custom"></a>Opzioni disponibili nella configurazione personalizzata

### <a name="character-set"></a>Set di caratteri

Consente di accettare solo cifre (PIN) o l'intero set di caratteri.

* **Solo numeri** consente di immettere solo cifre (0-9) durante la configurazione di una password.
* **Tutti** consente qualsiasi lettera, numero o simbolo.

### <a name="length"></a>Length

Consente di controllare i requisiti di lunghezza della password.

* Il valore **Lunghezza minima** deve essere almeno 4.
* Il valore **Lunghezza massima** deve essere maggiore o uguale alla lunghezza minima e può essere al massimo di 64 caratteri.

### <a name="character-classes"></a>Classi di caratteri

Consente di controllare i diversi tipi di carattere usati nella password.

* **2 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (2 di 4: carattere minuscolo, carattere maiuscolo, numero (0-9), simbolo): garantisce che la password contenga almeno due tipi di carattere, ad esempio un numero e un carattere minuscolo.
* **3 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (3 di 4: carattere minuscolo, carattere maiuscolo, numero (0-9), simbolo): garantisce che la password contenga almeno tre tipi di carattere, ad esempio un numero, un carattere minuscolo e un carattere maiuscolo.
* **4 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (4 di 4: carattere minuscolo, carattere maiuscolo, numero (0-9), simbolo): garantisce che la password contenga tutti i quattro tipi di carattere.

    > [!NOTE]
    > L'opzione **4 of 4** (4 di 4) può determinare frustrazione nell'utente finale. Alcuni studi hanno dimostrato inoltre che questo requisito non migliora l'entropia delle password. Vedere [NIST Password Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) (Linee guida sulle password NIST)
