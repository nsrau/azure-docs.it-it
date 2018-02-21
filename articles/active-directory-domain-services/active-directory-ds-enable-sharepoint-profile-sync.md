---
title: 'Servizi di dominio di Azure Active Directory: Abilitare il supporto per il servizio profilo utente di SharePoint | Documentazione Microsoft'
description: Configurare i domini gestiti dei servizi di dominio di Azure Active Directory per supportare la sincronizzazione dei profili per SharePoint Server
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: d98fbe85f35932161476a800a2fd438f0bf2b9ef
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Configurare un dominio gestito per supportare la sincronizzazione dei profili per SharePoint Server
SharePoint Server include un servizio profili utente utilizzato per la sincronizzazione dei profili utente. Per impostare il servizio profili utente, è necessario concedere le autorizzazioni appropriate in un dominio di Active Directory. Per ulteriori informazioni, vedere [Concedere a Servizi di dominio di Active Directory le autorizzazioni per la sincronizzazione dei profili in SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

In questo articolo viene illustrato come è possibile configurare domini gestiti di servizi di dominio Azure AD per distribuire il servizio di sincronizzazione dei profili utente di SharePoint Server.

## <a name="the-aad-dc-service-accounts-group"></a>Il gruppo 'AAD DC Service Accounts'
Un gruppo di sicurezza denominato '**AAD DC Service Accounts**' è disponibile all'interno dell'unità organizzativa 'Utenti' nel dominio gestito. È possibile visualizzare questo gruppo nello snap-in MMC **Utenti e computer di Active Directory** nel dominio gestito.

![Il gruppo di sicurezza AAD DC Service Accounts](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Ai membri di questo gruppo di sicurezza vengono delegati i privilegi seguenti:
- Il privilegio 'Replica modifiche directory' nella directory principale DSE del dominio gestito.
- Il privilegio 'Replica modifiche Directory' nel contesto di denominazione della configurazione (cn = contenitore di configurazione) del dominio gestito.

Questo gruppo di sicurezza è anche membro del gruppo predefinito **Accesso compatibile precedente a Windows 2000**.

![Il gruppo di sicurezza AAD DC Service Accounts](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Abilitare il dominio gestito per supportare la sincronizzazione di profili utente di SharePoint Server
È possibile aggiungere l'account di servizio utilizzato per la sincronizzazione dei profili utente di SharePoint per il gruppo **AAD DC Service Accounts**. Di conseguenza, l'account di sincronizzazione ottiene i privilegi adeguati per replicare le modifiche alla directory. Questo passaggio di configurazione consente il corretto funzionamento della sincronizzazione dei profili utente di SharePoint Server.

![AAD DC Service Accounts - aggiunta di membri](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![AAD DC Service Accounts - aggiunta di membri](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Contenuti correlati
* [Riferimento tecnico - Concedere a Servizi di dominio di Active Directory le autorizzazioni per la sincronizzazione dei profili in SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
