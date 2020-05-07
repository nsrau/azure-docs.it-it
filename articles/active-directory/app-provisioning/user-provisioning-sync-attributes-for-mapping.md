---
title: Sincronizzare gli attributi Azure AD per il mapping
description: Informazioni su come sincronizzare gli attributi dalla Active Directory locale a Azure AD. Quando si configura il provisioning utenti in app SaaS, usare la funzionalità di estensione della directory per aggiungere gli attributi di origine che non sono sincronizzati per impostazione predefinita.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.openlocfilehash: 4b51d74d188f8f1c99f2075000e8fd91e70a81a4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593149"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sincronizzare un attributo dal Active Directory locale a Azure AD per il provisioning in un'applicazione

Quando si personalizzano i mapping degli attributi per il provisioning degli utenti, si potrebbe notare che l'attributo di cui si vuole eseguire il mapping non viene visualizzato nell'elenco di **attributi di origine** . Questo articolo illustra come aggiungere l'attributo mancante eseguendo la sincronizzazione dal Active Directory locale (AD) al Azure Active Directory (Azure AD).

Azure AD deve contenere tutti i dati necessari per creare un profilo utente durante il provisioning degli account utente da Azure AD a un'app SaaS. In alcuni casi, per rendere disponibili i dati potrebbe essere necessario sincronizzare gli attributi dall'istanza locale di AD Azure AD. Azure AD Connect sincronizza automaticamente determinati attributi per Azure AD, ma non per tutti gli attributi. Inoltre, alcuni attributi, ad esempio SAMAccountName, sincronizzati per impostazione predefinita potrebbero non essere esposti tramite l'API Microsoft Graph. In questi casi, è possibile usare la funzionalità di estensione della directory Azure AD Connect per sincronizzare l'attributo con Azure AD. In questo modo, l'attributo sarà visibile all'API Microsoft Graph e al servizio di provisioning Azure AD.

Se i dati necessari per il provisioning sono in Active Directory ma non sono disponibili per il provisioning a causa dei motivi descritti in precedenza, seguire questa procedura.
 
## <a name="sync-an-attribute"></a>Sincronizzare un attributo 

1. Aprire la procedura guidata Azure AD Connect, scegliere attività, quindi scegliere **Personalizza opzioni di sincronizzazione**.

   ![Pagina attività aggiuntive della procedura guidata Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Accedere come amministratore globale Azure AD. 

3. Nella pagina **funzionalità facoltative** selezionare **sincronizzazione degli attributi di estensione della directory**.
 
   ![Pagina delle funzionalità facoltative della procedura guidata Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selezionare gli attributi che si desidera estendere Azure AD.
   > [!NOTE]
   > La ricerca sotto **gli attributi disponibili** fa distinzione tra maiuscole e minuscole.

   ![Pagina di selezione delle estensioni della directory della procedura guidata Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Terminare la procedura guidata Azure AD Connect e consentire l'esecuzione di un ciclo di sincronizzazione completo. Al termine del ciclo, lo schema viene esteso e i nuovi valori vengono sincronizzati tra AD locale e Azure AD.
 
6. Nel portale di Azure, mentre si [modificano i mapping degli attributi utente](customize-application-attributes.md), l'elenco di **attributi di origine** conterrà ora l'attributo aggiunto nel `<attributename> (extension_<appID>_<attributename>)`formato. Selezionare l'attributo ed eseguirne il mapping all'applicazione di destinazione per il provisioning.

   ![Pagina di selezione delle estensioni della directory della procedura guidata Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> La possibilità di eseguire il provisioning degli attributi di riferimento da AD locale, ad esempio **ManagedBy** o **DN/Distinguishname**, non è attualmente supportata. È possibile richiedere questa funzionalità in [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Passaggi successivi

* [Definire gli utenti che rientrano nell'ambito del provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
