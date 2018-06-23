---
title: Che cos'è la protezione di base nell'accesso condizionale di Azure Active Directory | Microsoft Docs
description: Informazioni su come la protezione di base garantisce di avere almeno il livello di base della sicurezza abilitato nell'ambiente in uso.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248936"
---
# <a name="what-is-baseline-protection"></a>Che cos'è la protezione di base?  

Nell'ultimo anno gli attacchi alle identità sono aumentati del 300%. Per proteggere l'ambiente dalla continua crescita degli attacchi, Azure Active Directory (Azure AD) introduce una nuova funzionalità denominata protezione di base. La protezione di base è un set di criteri di accesso condizionale predefiniti. L'obiettivo di questi criteri è assicurarsi di disporre almeno del livello di base della sicurezza abilitato nell'ambiente in uso. 

Nella versione di anteprima è necessario abilitare i criteri di base per attivarli. Nella versione disponibile a livello generale, questi criteri sono abilitati per impostazione predefinita. 

Il primo criterio di protezione di base richiede l'autenticazione MFA per gli account con privilegi. Gli utenti malintenzionati che ottengono il controllo di account con privilegi possono causare enormi danni, pertanto è fondamentale proteggere questi account prima di tutto. I ruoli con privilegi seguenti rientrano nell'ambito per questo criterio: 

- Amministratore globale  

- Amministratore di SharePoint  

- Amministratore di Exchange  

- Amministratore di accesso condizionale  

- Amministratore della sicurezza  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>Attività iniziali 

Per abilitare i criteri di base:  

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.

2. Nel **portale di Azure**, sulla barra di spostamento a sinistra, fare clic su **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. Nella sezione **Gestisci** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Accesso condizionale](./media/active-directory-conditional-access-baseline-protection/03.png)

4. Nell'elenco dei criteri fare clic su **Baseline policy: Require MFA for admins (Preview)** (Criteri di base: Richiedi MFA per gli amministratori - anteprima). 

5. Per abilitare i criteri, fare clic su **Usa i criteri immediatamente**.

6. Fare clic su **Save**. 
 

I criteri di base offrono la possibilità di escludere utenti e gruppi. È possibile escludere un *[account amministrativo di accesso di emergenza](active-directory-admin-manage-emergency-access-accounts.md)* per assicurarsi di non rimanere bloccati fuori dal tenant.
  
 

## <a name="what-you-should-know"></a>Informazioni utili 

I ruoli della directory inclusi nei criteri di base sono i ruoli di Azure AD con i maggiori privilegi. In base ai commenti e suggerimenti degli utenti, altri ruoli potrebbero essere inclusi in futuro. 

Se esistono account con privilegi di amministratore negli script, è consigliabile usare invece l'[identità del servizio gestita](managed-service-identity/overview.md) oppure [entità servizio (con certificati)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). Come soluzione alternativa temporanea, è possibile escludere account utente specifici dai criteri di base. 

I criteri si applicano ai flussi di autenticazione legacy, ad esempio POP, IMAP e il client desktop di Office meno recente. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 
