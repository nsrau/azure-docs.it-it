---
title: Reimpostazione self-service della password| Microsoft Docs
description: Illustra come configurare la reimpostazione della password self-service per i clienti in Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/17/2018
ms.author: davidmu
ms.openlocfilehash: 5b75455ad604b594a5f85fea8299d35a7d02c848
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configurare la reimpostazione self-service della password per i clienti
La funzione di reimpostazione self-service della password consente ai clienti che si sono registrati per ottenere un account locale di reimpostare le password in modo autonomo. Questo riduce notevolmente il carico di lavoro per lo staff di supporto, soprattutto se l'applicazione viene usata regolarmente da milioni di clienti. L'unico metodo di recupero attualmente supportato è l'uso di un indirizzo di posta elettronica verificato.

> [!NOTE]
> Questo articolo si applica alla reimpostazione delle password self-service nel contesto di un criterio di accesso. Se è necessario richiamare dall'app criteri di reimpostazione delle password completamente personalizzabili, vedere [questo articolo](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Per impostazione predefinita, per la directory personale la reimpostazione self-service della password non è attivata. Usare i passaggi seguenti per attivarla:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore della sottoscrizione. Si tratta dello stesso account aziendale o dell'istituto d'istruzione o dello stesso account Microsoft usato per la creazione della directory.
2. Aprire **Azure Active Directory** (nella barra di spostamento sul lato sinistro).
4. Impostare **Reimpostazione password self-service abilitata** su **Tutte**. 
5. Fare clic su **Salva** nella parte superiore della pagina. L'operazione è completata.

Per eseguire il test, usare la funzionalità "Esegui adesso" in ogni criterio di accesso che include gli account locali come provider di identità. Nella pagina di accesso dell'account locale in cui si immettono l'indirizzo di posta elettronica e la password o il nome utente e la password, fare clic su **Problemi di accesso all'account?** per verificare l'esperienza cliente.

> [!NOTE]
> Le pagine di reimpostazione della password self-service possono essere personalizzate con la [funzionalità di aggiunta di informazioni distintive dell'azienda](../active-directory/customize-branding.md).
> 
> 

