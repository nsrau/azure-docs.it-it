---
title: Prerequisiti di Azure Data Catalog | Documentazione Microsoft
description: 'Prerequisiti di Azure Data Catalog: tutte le informazioni per iniziare a usare Azure Data Catalog.'
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 412063692e87fe71890f62bcc18b99b317a80986


---
# <a name="azure-data-catalog-prerequisites"></a>Prerequisiti del Catalogo dati di Azure
## <a name="what-do-i-need-to-get-started-with-azure-data-catalog"></a>Prerequisiti per iniziare a usare il Catalogo dati di Azure
Vi sono alcune cose di cui è necessario occuparsi prima di poter impostare il **Catalogo dati di Azure**. Nessuna c’è da preoccuparsi: occorre poco tempo.

## <a name="azure-subscription"></a>Sottoscrizione di Azure
Per configurare Azure Data Catalog l'utente deve essere proprietario o comproprietario di una sottoscrizione di Azure.

Le sottoscrizioni di Azure consentono di organizzare l'accesso alle risorse del servizio cloud, come Catalogo dati di Azure. Consentono inoltre di controllare come l'utilizzo delle risorse viene segnalato, fatturato e pagato. Ogni sottoscrizione può disporre di un’impostazione di fatturazione e pagamento diversa, in modo da poter avere sottoscrizioni e piani diversi per reparto, progetto, ufficio regionale e così via. Ogni servizio cloud appartiene a una sottoscrizione ed è necessario che la sottoscrizione sia disponibile prima di impostare il Catalogo dati di Azure. Per altre informazioni, vedere l'articolo su come [gestire gli account, le sottoscrizioni e i ruoli amministrativi](../active-directory/active-directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Per configurare Azure Data Catalog, è necessario accedere con un account utente di Azure Active Directory.

Azure Active Directory (Azure AD) è un servizio che offre semplici e pratiche funzionalità di gestione delle identità e degli accessi, sia nel cloud sia in locale. Gli utenti possono utilizzare un singolo account aziendale o dell'istituto di istruzione per eseguire il Single Sign-On a qualsiasi applicazione Web locale o nel cloud. Il Catalogo dati di Azure utilizza Azure AD per autenticare il Single Sign-On. Per altre informazioni, vedere [Informazioni su Azure Active Directory](../active-directory/active-directory-whatis.md)

> [!NOTE]
> Il [portale di Azure](http://portal.azure.com/) consente agli utenti di accedere con un account Microsoft personale oppure con un account aziendale o dell'istituto di istruzione di Azure Active Directory. Per configurare Azure Data Catalog usando il portale di Azure o il [portale di Data Catalog](http://www.azuredatacatalog.com) , è necessario eseguire l'accesso con un account di Azure Active Directory, non con un account personale.
>
>

## <a name="active-directory-policy-configuration"></a>Configurazione dei criteri di Azure Active Directory
In alcuni casi, è possibile che si verifichi una situazione in cui gli utenti possono accedere al portale del Catalogo dati di Azure, ma, quando tentano di accedere allo strumento di registrazione dell'origine dati, viene restituito un messaggio di errore che impedisce l'accesso. Questo errore si verifica solo quando l'utente è nella rete aziendale o si connette dall'esterno della rete aziendale.

Lo strumento di registrazione dell'origine dati usa l'autenticazione basata su form per convalidare gli accessi degli utenti in Active Directory. Per accedere, l'autenticazione basata su form deve essere abilitata nei criteri di autenticazione globali da un amministratore di Active Directory.

I criteri di autenticazione globali consentono di abilitare i metodi di autenticazione separatamente per le connessioni Extranet e Intranet, come illustrato di seguito. Se l'autenticazione basata su form non è abilitata per la rete da cui l'utente si connette, è possibile che si verifichino errori di accesso.

 ![Criteri di autenticazione globali di Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Per altre informazioni, vedere [Configurare i criteri di autenticazione](https://technet.microsoft.com/library/dn486781.aspx).



<!--HONumber=Nov16_HO3-->


