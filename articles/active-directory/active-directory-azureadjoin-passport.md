---
title: "Autenticazione delle identità senza password con Windows Hello for Business e Azure AD | Documentazione Microsoft"
description: Contiene una panoramica di Windows Hello for Business e altre informazioni sulla distribuzione di Windows Hello for Business.
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b10fc7c51d9fee306b0e71d405f0fd7ec7a6816
ms.openlocfilehash: 9270df5e880cc9a512ae673c1c25449376d8a7b4
ms.contentlocale: it-it
ms.lasthandoff: 02/23/2017


---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>Autenticazione delle identità senza password con Windows Hello for Business
Gli attuali metodi di autenticazione basati solo su password non sono sufficienti a garantire la protezione degli utenti. Gli utenti riutilizzano e dimenticano le password. Le password possono causare violazioni della protezione, phishing, oltre ad essere facilmente violabili. Sono inoltre difficili da ricordare e più soggette ad attacchi di tipo "[pass-the-hash](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-windows-hello-for-business"></a>Informazioni su Windows Hello for Business
Windows Hello for Business è un approccio di autenticazione basato su certificato o su chiave privata/pubblica disponibile per organizzazioni e utenti grazie al quale non è più necessario usare password. Questo tipo di autenticazione si basa su credenziali a coppie di chiavi che possono sostituire le password e risultare efficaci in caso di violazioni, furti e phishing.

 Windows Hello for Business consente agli utenti di eseguire l'autenticazione a un account Microsoft, un account di Windows Server Active Directory, un account di Microsoft Azure Active Directory (Azure AD) o un servizio non Microsoft che supporta l'autenticazione FIDO (Fast IDentity Online). Dopo una verifica iniziale in due passaggi durante la registrazione di Windows Hello for Business, nel dispositivo dell'utente viene configurato Windows Hello for Business. L'utente deve quindi impostare un movimento, come Windows Hello o un PIN. L'utente esegue il movimento per verificare la propria identità. Windows usa quindi Windows Hello for Business per autenticare gli utenti e consentire loro di accedere a risorse e servizi protetti.

La chiave privata viene resa disponibile solo tramite un "movimento utente", come un PIN, un movimento biometrico o un dispositivo remoto, ad esempio una smart card usata per accedere al dispositivo. Queste informazioni vengono quindi collegate a un certificato o a una coppia di chiavi asimmetriche. La chiave privata viene attestata a livello hardware se il dispositivo è dotato di chip TPM (Trusted Platform Module). La chiave privata è sempre associata al dispositivo.

La chiave pubblica viene registrata con Azure Active Directory e Windows Server Active Directory (per la configurazione locale). I provider di identità (IDP) convalidano l'utente abbinando la chiave pubblica a quella privata e fornendo le informazioni di accesso tramite OTP (One Time Password), PhoneFactor o un altro meccanismo di notifica.

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>Perché alle aziende conviene scegliere Windows Hello for Business?
L'abilitazione di Windows Hello for Business consente alle aziende di aumentare la protezione delle proprie risorse mediante:

* Configurazione di Windows Hello for Business con l'opzione preferita dall'hardware. Ciò significa che le chiavi verranno generate su TPM 1.2 o TPM 2.0, se disponibile. Quando TPM non è disponibile, la chiave verrà generata dal software.
* Definire la complessità e la lunghezza del PIN e scegliere se abilitare l'utilizzo di Hello nell'organizzazione.
* Configurazione di Windows Hello for Business in modo da supportare scenari analoghi a quelli con smart card usando l'attendibilità basata su certificati.

## <a name="how-windows-hello-for-business-works"></a>Come funziona Windows Hello for Business
1. Le chiavi vengono generate a livello hardware da TPM o dal software. Numerosi dispositivi sono dotati di chip TPM integrato che consente di proteggere l'hardware integrando chiavi di crittografia nei dispositivi. Tramite TPM 1.2 o TPM 2.0 vengono generate chiavi o certificati creati da chiavi generate.
2. Queste chiavi associate all'hardware vengono attestate da TPM.
3. Un singolo movimento di sblocco consente di sbloccare il dispositivo. Questo movimento consente di accedere a più risorse se il dispositivo è stato aggiunto al dominio o ad Azure AD.

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>Come funziona il ciclo di vita di Windows Hello for Business
![Ciclo di vita di Windows Hello for Business](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Il diagramma precedente illustra la coppia di chiavi privata/pubblica e il meccanismo di convalida eseguito dal provider di identità. Ognuno di questi passaggi è illustrato in dettaglio di seguito:

1. L'utente dimostra la propria identità usando diversi metodi integrati (movimenti, smart card fisiche, autenticazione a più fattori) e invia queste informazioni a un provider di identità (IDP), ad esempio Azure Active Directory o Active Directory locale.
2. Il dispositivo crea quindi la chiave, la attesta, prende la parte pubblica della chiave, vi allega le istruzioni sulla postazione, esegue l'accesso e la invia al provider di identità in modo che venga registrata.
3. Non appena la parte pubblica della chiave viene registrata nel provider di identità, questo richiede al dispositivo di autenticarsi eseguendo l'accesso con la parte privata della chiave.
4. Il provider di identità esegue quindi la convalida e rilascia il token di autenticazione che consente all'utente e al dispositivo di accedere alle risorse protette. Gli IdP possono scrivere app multipiattaforma o usare il supporto del browser tramite API JavaScript/Webcrypto per creare e usare credenziali di Windows Hello for Business per i propri utenti.

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>Requisiti di distribuzione per Windows Hello for Business
### <a name="at-the-enterprise-level"></a>A livello aziendale
* L'azienda ha una sottoscrizione di Azure.

### <a name="at-the-user-level"></a>A livello dell'utente
* Nel computer dell'utente viene eseguito Windows 10 Professional o Enterprise.

Per istruzioni dettagliate sulla distribuzione, vedere [Abilitare Windows Hello for Business all'interno dell'organizzazione](active-directory-azureadjoin-passport-deployment.md).

## <a name="additional-information"></a>Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)


