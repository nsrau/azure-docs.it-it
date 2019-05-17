---
title: Sincronizzare gli attributi in Azure AD per il mapping | Microsoft Docs
description: Informazioni su come sincronizzare gli attributi da Active Directory locale ad Azure AD. Quando si configura provisioning utenti in App SaaS, usare la funzionalità di estensione di directory per aggiungere gli attributi di origine che non sono sincronizzati per impostazione predefinita.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806117"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sincronizzare un attributo da Active Directory locale ad Azure AD per il provisioning in un'applicazione

Durante la personalizzazione dei mapping degli attributi per il provisioning utenti, si noterà che l'attributo che si desidera eseguire il mapping non viene visualizzato nei **attributo di origine** elenco. Questo articolo illustra come aggiungere l'attributo mancano eseguendo la sincronizzazione da locale Active Directory (AD) per Azure Active Directory (Azure AD).

Azure AD deve contenere tutti i dati necessari per creare un profilo utente durante il provisioning degli account utente da Azure AD in un'app SaaS. In alcuni casi, per rendere disponibili i dati si potrebbe essere necessario sincronizzare gli attributi da on-premises AD Azure ad. Azure AD Connect Sincronizza automaticamente determinati attributi in Azure AD, ma non tutti gli attributi. Inoltre, alcuni attributi (ad esempio SAMAccountName) che vengono sincronizzate per impostazione predefinita potrebbero non essere esposte tramite l'API Graph di Azure AD. In questi casi, è possibile usare la funzionalità di estensione delle directory Azure AD Connect per sincronizzare l'attributo ad Azure AD. In questo modo, l'attributo sarà visibile per l'API Graph di Azure AD e il servizio di provisioning di Azure AD.

Se i dati che necessari per il provisioning in Active Directory, ma non sono disponibili per il provisioning per i motivi descritti in precedenza, seguire questa procedura.
 
## <a name="sync-an-attribute"></a>Sincronizzare un attributo 

1. Aprire la procedura guidata Azure AD Connect, scegliere attività e quindi scegliere **personalizzare le opzioni di sincronizzazione**.

   ![Azure Active Directory Connect pagina della procedura guidata attività aggiuntive](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Accedere come amministratore globale di Azure AD. 

3. Nel **funzionalità facoltative** pagina, selezionare **sincronizzazione attributi estensione della Directory**.
 
   ![Azure Active Directory Connect funzionalità facoltative pagina della procedura guidata](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selezionare gli attributi che si desidera estendere ad Azure AD.
   > [!NOTE]
   > La ricerca nella **attributi disponibili** distinzione maiuscole / minuscole.

   ![Azure Active Directory Connect guidata estensioni selezione pagina Directory](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Completare la procedura guidata Azure AD Connect e consentono di eseguire un ciclo di sincronizzazione completa. Una volta completato il ciclo, viene esteso lo schema e i nuovi valori vengono sincronizzati tra on-premises AD e Azure AD.
 
6. Nel portale di Azure, mentre sei [modifica dei mapping degli attributi utente](customize-application-attributes.md), il **attributo di origine** elenco conterranno ora l'attributo aggiunto nel formato `<attributename> (extension_<appID>_<attributename>)`. Selezionare l'attributo ed eseguirne il mapping all'applicazione di destinazione per il provisioning.

   ![Azure Active Directory Connect guidata estensioni selezione pagina Directory](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> La possibilità di effettuare il provisioning di attributi di riferimento da Active Directory locale, ad esempio **managedby** oppure **DN/DistinguishedName**, non è attualmente supportata. È possibile richiedere questa funzionalità nella [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Passaggi successivi

* [Definire chi è nell'ambito del provisioning](define-conditional-rules-for-provisioning-user-accounts.md)
