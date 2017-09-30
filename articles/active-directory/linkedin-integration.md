---
title: Configurare l'integrazione di LinkedIn in Azure AD | Microsoft Docs
description: Illustra come abilitare o disabilitare l'integrazione di LinkedIn per le app Microsoft in Azure Active Directory.
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 48b26bfcce67ce915c404a0ab2ac0f399c3b821d
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>Abilitazione dell'integrazione di LinkedIn in Azure Active Directory
L'abilitazione dell'integrazione di LinkedIn consente agli utenti di accedere sia ai dati pubblici di LinkedIn e, se lo vogliono, alla propria rete personale di LinkedIn direttamente dalle app Microsoft. Ogni utente può scegliere in modo indipendente di connettere il proprio account aziendale a quello su LinkedIn.

### <a name="linkedin-integration-from-your-end-users-perspective"></a>Integrazione di LinkedIn dal punto di vista degli utenti finali
Quando gli utenti finali di un'organizzazione connettono i propri account su LinkedIn agli account aziendali, sono in grado di identificare più facilmente le persone con cui lavorano all'interno e all'esterno dell'organizzazione. La connessione dei due account consente di usare i dati del profilo e delle connessioni di LinkedIn dell'utente all'interno delle app Microsoft dell'organizzazione, con la possibilità di rifiutare esplicitamente la connessione rimuovendo l'autorizzazione per LinkedIn a condividere tali dati. L'integrazione usa inoltre le informazioni del profilo disponibili pubblicamente e gli utenti possono scegliere se condividere le informazioni di rete e del profilo pubbliche con le applicazioni Microsoft tramite le impostazioni della privacy di LinkedIn.

>[!NOTE]
> L'abilitazione dell'integrazione di LinkedIn in Azure AD consente ad app e servizi di accedere ad alcune delle informazioni degli utenti finali. Leggere l'[Informativa sulla privacy di Microsoft](https://privacy.microsoft.com/privacystatement/) per altre informazioni sulle considerazioni relative alla privacy quando si abilita l'integrazione di LinkedIn in Azure AD. 

## <a name="enable-linkedin-integration"></a>Abilitare l'integrazione di LinkedIn
L'integrazione di LinkedIn per le aziende è abilitata per impostazione predefinita in Azure AD. Pertanto, quando questa funzionalità viene resa disponibile per il tenant, tutti gli utenti dell'organizzazione possono vedere le funzionalità e i profili di LinkedIn. L'abilitazione dell'integrazione di LinkedIn consente agli utenti dell'organizzazione di usare le funzionalità di LinkedIn direttamente dai servizi Microsoft, ad esempio per visualizzare i profili quando si riceve un messaggio e-mail in Outlook. La disabilitazione di questa funzionalità impedisce l'accesso alle funzionalità di LinkedIn e interrompe le connessioni degli account utente e la condivisione di dati tra LinkedIn e l'organizzazione attraverso i servizi Microsoft.

> [!IMPORTANT]
> Questa funzionalità non è disponibile per i tenant locali, sovrani e per enti pubblici. Inoltre, gli aggiornamenti del servizio Azure AD, come le funzionalità di integrazione di LinkedIn, non vengono distribuiti a tutti i tenant di Azure nello stesso momento. L'integrazione di LinkedIn con Azure AD può essere abilitata solo dopo che questa funzionalità è stata implementata nel tenant di Azure in uso.

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com/) con un account di amministratore globale per la directory del tenant.
2. Selezionare **Utenti e gruppi**.
3. Nel pannello **Utenti e gruppi** selezionare **Impostazioni utente**.
4. In **Integrazione di LinkedIn** selezionare Sì o No per abilitare o disabilitare l'integrazione di LinkedIn.
   ![Abilitazione dell'integrazione di LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>Altre informazioni 
* [About LinkedIn information and features in Microsoft apps and services](https://go.microsoft.com/fwlink/?linkid=850740) (Informazioni e funzionalità di LinkedIn nelle app e nei servizi Microsoft)

* [Centro assistenza LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Passaggi successivi
È possibile usare il collegamento seguente per abilitare o disabilitare l'integrazione di LinkedIn con Azure AD dal portale di Azure:

[Configurare l'integrazione di LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 
