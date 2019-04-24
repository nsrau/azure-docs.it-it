---
title: Disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente in Azure Active Directory B2C | Microsoft Docs
description: Questo argomento illustra come disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e139f40e9724840f3dad4dc9f0b4d5317a75ebd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317276"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente in Azure Active Directory B2C 
Quando è abilitato, Azure Active Directory (Azure AD) B2C permette all'utente di iscriversi alle applicazioni specificando un indirizzo di posta elettronica e creando un account locale. Azure AD B2C garantisce la validità degli indirizzi di posta elettronica richiedendone la verifica agli utenti durante il processo di iscrizione. Impedisce anche a processi dannosi automatizzati di generare account fittizi per le applicazioni.

Alcuni sviluppatori di applicazioni preferiscono ignorare la verifica tramite posta elettronica durante il processo di iscrizione, chiedendo agli utenti di verificare l'indirizzo di posta elettronica in un secondo momento. A tale scopo, è possibile configurare Azure AD B2C in modo da disabilitare la verifica tramite posta elettronica. In questo modo si ottiene un processo di iscrizione più semplice e gli sviluppatori hanno la possibilità di distinguere gli utenti che hanno verificato l'indirizzo di posta elettronica da quelli che non lo hanno fatto.

Per impostazione predefinita, nei flussi utente di iscrizione la verifica tramite posta elettronica è abilitata. Per disabilitarla, seguire questa procedura:

1. Fare clic su **Flussi utente**.
2. Fare clic sul flusso utente, ad esempio "B2C_1_SiUp", per aprirlo. 
3. Fare clic su **Layout di pagina**.
4. Fare clic su **Pagina di iscrizione dell'account locale**.
5. Fare clic su **Indirizzo di posta elettronica** nella colonna **Nome** sotto la sezione **Attributi utente**.
6. In **Richiede la verifica** selezionare **No**.
7. Fare clic su **Salva** nella parte superiore del pannello. L'operazione è completata.

> [!NOTE]
> Disabilitando la verifica tramite posta elettronica nel processo di iscrizione si potrebbe ricevere posta indesiderata. Se si disabilita il sistema di verifica predefinito, è consigliabile aggiungerne uno personalizzato.
> 
>
