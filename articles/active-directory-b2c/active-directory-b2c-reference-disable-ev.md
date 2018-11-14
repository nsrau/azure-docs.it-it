---
title: Disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente in Azure Active Directory B2C | Microsoft Docs
description: Questo argomento illustra come disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 2/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e36dd19aa020b8cb2a623cda904cf7fa8a0b26da
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004593"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente in Azure Active Directory B2C 
Quando è abilitato, Azure Active Directory (Azure AD) B2C permette all'utente di iscriversi alle applicazioni specificando un indirizzo di posta elettronica e creando un account locale. Azure AD B2C garantisce la validità degli indirizzi di posta elettronica richiedendone la verifica agli utenti durante il processo di iscrizione. Impedisce anche a processi dannosi automatizzati di generare account fittizi per le applicazioni.

Alcuni sviluppatori di applicazioni preferiscono ignorare la verifica tramite posta elettronica durante il processo di iscrizione, chiedendo agli utenti di verificare l'indirizzo di posta elettronica in un secondo momento. A tale scopo, è possibile configurare Azure AD B2C in modo da disabilitare la verifica tramite posta elettronica. In questo modo si ottiene un processo di iscrizione più semplice e gli sviluppatori hanno la possibilità di distinguere gli utenti che hanno verificato l'indirizzo di posta elettronica da quelli che non lo hanno fatto.

Per impostazione predefinita, nei criteri di iscrizione la verifica tramite posta elettronica è abilitata. Per disabilitarla, seguire questa procedura:

1. Fare clic su **Criteri di iscrizione** o su **Criteri di iscrizione o di accesso**, a seconda della configurazione usata per l'iscrizione.
2. Fare clic sul criterio, ad esempio "B2C_1_SiUp", per aprirlo. 
3. Fare clic su **Modifica** nella parte superiore del pannello.
4. Fare clic su **Personalizzazione dell'interfaccia utente della pagina**.
5. Fare clic su **Pagina di iscrizione dell'account locale**.
6. Fare clic su **Indirizzo di posta elettronica** nella colonna **Nome** sotto la sezione **Attributi di iscrizione**.
7. Impostare l'opzione **Richiedi la verifica** su **No**.
8. Fare clic su **OK** nella parte inferiore fino a raggiungere il pannello **Modifica criterio**.
9. Fare clic su **Salva** nella parte superiore del pannello. L'operazione è completata.

> [!NOTE]
> Disabilitando la verifica tramite posta elettronica nel processo di iscrizione si potrebbe ricevere posta indesiderata. Se si disabilita il sistema di verifica predefinito, è consigliabile aggiungerne uno personalizzato.
> 
>