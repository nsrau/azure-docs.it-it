---
title: Informazioni sulla protezione di base nell'accesso condizionale di Azure Active Directory - Anteprima | Microsoft Docs
description: Informazioni su come la protezione di base garantisce di avere almeno il livello di base della sicurezza abilitato nell'ambiente di Azure Active Directory.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 856673d2a5465f9646172a1436ed75c0d73692cb
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003173"
---
# <a name="what-is-baseline-protection-preview"></a>Che cos'è la protezione di base (anteprima)?  

Nell'ultimo anno gli attacchi alle identità sono aumentati del 300%. Per proteggere l'ambiente dalla continua crescita degli attacchi, Azure Active Directory (Azure AD) introduce una nuova funzionalità denominata protezione di base. La protezione di base è un set di [criteri di accesso condizionale](../active-directory-conditional-access-azure-portal.md) predefiniti. L'obiettivo di questi criteri è garantire che in tutte le edizioni di Azure AD sia abilitato almeno il livello di base della sicurezza. 

Questo articolo offre una panoramica della protezione di base in Azure Active Directory.


 
## <a name="require-mfa-for-admins"></a>Richiedere l'autenticazione a più fattori per gli amministratori

Gli utenti con accesso ad account con privilegi hanno accesso illimitato all'ambiente. Considerate le facoltà di questi account, è consigliabile trattarli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma di verifica degli account più avanzata quando vengono usati per l'accesso. In Azure Active Directory è possibile richiedere l'autenticazione a più fattori (MFA) per ottenere una verifica degli account più avanzata.  

**Richiedere l'autenticazione a più fattori per gli amministratori** è un criterio di base che rende obbligatoria l'autenticazione a più fattori per i ruoli della directory seguenti: 

- Amministratore globale  

- Amministratore di SharePoint  

- Amministratore di Exchange  

- Amministratore di accesso condizionale  

- Amministratore della sicurezza  


![Azure Active Directory](./media/baseline-protection/01.png)

Questi criteri di base offrono la possibilità di escludere utenti e gruppi. È possibile escludere un *[account amministrativo di accesso di emergenza](../users-groups-roles/directory-emergency-access.md)* per assicurarsi di non rimanere bloccati fuori dal tenant.


## <a name="enable-a-baseline-policy"></a>Abilitare un criterio di base 

Per impostazione predefinita, i criteri di base, mentre sono in anteprima, non sono attivati. Per attivare un criterio, è necessario abilitarlo manualmente. Non appena questa funzionalità è disponibile a livello generale, i criteri vengono attivati per impostazione predefinita. La modifica del comportamento pianificata è il motivo per cui è anche necessario attivare e disattivare una terza opzione per impostare lo stato di un criterio: **Abilita automaticamente i criteri in futuro**. Selezionando questa opzione, si consente a Microsoft di decidere quando attivare un criterio.      


**Per abilitare un criterio di base:**  

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.

2. Nel **portale di Azure**, sulla barra di spostamento a sinistra, fare clic su **Azure Active Directory**.

    ![Azure Active Directory](./media/baseline-protection/02.png)

3. Nella sezione **Sicurezza** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Accesso condizionale](./media/baseline-protection/05.png)

4. Nell'elenco dei criteri fare clic su un criterio che inizia con **Baseline policy:** (Criteri di base). 

5. Per abilitare i criteri, fare clic su **Usa i criteri immediatamente**.

6. Fare clic su **Save**. 
 
  
 

## <a name="what-you-should-know"></a>Informazioni utili 

Anche se la gestione dei criteri di accesso condizionale personalizzati richiede una licenza Azure AD Premium, i criteri di base sono disponibili in tutte le edizioni di Azure AD.     

I ruoli della directory inclusi nei criteri di base sono i ruoli di Azure AD con i maggiori privilegi. 

Se esistono account con privilegi usati negli script, è consigliabile sostituirli con l'[identità del servizio gestita](../managed-service-identity/overview.md) oppure con le [entità servizio con certificati](../../azure-resource-manager/resource-group-authenticate-service-principal.md). Come soluzione alternativa temporanea, è possibile escludere account utente specifici dai criteri di base. 

I criteri di base si applicano ai flussi di autenticazione legacy, ad esempio POP, IMAP e il client desktop di Office meno recente. 




## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

- [Cinque passaggi per proteggere l'infrastruttura di identità](https://docs.microsoft.com/azure/security/azure-ad-secure-steps)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](overview.md) 

