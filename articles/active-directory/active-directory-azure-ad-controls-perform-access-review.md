---
title: Come verificare l'accesso usando le verifiche di accesso di Azure AD | Microsoft Docs
description: Come verificare l'accesso usando le verifiche di accesso di Azure AD.
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: c9115bb5bc3d22c67ea0ac77b8972ae7f919926b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-review-access-with-azure-ad-access-reviews"></a>Come verificare l'accesso con le verifiche di accesso di Azure AD

Azure Active Directory semplifica il modo in cui le aziende gestiscono l'accesso alle applicazioni e i membri dei gruppi in Azure AD e gli altri Microsoft Online Services, con una funzione denominata verifiche di accesso.  È possibile che si abbia ricevuto un messaggio di posta elettronica da Microsoft in cui viene richiesto di verificare l'accesso dei membri di un gruppo o degli utenti con accesso a un'applicazione. 

## <a name="opening-an-access-review"></a>Apertura di una verifica di accesso

È possibile esaminare le verifiche di accesso in sospeso facendo clic sul collegamento nel messaggio di posta elettronica.  Se non è disponibile il messaggio di posta elettronica, è possibile individuare le verifiche di accesso seguendo questa procedura:

1. Accedere al [pannello di accesso di Azure AD](https://myapps.microsoft.com).
2. Fare clic sull'icona utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare quella che ha richiesto una verifica di accesso.
3. Fare clic sul riquadro **Verifiche di accesso** sul lato destro della pagina, se visibile. Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

## <a name="filling-out-an-access-review"></a>Compilare una verifica di accesso

Selezionare una verifica di accesso nell'elenco e verranno visualizzati i nomi degli utenti che necessitano di essere sottoposti a una verifica.  È possibile che venga visualizzato un unico nome, ossia il proprio nome, se la richiesta era relativa alla verifica del proprio accesso.

Per ogni riga nell'elenco è possibile decidere se approvare o negare l'accesso dell'utente. È possibile fare clic sulla riga e scegliere se approvare o negare l'accesso. Se non si conosce l'utente, è possibile anche indicarlo nella risposta.

Il revisore potrebbe richiedere una giustificazione per l'approvazione dell'accesso continuo o dell'appartenenza al gruppo.

## <a name="next-steps"></a>Passaggi successivi

Si noti che l'accesso negato dell'utente non viene rimosso immediatamente; può essere rimosso al completamento della verifica o quando un amministratore interrompe la verifica. Se si vuole cambiare la risposta e approvare per un utente l'accesso negato in precedenza o negare un accesso approvato in precedenza, fare clic sulla riga, reimpostare la risposta e selezionare una nuova risposta.  È possibile eseguire questa operazione finché non viene completata la verifica di accesso.



