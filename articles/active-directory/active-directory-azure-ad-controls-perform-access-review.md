---
title: Verificare l'accesso usando le verifiche di accesso di Azure AD | Microsoft Docs
description: Informazioni su come verificare l'accesso usando le verifiche di accesso di Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 2e0a522f6125a9e34a37c50f547e725072ec65b4
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205004"
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Verificare l'accesso con le verifiche di accesso di Azure AD

Azure Active Directory (Azure AD) semplifica il modo in cui le aziende gestiscono l'accesso alle applicazioni e i membri dei gruppi in Azure AD e gli altri Microsoft Online Services, con una funzione denominata verifiche di accesso. È possibile che si abbia ricevuto un messaggio di posta elettronica da Microsoft in cui viene richiesto di verificare l'accesso dei membri di un gruppo o degli utenti con accesso a un'applicazione. 

## <a name="open-an-access-review"></a>Aprire una verifica di accesso

Per esaminare le verifiche di accesso in sospeso selezionare il collegamento per la verifica dell'accesso nel messaggio di posta elettronica. A partire da agosto 2018, le notifiche tramite posta elettronica per i ruoli di Azure AD avranno un design aggiornato. Di seguito viene illustrato un messaggio di posta elettronica di esempio che viene inviato quando un utente viene invitato da un revisore. 

![Messaggio di posta elettronica di verifica di accesso](./media/active-directory-azure-ad-controls-perform-access-review/new-ar-email.png)

Se non è disponibile il messaggio di posta elettronica, è possibile individuare le verifiche di accesso seguendo questa procedura:

1. Accedere al [pannello di accesso di Azure AD](https://myapps.microsoft.com).

2. Selezionare il simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

3. Selezionare il riquadro **Verifiche di accesso** sul lato destro della pagina, se visibile. Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

## <a name="fill-out-an-access-review"></a>Compilare una verifica di accesso

Quando si seleziona una verifica di accesso nell'elenco, verranno visualizzati i nomi degli utenti che necessitano di essere sottoposti a una verifica. È possibile che venga visualizzato un unico nome, ossia il proprio nome, se la richiesta era relativa alla verifica del proprio accesso.

Per ogni riga nell'elenco è possibile decidere se approvare o negare l'accesso dell'utente. Selezionare la riga e scegliere se approvare o negare l'accesso. Se non si conosce l'utente, è possibile anche indicare questa informazione.

Il revisore potrebbe richiedere una giustificazione per l'approvazione dell'accesso continuo o dell'appartenenza al gruppo.

## <a name="next-steps"></a>Passaggi successivi

L'accesso negato dell'utente non viene rimosso immediatamente. Può essere rimosso al termine della verifica o quando un amministratore interrompe la verifica. Se si vuole cambiare la risposta e approvare per un utente l'accesso negato in precedenza o negare un accesso approvato in precedenza, selezionare la riga, reimpostare la risposta e selezionare una nuova risposta. È possibile eseguire questo passaggio finché non viene completata la verifica di accesso.



