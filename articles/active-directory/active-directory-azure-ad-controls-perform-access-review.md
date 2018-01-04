---
title: Verificare l'accesso usando le verifiche di accesso di Azure AD | Microsoft Docs
description: Informazioni su come verificare l'accesso usando le verifiche di accesso di Azure Active Directory.
services: active-directory
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 98658a2b9fdd0ede98b3f6c10a19af527a8677cc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Verificare l'accesso con le verifiche di accesso di Azure AD

Azure Active Directory (Azure AD) semplifica il modo in cui le aziende gestiscono l'accesso alle applicazioni e i membri dei gruppi in Azure AD e gli altri Microsoft Online Services, con una funzione denominata verifiche di accesso. È possibile che si abbia ricevuto un messaggio di posta elettronica da Microsoft in cui viene richiesto di verificare l'accesso dei membri di un gruppo o degli utenti con accesso a un'applicazione. 

## <a name="open-an-access-review"></a>Aprire una verifica di accesso

Per esaminare le verifiche di accesso in sospeso selezionare il collegamento nel messaggio di posta elettronica. Se non è disponibile il messaggio di posta elettronica, è possibile individuare le verifiche di accesso seguendo questa procedura:

1. Accedere al [pannello di accesso di Azure AD](https://myapps.microsoft.com).

2. Selezionare il simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

3. Selezionare il riquadro **Verifiche di accesso** sul lato destro della pagina, se visibile. Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

## <a name="fill-out-an-access-review"></a>Compilare una verifica di accesso

Quando si seleziona una verifica di accesso nell'elenco, verranno visualizzati i nomi degli utenti che necessitano di essere sottoposti a una verifica. È possibile che venga visualizzato un unico nome, ossia il proprio nome, se la richiesta era relativa alla verifica del proprio accesso.

Per ogni riga nell'elenco è possibile decidere se approvare o negare l'accesso dell'utente. Selezionare la riga e scegliere se approvare o negare l'accesso. Se non si conosce l'utente, è possibile anche indicare questa informazione.

Il revisore potrebbe richiedere una giustificazione per l'approvazione dell'accesso continuo o dell'appartenenza al gruppo.

## <a name="next-steps"></a>Passaggi successivi

L'accesso negato dell'utente non viene rimosso immediatamente. Può essere rimosso al termine della verifica o quando un amministratore interrompe la verifica. Se si vuole cambiare la risposta e approvare per un utente l'accesso negato in precedenza o negare un accesso approvato in precedenza, selezionare la riga, reimpostare la risposta e selezionare una nuova risposta. È possibile eseguire questo passaggio finché non viene completata la verifica di accesso.



