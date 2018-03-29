---
title: 'Azure Active Directory B2C: reimpostazione password self-service | Documentazione Microsoft'
description: Un argomento che dimostra come configurare la reimpostazione della password self-service per gli utenti in Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: f38473989f90bfe6d35bffb17a02a892ad08cf5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C: configurare la reimpostazione password self-service per gli utenti
La funzione di reimpostazione password self-service consente agli utenti che hanno effettuato la registrazione agli account locali di reimpostare le password in modo indipendente. Questo riduce notevolmente il carico per lo staff di supporto, soprattutto se l'applicazione dispone di milioni di clienti che la utilizzano regolarmente. Attualmente, è supportato solo l’utilizzo di un indirizzo di posta elettronica verificato come metodo di ripristino. Verranno aggiunti metodi di ripristino aggiuntivi (numero di telefono verificato, domande di sicurezza e così via) in futuro.

> [!NOTE]
> Questo articolo si applica alla reimpostazione delle password self-service nel contesto di un criterio di accesso. Se è necessario richiamare dall'app criteri di reimpostazione delle password completamente personalizzabili, vedere [questo articolo](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Per impostazione predefinita, la directory non avrà la reimpostazione password self-service attivata. Usare i passaggi seguenti per attivarla:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore della sottoscrizione. Si tratta dello stesso account aziendale o dell'istituto d'istruzione o dello stesso account Microsoft usato per la creazione della directory.
2. Aprire Active Directory (nella barra di spostamento sul lato sinistro).
3. Selezionare **Proprietà**.
4. Scorrere verso il basso fino a **Reimpostazione password self-service abilitata** e impostare l'opzione su **Tutti**. 
5. Fare clic su **Salva** nella parte superiore della pagina. L'operazione è completata.

Per eseguire il test, usare la funzionalità "Esegui adesso" in ogni criterio di accesso che include gli account locali come provider di identità. Nella pagina di accesso dell'account locale in cui si immettono l'indirizzo di posta elettronica e la password o il nome utente e la password, fare clic su **Problemi di accesso all'account?** per verificare l'esperienza dell'utente.

> [!NOTE]
> Le pagine di reimpostazione della password self-service possono essere personalizzate con la [funzionalità di aggiunta di informazioni distintive dell'azienda](../active-directory/customize-branding.md).
> 
> 

