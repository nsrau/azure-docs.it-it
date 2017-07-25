---
title: Connettere dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10 | Documentazione Microsoft
description: Illustra agli amministratori come configurare Criteri di gruppo per abilitare i dispositivi per l'aggiunta a un dominio nella rete dell'organizzazione.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 39bd4fcbe1b9a197bcf5b8bb33bf6fffae2063fc
ms.contentlocale: it-it
ms.lasthandoff: 03/11/2017

---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Connettere dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10
Negli ultimi 15 anni, per connettere i dispositivi sui cui lavorare le aziende hanno fatto essenzialmente ricorso alla funzione di aggiunta a un dominio. Questo ha permesso agli utenti di accedere ai dispositivi con i relativi account aziendali o dell'istituto di istruzione di Windows Server Active Directory (Active Directory) e agli amministratori IT di gestire interamente tali dispositivi. In genere, le aziende si basano su metodi di creazione dell'immagine per effettuare il provisioning dei dispositivi agli utenti e, per gestirli, usano System Center Configuration Manager (SCCM) o Criteri di gruppo.


Dopo aver connesso i dispositivi ad Azure Active Directory (Azure AD), l'aggiunta a un dominio di Windows 10 offre i vantaggi seguenti:

* Accesso Single Sign-On (SSO) alle risorse di Azure AD da qualsiasi posizione.
* Accesso alla sezione aziendale di Windows Store usando account aziendali o dell'istituto di istruzione, non è necessario un account Microsoft.
* Roaming delle impostazioni utente tra dispositivi conforme ai criteri dell'organizzazione usando account aziendali o dell'istituto di istruzione, non è necessario un account Microsoft.
* Autenticazione avanzata e pratico accesso all'account aziendale o dell'istituto di istruzione con Windows Hello for Business e Windows Hello
* Possibilità di limitare l'accesso solo ai dispositivi conformi alle impostazioni di Criteri di gruppo dei dispositivi aziendali.

## <a name="prerequisites"></a>Prerequisiti
Aggiunta a un dominio continua a essere utile. Tuttavia, per poter sfruttare i vantaggi offerti da Azure AD per l'accesso SSO, il roaming delle impostazioni e l'accesso a Windows Store con account aziendali o dell'istituto di istruzione, è necessario quanto segue:

* Sottoscrizione di Azure AD
* Azure AD Connect per estendere la directory locale ad Azure AD.
* Criteri impostati per connettere dispositivi aggiunti a un dominio ad Azure AD.
* Build di Windows 10 (build 10551 o successiva) per i dispositivi.

Per abilitare Windows Hello for Business e Windows Hello è anche necessario quanto segue:

- **Infrastruttura a chiave pubblica (PKI)** per il rilascio di certificati utente.

- **System Center Configuration Manager Current Branch**: è necessario installare la versione 1606 o successiva.  
Per altre informazioni, vedere: 
    - [Documentazione per System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx)
    - [Blog del team di System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)
    - [Impostazioni di Windows Hello for Business in System Center Configuration Manager](https://docs.microsoft.com/sccm/protect/deploy-use/windows-hello-for-business-settings)

In alternativa alla distribuzione di un'infrastruttura PKI, è possibile soddisfare il requisito seguente:

* Disporre di alcuni controller di dominio con Servizi di dominio Active Directory di Windows Server 2016.

Per abilitare l'accesso condizionale, è possibile creare impostazioni di Criteri di gruppo che consentano l'accesso a dispositivi aggiunti al dominio senza distribuzioni aggiuntive. Per gestire il controllo di accesso in base alla conformità dei dispositivi, è necessario quanto segue:

* System Center Configuration Manager Current Branch (1606 o versione successiva) per scenari Windows Hello for Business

## <a name="deployment-instructions"></a>Istruzioni per la distribuzione

Per eseguire la distribuzione, seguire la procedura indicata in [Come configurare la registrazione automatica dei dispositivi di dominio Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="next-step"></a>Passaggio successivo
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)


