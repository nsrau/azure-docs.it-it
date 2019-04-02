---
title: Privacy dell'utente e accesso Single Sign-On facile di Azure AD | Microsoft Docs
description: Questo articolo riguarda l'accesso Single Sign-On facile di Azure Active Directory (Azure AD) e la conformità al Regolamento generale sulla protezione dei dati (GDPR).
services: active-directory
keywords: che cos'è Azure AD Connect, GDPR, componenti richiesti per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9311c1060b953e87f163cb482db14cdd43f50d3d
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791548"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Privacy dell'utente e accesso Single Sign-On facile di Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Panoramica


L'accesso Single Sign-On facile di Azure AD crea il tipo di log seguente che può contenere dati personali: 

- File di log di traccia di Azure AD Connect.

Migliorare la privacy dell'utente per l'accesso Single Sign-On facile in due modi:

1.  Qualora richiesto, estrarre i dati per un utente e rimuovere i dati di tale utente dalle installazioni.
2.  Verificare che nessun dato venga conservato per più di 48 ore.

La seconda opzione è consigliabile perché risulta più semplice da implementare e gestire. Vedere le istruzioni seguenti per ogni tipo di log:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminare i file di log di traccia di Azure AD Connect

Controllare il contenuto della cartella **%ProgramData%\AADConnect** ed eliminare il contenuto del log di traccia (file **trace-\*.log**) di questa cartella entro 48 ore dall'installazione o dall'aggiornamento di Azure AD Connect o dalla modifica della configurazione dell'accesso Single Sign-On facile, perché questa azione può creare dati a cui si applica il regolamento GDPR.

>[!IMPORTANT]
>Non eliminare il file **PersistedState.xml** in questa cartella, perché questo file viene usato per mantenere lo stato dell'installazione precedente di Azure AD Connect e viene usato quando viene eseguita un'installazione di aggiornamento. Questo file non conterrà mai dati relativi a un utente e non deve essere mai eliminato.

È possibile esaminare ed eliminare questi file di log di traccia tramite Esplora risorse oppure è possibile usare lo script di PowerShell seguente per eseguire le azioni necessarie:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Salvare lo script in un file con estensione "PS1". Eseguire questo script quando necessario.

Per altre informazioni sui requisiti GDPR per Azure AD Connect correlati, vedere [questo articolo](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Nota sui log del controller di dominio

Se è abilitata la registrazione di controllo, questo prodotto può generare log di sicurezza per i controller di dominio. Per altre informazioni sulla configurazione dei criteri di controllo, leggere questo [articolo](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Passaggi successivi

* [Consultare l'Informativa sulla privacy Microsoft nel Trust Center](https://www.microsoft.com/trustcenter)
  - [**Risoluzione dei problemi**](tshoot-connect-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.