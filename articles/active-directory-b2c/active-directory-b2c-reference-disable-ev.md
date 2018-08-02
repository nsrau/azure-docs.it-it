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
ms.openlocfilehash: e008fb87b57b92f8f7e914e6b4344b52d42f9ef8
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263930"
---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Azure Active Directory B2C: disabilitare la verifica tramite posta elettronica durante l'iscrizione dell'utente
Quando è abilitato, Azure Active Directory (Azure AD) B2C permette all'utente di iscriversi alle applicazioni specificando un indirizzo di posta elettronica e creando un account locale. Azure AD B2C garantisce la validità degli indirizzi di posta elettronica richiedendone la verifica agli utenti durante il processo di iscrizione. Impedisce anche a processi dannosi automatizzati di generare account fittizi per le applicazioni.

Alcuni sviluppatori di applicazioni preferiscono ignorare la verifica tramite posta elettronica durante il processo di iscrizione, chiedendo agli utenti di verificare l'indirizzo di posta elettronica in un secondo momento. A tale scopo, è possibile configurare Azure AD B2C in modo da disabilitare la verifica tramite posta elettronica. In questo modo si ottiene un processo di iscrizione più semplice e gli sviluppatori hanno la possibilità di distinguere gli utenti che hanno verificato l'indirizzo di posta elettronica da quelli che non lo hanno fatto.

Per impostazione predefinita, nei criteri di iscrizione la verifica tramite posta elettronica è abilitata. Per disabilitarla, seguire questa procedura:

1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Fare clic su **Criteri di iscrizione** o su **Criteri di iscrizione o di accesso**, a seconda della configurazione usata per l'iscrizione.
3. Fare clic sul criterio, ad esempio "B2C_1_SiUp", per aprirlo. 
4. Fare clic su **Modifica** nella parte superiore del pannello.
5. Fare clic su **Personalizzazione dell'interfaccia utente della pagina**.
6. Fare clic su **Pagina di iscrizione dell'account locale**.
7. Fare clic su **Indirizzo di posta elettronica** nella colonna **Nome** sotto la sezione **Attributi di iscrizione**.
8. Impostare l'opzione **Richiedi la verifica** su **No**.
9. Fare clic su **OK** nella parte inferiore fino a raggiungere il pannello **Modifica criterio**.
10. Fare clic su **Salva** nella parte superiore del pannello. L'operazione è completata.

> [!NOTE]
> Disabilitando la verifica tramite posta elettronica nel processo di iscrizione si potrebbe ricevere posta indesiderata. Se si disabilita il sistema di verifica predefinito, è consigliabile aggiungerne uno personalizzato.
> 
> 

Commenti e suggerimenti sono sempre graditi. In caso di difficoltà con questo argomento o di suggerimenti per migliorarne il contenuto, è possibile lasciare un commento nella parte inferiore della pagina. Le richieste di funzionalità possono essere aggiunte in [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
