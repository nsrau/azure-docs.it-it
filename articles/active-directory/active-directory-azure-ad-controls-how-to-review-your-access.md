---
title: Verificare l'accesso | Microsoft Docs
description: Verificare l'accesso con Azure Active Directory
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: f85f83ff103818b826ca2c61c41fbbef67e98c14
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622229"
---
# <a name="review-your-access"></a>Verificare l'accesso

Azure Active Directory (Azure AD) semplifica il modo in cui le aziende gestiscono l'accesso alle applicazioni e i membri dei gruppi in Azure AD e gli altri Microsoft Online Services, con una funzione denominata verifiche di accesso. È possibile che si abbia ricevuto un messaggio di posta elettronica da Microsoft in cui viene richiesto di verificare l'accesso dei membri di un gruppo o degli utenti con accesso a un'applicazione. 

## <a name="open-an-access-review"></a>Aprire una verifica di accesso

Per esaminare le verifiche di accesso in sospeso selezionare il collegamento per la verifica dell'accesso nel messaggio di posta elettronica. A partire da agosto 2018, le notifiche tramite posta elettronica per i ruoli di Azure AD avranno un design aggiornato. Di seguito viene illustrato un messaggio di posta elettronica di esempio che viene inviato quando un utente viene invitato da un revisore.

![Messaggio di posta elettronica di verifica di accesso](./media/active-directory-azure-ad-controls-how-to-review-your-access/new-ar-email.png)

Se non è disponibile il messaggio di posta elettronica, è possibile individuare le verifiche di accesso seguendo questa procedura:

1. Accedere al [pannello di accesso di Azure AD](https://myapps.microsoft.com).

2. Selezionare il simbolo utente nell'angolo superiore destro della pagina, che indica il proprio nome e l'organizzazione predefinita. Se sono elencate più organizzazioni, selezionare l'organizzazione che ha richiesto una verifica di accesso.

3. Selezionare il riquadro **Verifiche di accesso** sul lato destro della pagina, se visibile. Se il riquadro non è visibile, non esistono verifiche di accesso da eseguire per tale organizzazione e al momento non è necessaria alcuna azione.

## <a name="fill-out-an-access-review"></a>Compilare una verifica di accesso

Quando si seleziona una verifica di accesso dall'elenco, è possibile esaminare il proprio accesso. Selezionare la riga e scegliere se approvare o negare la necessità di accesso continuo.

Il revisore potrebbe richiedere una giustificazione per l'approvazione dell'accesso continuo.

## <a name="next-steps"></a>Passaggi successivi

L'accesso negato non viene rimosso immediatamente. Se si vuole modificare la risposta e approvare, reimpostare la risposta e selezionare una nuova risposta. È possibile eseguire questo passaggio finché non viene completata la verifica di accesso.






