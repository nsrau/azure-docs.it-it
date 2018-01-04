---
title: Migrare i criteri classici nel portale di Azure | Microsoft Docs
description: Informazioni su cosa occorre per eseguire la migrazione di criteri classici nel portale di Azure.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 16628bd4fa41d2e7697e1c2501f2ccd31dbd0496
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migrare i criteri classici nel portale di Azure 


L'[accesso condizionale](active-directory-conditional-access-azure-portal.md) è una capacità di Azure Active Directory che consente di controllare come gli utenti autorizzati accedono alle app cloud. Mentre lo scopo è comunque lo stesso, la versione del nuovo portale di Azure ha introdotto i miglioramenti significativi di funzionamento dell'accesso condizionale.

È consigliabile eseguire la migrazione di criteri che non è stato creato nel portale di Azure perché:

- È possibile soddisfare gli scenari che non è possibile gestire prima.

- Il consolidamento consente di ridurre il numero di criteri da gestire.   

- È possibile gestire tutti i criteri di accesso condizionale in un'unica posizione centrale.

- Portale di Azure classico verrà ritirato.   

Questo articolo spiega cosa occorre per eseguire la migrazione i criteri di accesso condizionale esistenti per il nuovo framework.
 
## <a name="classic-policies"></a>Criteri classici

Nel [portale di Azure](https://portal.azure.com), [accesso condizionale - criteri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) pagina è il punto di ingresso per l'accesso condizionale, i criteri. Tuttavia, nell'ambiente in uso, può inoltre essere criteri di accesso condizionale che non è stato creato tramite questa pagina. Questi criteri sono note come *criteri classici*. Criteri classici sono criteri di accesso condizionale, è stato creato in:

- portale di Azure classico
- Il portale classico di Intune
- Il portale di Intune App Protection


Nel **accesso condizionale** pagina, è possibile accedere ai criteri classici, fare clic su [ **criteri classica (anteprima)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) nel **Gestisci** sezione. 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


Il **criteri classica** visualizzazione fornisce un'opzione per:

- Filtrare i criteri classici.
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Disabilitare i criteri classici.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Verificare le impostazioni dei criteri classico (e per disabilitarlo).

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


Se un criterio classico è disabilitata, è non possibile ripristinare più questo passaggio. È per questo motivo è possibile modificare l'appartenenza al gruppo in un criterio classico con il **dettagli** visualizzazione. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

Modificando i gruppi selezionati o escludendo i gruppi specifici, è possibile testare l'effetto dei criteri disabilitati classico per alcuni utenti di test prima di disabilitare i criteri per inclusi tutti gli utenti e gruppi. 



## <a name="azure-ad-conditional-access-policies"></a>Criteri di accesso condizionale di Azure Active Directory

Con l'accesso condizionale nel portale di Azure, è possibile gestire tutti i criteri in un'unica posizione centrale. Poiché l'implementazione dell'accesso condizionale è stato modificato in modo significativo, è necessario acquisire familiarità con i concetti di base prima della migrazione dei criteri classici.

Vedere:

- [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md) per apprendere i concetti di base e la terminologia.

- [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md) per ottenere alcune indicazioni sulla distribuzione di accesso condizionale all'interno dell'organizzazione.

- [Iniziare con l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) per acquisire familiarità con l'interfaccia utente nel portale di Azure.


 
## <a name="migration-considerations"></a>Considerazioni sulla migrazione

In questo articolo, criteri di accesso condizionale di Azure AD sono anche detto *nuovi criteri*.
I criteri classici continuano a funzionare in modo affiancato con i nuovi criteri finché non si disabilita o eliminarli. 

Gli aspetti seguenti sono importanti nel contesto di un consolidamento di criteri:

- Mentre classici criteri sono legati a un'applicazione specifica cloud, è possibile selezionare quante più App cloud che è necessario in un nuovo criterio.

- I controlli di un criterio classico e un nuovo criterio per un'applicazione cloud richiedono tutti i controlli (*AND*) venga soddisfatta. 


- In un nuovo criterio, è possibile:
 
    - Combinare più condizioni, se richiesto dal proprio scenario. 

    - Selezionare diversi concedere requisiti di accesso di controllo e combinano con una logica *OR* (richiede uno dei controlli selezionati) o con una logica *AND* (richiedono tutti i controlli selezionati).

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Se si desidera eseguire la migrazione di criteri classici per **Office 365 Exchange online** che includono **Exchange Active Sync** come condizione di App client, potrebbe non essere in grado di consolidare in un nuovo criterio. 

Questo avviene, ad esempio, se si desidera supportare tutti i tipi di app client. In un nuovo criterio con **Exchange Active Sync** come condizione di App client, non è possibile selezionare altre applicazioni client.

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

Un consolidamento in un nuovo criterio non è possibile se i criteri classici contengano diverse condizioni. Un nuovo criterio con **Exchange Active Sync** condizione configurato come le applicazioni client non supporta altre condizioni:   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Se si dispone di un nuovo criterio con **Exchange Active Sync** come le applicazioni client condizione configurato, è necessario assicurarsi che tutte le altre condizioni non sono configurate. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

[Basato su app](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) classici criteri per Office 365 Exchange Online che includono **Exchange Active Sync** come condizione di App client consentire **supportati** e **non supportato** [piattaforme per dispositivi](active-directory-conditional-access-technical-reference.md#device-platform-condition). Mentre non è possibile configurare le piattaforme per dispositivi singoli in un nuovo criterio correlato, è possibile limitare il supporto per [piattaforme per dispositivi supportate](active-directory-conditional-access-technical-reference.md#device-platform-condition) solo. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

È possibile consolidare più classici criteri che includono **Exchange Active Sync** come condizione di App client se dispongono di:

- Solo **Exchange Active Sync** come condizione 

- Diversi requisiti per la concessione dell'accesso configurato

Uno scenario comune è il consolidamento di:

- Un criterio basato su dispositivi classico dal portale di Azure classico 
- Un criterio basato su app classico nel portale di protezione di Intune app 
 
In questo caso, è possibile consolidare i criteri classici in un nuovo criterio con entrambi i requisiti selezionati.

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Piattaforme del dispositivo

Criteri classici con [controlli basati su app](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) sono preconfigurati con iOS e Android come il [condizione piattaforma dispositivo](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

In un nuovo criterio, è necessario selezionare il [piattaforme per dispositivi](active-directory-conditional-access-technical-reference.md#device-platform-condition) si desidera supportare singolarmente.

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 
