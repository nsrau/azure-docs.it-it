---
title: Migrate Conditional Access policies - Azure Active Directory
description: Informazioni che è necessario conoscere per eseguire la migrazione dei criteri classici nel portale di Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380547"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>What is a policy migration in Azure Active Directory Conditional Access? 

[Conditional Access](../active-directory-conditional-access-azure-portal.md) is a capability of Azure Active directory (Azure AD) that enables you to control how authorized users access your cloud apps. While the purpose is still the same, the release of the new Azure portal has introduced significant improvements to how Conditional Access works.

Valutare la possibilità di eseguire la migrazione dei criteri che non sono stati creati nel portale di Azure per i motivi seguenti:

- È ora possibile gestire scenari che in precedenza non era possibile gestire.
- Il consolidamento consente di ridurre il numero di criteri da gestire.   
- You can manage all your Conditional Access policies in one central location.
- Il portale di Azure classico verrà ritirato.   

This article explains what you need to know to migrate your existing Conditional Access policies to the new framework.
 
## <a name="classic-policies"></a>Criteri classici

In the [Azure portal](https://portal.azure.com), the [Conditional Access - Policies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) page is your entry point to your Conditional Access policies. However, in your environment, you might also have Conditional Access policies you have not created using this page. ovvero i *criteri classici*. Classic policies are Conditional Access policies, you have created in:

- Portale di Azure classico
- Portale di Intune classico
- Portale di Protezione app di Intune

On the **Conditional Access** page, you can access your classic policies by clicking [**Classic policies (preview)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) in the **Manage** section. 

![Azure Active Directory](./media/policy-migration/71.png)

La visualizzazione **Criteri classici** consente di eseguire queste operazioni:

- Filtrare i criteri classici.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Disabilitare i criteri classici.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Review the settings of a classic policy (and to disable it).

   ![Azure Active Directory](./media/policy-migration/74.png)

Una volta disabilitati, i criteri classici non possono più essere ripristinati. È per questo motivo che è possibile modificare l'appartenenza ai gruppi nei criteri classici usando la visualizzazione **Dettagli**. 

![Azure Active Directory](./media/policy-migration/75.png)

Modificando i gruppi selezionati o escludendo gruppi specifici, è possibile testare l'effetto dei criteri classici disabilitati per alcuni utenti di test prima di disabilitare i criteri per tutti gli utenti e i gruppi inclusi. 

## <a name="azure-ad-conditional-access-policies"></a>Azure AD Conditional Access policies

With Conditional Access in the Azure portal, you can manage all your policies in one central location. Because the implementation of how Conditional Access has changed, you should familiarize yourself with the basic concepts before migrating your classic policies.

Vedere:

- [What is Conditional Access in Azure Active Directory](../active-directory-conditional-access-azure-portal.md) to learn about the basic concepts and the terminology.
- [Best practices for Conditional Access in Azure Active Directory](best-practices.md) to get some guidance on deploying Conditional Access in your organization.
- [Require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md) to familiarize yourself with the user interface in the Azure portal.
 
## <a name="migration-considerations"></a>Considerazioni sulla migrazione

In this article, Azure AD Conditional Access policies are also referred to as *new policies*.
I criteri classici continuano a funzionare in parallelo con quelli nuovi finché non vengono disabilitati o eliminati. 

Nella prospettiva di un consolidamento dei criteri è importante considerare gli aspetti seguenti:

- Diversamente dai criteri classici, che sono legati a un'app cloud specifica, i criteri nuovi consentono di selezionare tutte le app cloud desiderate.
- I controlli applicati da criteri classici e criteri nuovi per un'app cloud devono risultare tutti soddisfatti (*AND*). 
- Nei criteri nuovi è possibile:
   - Combinare più condizioni, se necessarie per lo specifico scenario. 
   - Selezionare più requisiti di controllo di accesso e combinarli con un *OR* logico (per richiedere uno dei controlli selezionati) o con un *AND* logico (per richiedere tutti i controlli selezionati).

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Se si intende eseguire la migrazione di criteri classici per **Office 365 Exchange Online** che includono **Exchange Active Sync** come condizione per le app client, può non essere possibile consolidarli in un unico criterio nuovo. 

Ciò avviene, ad esempio, quando si vuole includere il supporto per tutti i tipi di app client. In un criterio nuovo con **Exchange Active Sync** come condizione per le app client, non è possibile selezionare altre app client.

![Azure Active Directory](./media/policy-migration/64.png)

Il consolidamento in un criterio nuovo non è possibile nemmeno se i criteri classici contengono più condizioni. Un criterio nuovo con **Exchange Active Sync** configurato come condizione per le app client non supporta altre condizioni:   

![Azure Active Directory](./media/policy-migration/08.png)

Se si ha un criterio nuovo con **Exchange Active Sync** configurato come condizione per le app client, è necessario assicurarsi che tutte le altre condizioni non siano configurate. 

![Azure Active Directory](./media/policy-migration/16.png)
 
I criteri classici [basati su app](technical-reference.md#approved-client-app-requirement) per Office 365 Exchange Online che includono **Exchange Active Sync** come condizione per le app client consentono l'uso di [piattaforme di dispositivo](technical-reference.md#device-platform-condition) **supportate** e **non supportate**. Anche se non si possono configurare singole piattaforme di dispositivo in un criterio nuovo correlato, è possibile limitare il supporto alle [piattaforme di dispositivo supportate](technical-reference.md#device-platform-condition). 

![Azure Active Directory](./media/policy-migration/65.png)

È possibile consolidare più criteri classici che includono **Exchange Active Sync** come condizione per le app client se questi hanno:

- Solo **Exchange Active Sync** come condizione 
- Più requisiti configurati per concedere l'accesso

Uno scenario comune consiste nel consolidamento dei criteri seguenti:

- Un criterio classico basato su dispositivo configurato nel portale di Azure classico 
- Un criterio classico basato su app configurato nel portale di Protezione app di Intune 
 
In questo caso, è possibile consolidare i criteri classici in un unico criterio nuovo con entrambi i requisiti selezionati.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Piattaforme del dispositivo

I criteri classici con [controlli basati su app](technical-reference.md#approved-client-app-requirement) sono preconfigurati con iOS e Android come [condizione per le piattaforme di dispositivo](technical-reference.md#device-platform-condition). 

In un criterio nuovo è necessario selezionare le [piattaforme di dispositivo](technical-reference.md#device-platform-condition) da supportare singolarmente.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere le [procedure consigliate per l'accesso condizionale](best-practices.md) in Azure Active Directory. 
