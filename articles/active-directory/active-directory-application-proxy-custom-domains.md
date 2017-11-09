---
title: Domini personalizzati nel proxy di applicazione di Azure AD | Documentazione Microsoft
description: Gestire domini personalizzati nel proxy di applicazione di Azure AD in modo che l'URL per l'app resti invariato indipendentemente da dove gli utenti vi accedono.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: c15808a97d4efa5f9453f33d9ac70ebde5288201
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Utilizzo di domini personalizzati nel Proxy di applicazione AD Azure

Quando si pubblica un'applicazione tramite il proxy di applicazione di Azure Active Directory, si crea un URL esterno a cui gli utenti possono passare quando lavorano in remoto. Questo URL ottiene il dominio predefinito *tenant.msappproxy.net*. Ad esempio, se è stata pubblicata un'app chiamata Spese e il tenant si chiama Contoso, l'URL esterno sarà https://spese-contoso.msappproxy.net. Per usare il proprio nome di dominio, configurare un dominio personalizzato per l'applicazione. 

È consigliabile configurare domini personalizzati per le applicazioni ogni volta che è possibile. Ecco alcuni dei vantaggi offerti dai domini personalizzati:

- Gli utenti possono accedere all'applicazione con lo stesso URL, che lavorino all'interno o all'esterno della rete.
- Se tutte le applicazioni hanno gli stessi URL interni ed esterni, i collegamenti in un'applicazione che puntano a un'altra applicazione continuano a funzionare anche all'esterno della rete aziendale. 
- È possibile controllare la personalizzazione e creare gli URL desiderati. 


## <a name="configure-a-custom-domain"></a>Configurare un dominio personalizzato

### <a name="prerequisites"></a>Prerequisiti

Prima di configurare un dominio personalizzato, assicurarsi che siano stati preparati i requisiti seguenti: 
- Un [dominio verificato aggiunto ad Azure Active Directory](active-directory-domains-add-azure-portal.md).
- Un certificato personalizzato per il dominio, sotto forma di file PFX. 
- Un'app locale [pubblicata tramite il proxy di applicazione](application-proxy-publish-azure-portal.md).

### <a name="configure-your-custom-domain"></a>Configurare il dominio personalizzato

Quando questi tre requisiti sono pronti, seguire questi passaggi per configurare il dominio personalizzato:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni** e scegliere l'app da gestire.
3. Selezionare **Proxy dell'applicazione**. 
4. Nel campo URL esterno usare l'elenco a discesa per selezionare il dominio personalizzato. Se il dominio non viene visualizzato nell'elenco, significa che non è stato ancora verificato. 
5. Selezionare **Salva**
5. Il campo **Certificato** che era disabilitato viene abilitato. Selezionare questo campo. 

   ![Fare clic per caricare un certificato](./media/active-directory-application-proxy-custom-domains/certificate.png)

   Se è già stato caricato un certificato per il dominio, il campo Certificato visualizza le informazioni sul certificato. 

6. Caricare il certificato PFX e immettere la relativa password. 
7. Fare clic su **Salva** per salvare le modifiche. 
8. Aggiungere un [record DNS](../dns/dns-operations-recordsets-portal.md) che reindirizza il nuovo URL esterno al dominio msappproxy.net. 

>[!TIP] 
>È sufficiente caricare un certificato per ogni dominio personalizzato. Dopo aver caricato un certificato, è possibile scegliere il dominio personalizzato quando si pubblica una nuova app, senza dover eseguire configurazioni aggiuntive, ad eccezione che per il record DNS. 

## <a name="manage-certificates"></a>Gestire i certificati

### <a name="certificate-format"></a>Formato del certificato
Non ci sono restrizioni per i metodi di firma del certificato. Sono supportati certificati ECC (Curve Cryptography), SAN (Subject Alternative Name) e altri tipi comuni di certificato. 

È possibile usare un certificato con caratteri jolly, purché i caratteri jolly corrispondano all'URL esterno desiderato. 

### <a name="changing-the-domain"></a>Modifica del dominio
Tutti i domini verificati vengono visualizzati nell'elenco a discesa URL esterno per l'applicazione. Per modificare il dominio, è sufficiente aggiornare tale campo per l'applicazione. Se il dominio desiderato non è nell'elenco, [aggiungerlo come dominio verificato](active-directory-domains-add-azure-portal.md). Se si seleziona un dominio cui non è ancora associato un certificato, seguire i passaggi da 5 a 7 per aggiungere il certificato. Assicurarsi quindi di aggiornare il record DNS per il reindirizzamento dal nuovo URL esterno. 

### <a name="certificate-management"></a>Gestione dei certificati
È possibile usare lo stesso certificato per più applicazioni, a meno che le applicazioni condividano un host esterno. 

Quando un certificato scade viene visualizzato un avviso, che richiede di caricare un altro certificato tramite il portale. Se il certificato viene revocato, gli utenti potrebbero vedere un avviso di sicurezza quando accedono all'applicazione. Non vengono eseguiti controlli delle revoche dei certificati.  Per aggiornare il certificato per una determinata applicazione, passare all'applicazione e seguire i passaggi da 5 a 7 per la configurazione di domini personalizzati in applicazioni pubblicate per caricare un nuovo certificato. Se il certificato precedente non è usato da altre applicazioni, viene eliminato automaticamente. 

Attualmente la gestione di tutti i certificati avviene tramite singole pagine dell'applicazione, quindi è necessario gestire i certificati nel contesto delle applicazioni pertinenti. 

## <a name="next-steps"></a>Passaggi successivi
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md) alle app pubblicate con l'autenticazione di Azure AD.
* [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md) alle app pubblicate.
* [Aggiungere un nome di dominio personalizzato ad Azure AD](active-directory-domains-add-azure-portal.md)


