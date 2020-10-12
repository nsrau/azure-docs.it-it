---
title: Funzionamento dell'accesso SSO alle risorse locali nei dispositivi aggiunti ad Azure AD | Microsoft Docs
description: Informazioni su come estendere l'esperienza SSO configurando dispositivi ibridi Azure Active Directory aggiunti.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba802cb86d68298cd4dfff94162069590744833c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91256463"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Funzionamento dell'accesso SSO alle risorse locali nei dispositivi aggiunti ad Azure AD

Non deve sorprendere il fatto che un dispositivo aggiunto ad Azure Active Directory (Azure AD) offra un'esperienza Single Sign-On (SSO) alle app cloud del tenant. Se l'ambiente dispone di un Active Directory locale (AD), è possibile estendere l'esperienza SSO su questi dispositivi a risorse e applicazioni che si basano anche su AD locale. 

Questo articolo illustra il funzionamento di questa caratteristica.

## <a name="prerequisites"></a>Prerequisiti

 Se Azure AD macchine unite in join non sono connesse alla rete dell'organizzazione, è necessaria una VPN o un'altra infrastruttura di rete. SSO locale richiede la comunicazione line-of-visione con i controller di dominio di servizi di dominio Active Directory locali.

## <a name="how-it-works"></a>Funzionamento 

Con un dispositivo aggiunto ad Azure AD, gli utenti possono già usufruire dell'esperienza SSO alle app cloud nell'ambiente. Se l'ambiente ha un Azure AD e un'istanza locale di AD, è possibile espandere l'ambito dell'esperienza SSO con le app line-of-business (LOB) locali, le condivisioni file e le stampanti.

I dispositivi aggiunti ad Azure AD non conoscono l'ambiente AD locale perché non sono aggiunti a esso. Tuttavia, è possibile fornire a questi dispositivi informazioni aggiuntive sull'ambiente AD locale usando Azure AD Connect.

Un ambiente in cui sono presenti sia Azure AD che un'istanza locale di AD è detto anche ambiente ibrido. Se si ha un ambiente ibrido, è probabile che Azure AD Connect sia già stato distribuito per sincronizzare le informazioni sulle identità locali con il cloud. Nell'ambito del processo di sincronizzazione, Azure AD Connect sincronizza le informazioni utente locali per Azure AD. Quando un utente accede a un dispositivo aggiunto ad Azure AD in un ambiente ibrido:

1. Azure AD invia nuovamente al dispositivo i dettagli del dominio locale dell'utente, insieme al [token di aggiornamento primario](concept-primary-refresh-token.md)
1. Il servizio autorità di sicurezza locale (LSA) consente l'autenticazione Kerberos e NTLM nel dispositivo.

Durante un tentativo di accesso a una risorsa che richiede Kerberos o NTLM nell'ambiente locale dell'utente, il dispositivo:

1. Invia le informazioni sul dominio locale e le credenziali utente al controller di dominio individuato per ottenere l'autenticazione dell'utente.
1. Riceve un [ticket di concessione ticket (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) Kerberos o un token NTLM basato sul protocollo supportato dalla risorsa o dall'applicazione locale. Se il tentativo di ottenere il token TGT o NTLM Kerberos per il dominio ha esito negativo (il timeout DCLocator correlato può causare un ritardo), vengono tentate le voci di gestione credenziali o l'utente può ricevere un popup di autenticazione che richiede le credenziali per la risorsa di destinazione.

Tutte le app configurate per l'**autenticazione integrata di Windows** ottengono facilmente l'accesso SSO quando un utente prova ad accedervi.

Windows Hello for Business richiede una configurazione aggiuntiva per abilitare l'accesso SSO locale da un dispositivo aggiunto ad Azure AD. Per altre informazioni, vedere [Configure Azure AD joined devices for On-premises Single-Sign On using Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base) (Configurare dispositivi aggiunti ad Azure AD per l'accesso Single-Sign On locale usando Windows Hello for Business). 

## <a name="what-you-get"></a>Componenti

L'accesso SSO in un dispositivo aggiunto ad Azure AD consente di: 

- Accedere a un percorso UNC in un server membro di AD
- Accedere a un server Web membro di AD configurato per la sicurezza integrata di Windows 

Se si vuole gestire l'istanza locale di AD da un dispositivo Windows, installare [Strumenti di amministrazione remota del server per Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

È possibile usare:

- Lo snap-in Utenti e computer di Active Directory per amministrare tutti gli oggetti di AD. Tuttavia, è necessario specificare il dominio a cui ci si vuole connettere manualmente.
- Lo snap-in DHCP per amministrare un server DHCP aggiunto ad AD. Tuttavia, è necessario specificare il nome o l'indirizzo del server DHCP.
 
## <a name="what-you-should-know"></a>Informazioni importanti

Potrebbe essere necessario modificare le opzioni di [filtro basato su dominio](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) in Azure AD Connect per assicurarsi che i dati relativi ai domini richiesti siano sincronizzati.

App e risorse che dipendono dall'autenticazione del computer di Active Directory non funzionano perché i dispositivi aggiunti ad Azure AD non hanno un oggetto computer in AD. 

Non è possibile condividere i file con altri utenti in un dispositivo aggiunto ad Azure AD.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Informazioni sulla gestione dei dispositivi in Azure Active Directory](overview.md) 
