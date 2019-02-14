---
title: Qual è la migrazione di criteri nell'accesso condizionale di Azure Active Directory? | Microsoft Docs
description: Informazioni che è necessario conoscere per eseguire la migrazione dei criteri classici nel portale di Azure.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/24/2018
ms.author: markvi
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f93b9d6fb6e79ce348dc9bed64452536d5132f7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169276"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Qual è la migrazione di criteri nell'accesso condizionale di Azure Active Directory? 


L'[accesso condizionale](../active-directory-conditional-access-azure-portal.md) è una capacità di Azure Active Directory che consente di controllare come gli utenti autorizzati accedono alle app cloud. Anche se la finalità è la stessa, il nuovo portale di Azure ha introdotto miglioramenti significativi nel funzionamento dell'accesso condizionale.

È consigliabile eseguire la migrazione di criteri non creati nel portale di Azure per i motivi seguenti:

- È ora possibile gestire scenari che in precedenza non era possibile gestire.

- Il consolidamento consente di ridurre il numero di criteri da gestire.   

- È possibile gestire tutti i criteri di accesso condizionale in un'unica posizione centrale.

- Il portale di Azure classico verrà ritirato.   

Questo articolo spiega cosa è necessario sapere per eseguire la migrazione dei criteri di accesso condizionale esistenti nel nuovo framework.
 
## <a name="classic-policies"></a>Criteri classici

Nel [portale di Azure](https://portal.azure.com) la pagina [Accesso condizionale - Criteri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) è il punto di ingresso per i criteri di accesso condizionale. È tuttavia possibile che nel proprio ambiente siano impostati criteri di accesso condizionale che non sono stati creati tramite questa pagina, ovvero i *criteri classici*. Per criteri classici si intendono i criteri di accesso condizionale creati in uno dei portali seguenti:

- Portale di Azure classico
- Portale di Intune classico
- Portale di Protezione app di Intune


Nella pagina **Accesso condizionale** è possibile accedere ai criteri classici facendo clic su [**Criteri classici (anteprima)**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) nella sezione **Gestione**. 


![Azure Active Directory](./media/policy-migration/71.png)


La visualizzazione **Criteri classici** consente di eseguire queste operazioni:

- Filtrare i criteri classici.
 
    ![Azure Active Directory](./media/policy-migration/72.png)

- Disabilitare i criteri classici.

    ![Azure Active Directory](./media/policy-migration/73.png)
   
- Rivedere le impostazioni dei criteri classici e disabilitarli.

    ![Azure Active Directory](./media/policy-migration/74.png)


Una volta disabilitati, i criteri classici non possono più essere ripristinati. È per questo motivo che è possibile modificare l'appartenenza ai gruppi nei criteri classici usando la visualizzazione **Dettagli**. 

![Azure Active Directory](./media/policy-migration/75.png)

Modificando i gruppi selezionati o escludendo gruppi specifici, è possibile testare l'effetto dei criteri classici disabilitati per alcuni utenti di test prima di disabilitare i criteri per tutti gli utenti e i gruppi inclusi. 



## <a name="azure-ad-conditional-access-policies"></a>Criteri di accesso condizionale di Azure Active Directory

Con l'accesso condizionale nel portale di Azure è possibile gestire tutti i criteri in un'unica posizione centrale. Poiché l'implementazione dell'accesso condizionale è cambiata in modo significativo, è necessario acquisire familiarità con i concetti di base prima di eseguire la migrazione dei criteri classici.

Vedere:

- [Qual è l'accesso condizionale in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md) per apprendere i concetti di base e la terminologia.

- [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md) per ottenere alcune indicazioni su come distribuire l'accesso condizionale all'interno di un'organizzazione.

- [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md) per acquisire familiarità con l'interfaccia utente nel portale di Azure.


 
## <a name="migration-considerations"></a>Considerazioni sulla migrazione

In questo articolo si fa riferimento ai criteri di accesso condizionale di Azure AD anche con il termine *criteri nuovi*.
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

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md). 
