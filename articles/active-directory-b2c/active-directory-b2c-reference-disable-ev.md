---
title: Disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente
titleSuffix: Azure AD B2C
description: Informazioni su come disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c5b3598e33dac131c8881248a5f4b740a6302e20
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948148"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente in Azure Active Directory B2C

Per impostazione predefinita, Azure Active Directory B2C (Azure AD B2C) verifica l'indirizzo di posta elettronica del cliente per gli account locali (account per gli utenti che effettuano l'iscrizione con l'indirizzo di posta elettronica o il nome utente). Azure AD B2C garantisce indirizzi di posta elettronica validi richiedendo ai clienti di verificarli durante il processo di iscrizione. Impedisce inoltre a un utente malintenzionato di usare processi automatizzati per generare account illeciti nelle applicazioni.

Alcuni sviluppatori di applicazioni preferiscono ignorare la verifica tramite posta elettronica durante il processo di iscrizione e i clienti possono invece verificare il proprio indirizzo di posta elettronica in un secondo momento. A tale scopo, è possibile configurare Azure AD B2C in modo da disabilitare la verifica tramite posta elettronica. Questa operazione consente di creare un processo di iscrizione più semplice e offre agli sviluppatori la flessibilità necessaria per distinguere i clienti che hanno verificato il proprio indirizzo di posta elettronica dai clienti che non lo hanno fatto.

Per disabilitare la verifica tramite posta elettronica, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Usare il filtro **directory + sottoscrizione** nel menu in alto per selezionare la directory che contiene il tenant del Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare il flusso utente per il quale si desidera disabilitare la verifica tramite posta elettronica. Ad esempio, *B2C_1_signinsignup*.
1. Selezionare **layout di pagina**.
1. Selezionare **la pagina di iscrizione dell'account locale**.
1. In **attributi utente**selezionare **indirizzo di posta elettronica**.
1. Nell'elenco a discesa **richieste di verifica** selezionare **No**.
1. Selezionare **Salva**. La verifica tramite posta elettronica è ora disabilitata per questo flusso utente.

> [!WARNING]
> Disabilitando la verifica tramite posta elettronica nel processo di iscrizione si potrebbe ricevere posta indesiderata. Se si disattiva la verifica predefinita della posta elettronica fornita dal Azure AD B2C, si consiglia di implementare un sistema di verifica sostitutivo.
