---
title: Sincronizzare gli attributi in Azure AD per il mapping Documenti Microsoft
description: Informazioni su come sincronizzare gli attributi da Active Directory locale ad Azure AD. Quando configuri il provisioning degli utenti nelle app SaaS, usa la funzionalità di estensione della directory per aggiungere attributi di origine che non sono sincronizzati per impostazione predefinita.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522272"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sincronizzare un attributo da Active Directory locale ad Azure AD per il provisioning in un'applicazione

Quando si personalizzano i mapping degli attributi per il provisioning degli utenti, è possibile che l'attributo di cui si desidera eseguire il mapping non venga visualizzato nell'elenco Attributo di **origine.** Questo articolo illustra come aggiungere l'attributo mancante sincronizzandolo da Active Directory (AD) locale ad Azure Active Directory (Azure AD).

Azure AD deve contenere tutti i dati necessari per creare un profilo utente durante il provisioning degli account utente da Azure AD a un'app SaaS.Azure AD must contain all the data required to create a user profile when provisioning user accounts from Azure AD to a SaaS app. In alcuni casi, per rendere disponibili i dati potrebbe essere necessario sincronizzare gli attributi da AD locale ad Azure AD. Azure AD Connect sincronizza automaticamente alcuni attributi con Azure AD, ma non con tutti gli attributi. Inoltre, alcuni attributi (ad esempio SAMAccountName) che vengono sincronizzati per impostazione predefinita potrebbero non essere esposti utilizzando l'API Microsoft Graph. In questi casi, è possibile usare la funzionalità di estensione della directory Azure AD Connect per sincronizzare l'attributo con Azure AD. In questo modo, l'attributo sarà visibile all'API Microsoft Graph e al servizio di provisioning di Azure AD.

Se i dati necessari per il provisioning sono in Active Directory ma non sono disponibili per il provisioning a causa dei motivi descritti in precedenza, attenersi alla seguente procedura.
 
## <a name="sync-an-attribute"></a>Sincronizzare un attributo 

1. Aprire la procedura guidata di Azure AD Connect, scegliere Attività e quindi **Personalizza opzioni di sincronizzazione**.

   ![Pagina Attività aggiuntive della procedura guidata di Azure Active Directory ConnectAzure Active Directory Connect wizard Additional tasks page](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Accedere come amministratore globale di Azure AD. 

3. Nella pagina **Funzionalità facoltative** selezionare **Sincronizzazione attributi estensione directory**.
 
   ![Pagina Delle funzionalità facoltative della procedura guidata di Azure Active Directory ConnectAzure Active Directory Connect wizard Optional features page](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selezionare gli attributi che si desidera estendere ad Azure AD.
   > [!NOTE]
   > Per la ricerca in **Attributi disponibili** viene fatta distinzione tra maiuscole e minuscole.

   ![Pagina di selezione delle estensioni della procedura guidata di Azure Active Directory ConnectAzure Active Directory Connect wizard Directory extensions selection page](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Completare la procedura guidata di Azure AD Connect e consentire l'esecuzione di un ciclo di sincronizzazione completo. Al termine del ciclo, lo schema viene esteso e i nuovi valori vengono sincronizzati tra l'AD locale e Azure AD.
 
6. Nel portale di Azure, durante la modifica dei mapping degli [attributi utente,](customize-application-attributes.md)l'elenco di **attributi Source** conterrà ora l'attributo aggiunto nel formato `<attributename> (extension_<appID>_<attributename>)`. Selezionare l'attributo ed eseguirne il mapping all'applicazione di destinazione per il provisioning.

   ![Pagina di selezione delle estensioni della procedura guidata di Azure Active Directory ConnectAzure Active Directory Connect wizard Directory extensions selection page](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> La possibilità di eseguire il provisioning di attributi di riferimento da Active Directory locale, ad esempio **managedby** o **DN/DistinguishedName**, non è attualmente supportata. È possibile richiedere questa funzione su [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Passaggi successivi

* [Definire chi rientra nell'ambito del provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
