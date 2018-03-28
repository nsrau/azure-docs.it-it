---
title: 'Azure AD Connect: accesso Single Sign-On facile - Conformità a RGPD | Microsoft Docs'
description: Questo articolo riguarda l'accesso Single Sign-On facile di Azure Active Directory (Azure AD) e la conformità al Regolamento generale sulla protezione dei dati (RGPD).
services: active-directory
keywords: che cos'è Azure AD Connect, RGPD, componenti richiesti per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 0c7ed376accb1eed01106358491e925d3b8126c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-seamless-single-sign-on-gdpr-compliance"></a>Accesso Single Sign-On facile di Azure AD: conformità al Regolamento generale sulla protezione dei dati (RGPD)

## <a name="overview"></a>Panoramica

A maggio 2018 entrerà in vigore una legge sulla privacy europea, il [Regolamento generale sulla protezione dei dati (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm). Il GDPR impone nuove regole per le aziende, gli enti pubblici, le organizzazioni no profit e altre organizzazioni che offrono beni e servizi a persone nell'Unione europea o che raccolgono e analizzano dati collegati a persone residenti nell'Unione europea. Il GDPR si applica indipendentemente da dove si trovano tali aziende, enti e organizzazioni. 

Sono oggi disponibili prodotti e servizi Microsoft che aiutano a soddisfare i requisiti del GDPR. Per altri dettagli sull'Informativa sulla privacy Microsoft, consultare il [Trust Center](https://www.microsoft.com/trustcenter).

L'accesso Single Sign-On facile di Azure AD crea il tipo di log seguente che può contenere informazioni personali degli utenti finali:

- File di log di traccia di Azure AD Connect.

La conformità al Regolamento generale sulla protezione dei dati (RGPD) per l'accesso Single Sign-On facile può essere ottenuta in due modi:

1.  Qualora richiesto, estrarre i dati per un utente e rimuovere i dati di tale utente dalle installazioni.
2.  Verificare che nessun dato venga conservato per più di 48 ore.

La seconda opzione è consigliabile perché risulta più semplice da implementare e gestire. Vedere le istruzioni seguenti per ogni tipo di log:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminare i file di log di traccia di Azure AD Connect

Controllare il contenuto della cartella **%ProgramData%\AADConnect** ed eliminare il contenuto del log di traccia (file **trace-\*.log**) di questa cartella entro 48 ore dall'installazione o dall'aggiornamento di Azure AD Connect o dalla modifica della configurazione dell'accesso Single Sign-On facile, perché questa azione può creare dati a cui si applica il regolamento RGPD.

>[!IMPORTANT]
>Non eliminare il file **PersistedState.xml** in questa cartella, perché questo file viene usato per mantenere lo stato dell'installazione precedente di Azure AD Connect e viene usato quando viene eseguita un'installazione di aggiornamento. Questo file non conterrà mai dati relativi a un utente e non deve essere mai eliminato.

È possibile esaminare ed eliminare questi file di log di traccia tramite Esplora risorse oppure è possibile usare lo script di PowerShell seguente per eseguire le azioni necessarie:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Salvare lo script in un file con estensione "PS1". Eseguire questo script quando necessario.

Per altre informazioni sui requisiti RGPD per Azure AD Connect correlati, vedere [questo articolo](active-directory-aadconnect-gdpr.md).

### <a name="note-about-domain-controller-logs"></a>Nota sui log del controller di dominio

Se è abilitata la registrazione di controllo, questo prodotto può generare log di sicurezza per i controller di dominio. Per altre informazioni sulla configurazione dei criteri di controllo, leggere questo [articolo](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Passaggi successivi

- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-sso.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.
