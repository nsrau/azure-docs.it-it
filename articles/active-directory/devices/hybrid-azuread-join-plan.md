---
title: Come pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido in Azure Active Directory (Azure AD) | Microsoft Docs
description: Informazioni su come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2019
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: de80825ccdd331f57dcd31d307196dc0b45b9cc9
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294587"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Procedura: Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido

Analogamente agli utenti, i dispositivi stanno diventando un'altra identità da proteggere e da usare per proteggere le risorse in qualsiasi momento e ovunque. Questo obiettivo si raggiunge trasferendo le identità dei dispositivi in Azure AD usando uno dei metodi seguenti:

- Aggiunta ad Azure AD
- Aggiunta ad Azure AD ibrido
- Registrazione di Azure AD

Con il trasferimento dei dispositivi in Azure AD si ottimizza la produttività degli utenti tramite il Single Sign-On (SSO) in tutte le risorse locali e nel cloud. Analogamente, è possibile proteggere l'accesso alle risorse locali e nel cloud con l'[accesso condizionale](../active-directory-conditional-access-azure-portal.md).

Se in un ambiente Active Directory locale Per aggiungere ad Azure AD i dispositivi aggiunti a un dominio in un ambiente Active Directory locale, è possibile configurare dispositivi aggiunti all'identità ibrida di Azure AD. Questo articolo fornisce i passaggi correlati per implementare un'aggiunta ad Azure AD ibrido nell'ambiente. 


## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia familiarità con quanto descritto in [Introduzione alla gestione dei dispositivi in Azure Active Directory](../device-management-introduction.md).

>[!NOTE]
>  Minima richiesta per i livelli di funzionalità foresta e dominio funzionale per il join di Windows 10 ad Azure AD ibrido è Windows Server 2008 R2. In versioni precedenti, l'utente potrebbe non ottenere un Token di aggiornamento primario durante l'accesso di Windows a causa di problemi LSA 

## <a name="plan-your-implementation"></a>Pianificare l'implementazione

Per pianificare l'implementazione di Azure AD ibrido, è necessario acquisire familiarità con:

|   |   |
|---|---|
|![Controllo][1]|Esaminare i dispositivi supportati|
|![Controllo][1]|Esaminare le informazioni utili|
|![Controllo][1]|Esaminare come controllare l'aggiunta dei dispositivi all'identità ibrida di Azure AD|
|![Controllo][1]|Selezionare lo scenario|


 

## <a name="review-supported-devices"></a>Esaminare i dispositivi supportati 

L'aggiunta ad Azure AD ibrido supporta un'ampia gamma di dispositivi Windows. Poiché la configurazione per i dispositivi che eseguono versioni precedenti di Windows richiede passaggi aggiuntivi o diversi, i dispositivi supportati sono raggruppati in due categorie:

**Dispositivi Windows correnti**

- Windows 10
    
- Windows Server 2016


Per i dispositivi che eseguono il sistema operativo desktop Windows, è supportata la versione 1607 (Aggiornamento dell'anniversario di Windows 10) o successiva. Come procedura consigliata, eseguire l'aggiornamento alla versione più recente di Windows 10.



 **Dispositivi Windows di livello inferiore**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


Come primo passaggio della pianificazione, è consigliabile esaminare l'ambiente e determinare se è necessario supportare dispositivi Windows di livello inferiore.



## <a name="review-things-you-should-know"></a>Esaminare le informazioni utili

Non è possibile usare un'aggiunta ad Azure AD ibrido se l'ambiente è costituito da una sola foresta che ha sincronizzato i dati sull'identità con più di un tenant di Azure AD.

Se si intende usare l'utilità preparazione sistema (Sysprep), assicurarsi che immagini create da un'installazione di Windows 10 1803 o versioni precedenti non sono stati configurati per l'aggiunta ad Azure AD ibrido.

Se ci si basa sullo snapshot di una macchina virtuale (VM) per creare altre VM, assicurarsi di usare lo snapshot di una VM che non è stato configurato per l'aggiunta di Azure AD ibrido.

L'aggiunta di dispositivi Windows di livello inferiore all'identità ibrida di Azure AD:

- **È** supportata in ambienti non federati tramite [Accesso Single Sign-On facile di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 

- **Non è** supportata quando si usa l'autenticazione pass-through di Azure AD senza accesso Single Sign-On facile.

- **Non è** supportata quando si usa il roaming delle credenziali o il roaming dei profili utente o quando si usa VDI (Virtual Desktop Infrastructure).


La registrazione di Windows Server che esegue il ruolo controller di dominio non è supportata.

Se l'organizzazione richiede l'accesso a Internet tramite un proxy in uscita autenticato, è necessario assicurarsi che i computer Windows 10 possano eseguire l'autenticazione nel proxy in uscita. Poiché i computer Windows 10 eseguono la registrazione dei dispositivi usando il contesto del computer, è necessario configurare l'autenticazione del proxy in uscita usando il contesto del computer.


L'aggiunta ad Azure AD ibrido è un processo che consente di registrare automaticamente i dispositivi aggiunti a un dominio locale con Azure AD. In alcuni casi non si vuole che tutti i dispositivi vengano registrati automaticamente. In questo caso, vedere [Come controllare l'aggiunta dei dispositivi all'identità ibrida di Azure AD](hybrid-azuread-join-control.md).

Se i dispositivi aggiunti a un dominio di Windows 10 sono già [registrati in Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) nel tenant, è consigliabile rimuovere questo stato prima di abilitare l'aggiunta ad Azure AD ibrido. Dalla versione 1809 di Windows 10 sono state introdotte le modifiche seguenti per evitare questo stato doppio: 
 - L'eventuale stato esistente di registrato in Azure AD viene rimosso automaticamente dopo l'aggiunta del dispositivo ad Azure AD ibrido. 
 - È possibile impedire che il dispositivo aggiunto al dominio in Azure AD registrata mediante l'aggiunta di questa chiave del Registro di sistema - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" runasppl"=DWORD:00000001.
 - Questa modifica è ora disponibile per il rilascio di Windows 10 1803 con KB4489894.

I moduli TPM compatibili con FIPS non sono supportati per l'aggiunta ad Azure AD ibrido. Se i dispositivi dispongono di moduli TPM compatibili con FIPS, è necessario disabilitare prima di procedere con l'aggiunta ad Azure AD ibrido. Microsoft non fornisce gli strumenti per la disabilitazione della modalità FIPS per TPM come dipende dal produttore del TPM. Per supporto, contattare l'OEM di hardware.

## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Esaminare come controllare l'aggiunta dei dispositivi all'identità ibrida di Azure AD

L'aggiunta ad Azure AD ibrido è un processo che consente di registrare automaticamente i dispositivi aggiunti a un dominio locale con Azure AD. In alcuni casi non si vuole che tutti i dispositivi vengano registrati automaticamente. Questo avviene ad esempio durante l'implementazione iniziale, per verificare che tutto funzioni come previsto.

Per altre informazioni, vedere [Come controllare l'aggiunta dei dispositivi all'identità ibrida di Azure AD](hybrid-azuread-join-control.md).

## <a name="select-your-scenario"></a>Selezionare lo scenario

È possibile configurare l'aggiunta ad Azure AD ibrido per gli scenari seguenti:

- Domini gestiti
- Domini federati  



Se l'ambiente include domini gestiti, l'aggiunta ad Azure AD ibrido supporta:

- Autenticazione pass-through

- Sincronizzazione dell'hash delle password

A partire dalla versione 1.1.819.0, in Azure AD Connect è presente una procedura guidata per configurare l'aggiunta ad Azure AD ibrido che semplifica in modo significativo il processo di configurazione. Per altre informazioni, vedere:

- [Configurare l'aggiunta all'identità ibrida di Azure Active Directory per i domini federati](hybrid-azuread-join-federated-domains.md)


- [Configurare l'aggiunta all'identità ibrida di Azure Active Directory per i domini gestiti](hybrid-azuread-join-managed-domains.md)


 Se non si può prendere in considerazione l'installazione della versione richiesta di Azure AD Connect, vedere [come configurare manualmente la registrazione dei dispositivi](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 


## <a name="on-premises-ad-upn-support-in-hybrid-azure-ad-join"></a>Supporto dei nomi dell'entità utente di AD locale nell'aggiunta ad Azure AD ibrido

I nomi dell'entità utente di AD locale a volte possono essere diversi da quelli di Azure AD. In questi casi l'aggiunta ad Azure AD ibrido di Windows 10 offre supporto limitato per i nomi dell'entità utente di AD locale in base al [metodo di autenticazione](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), al tipo di dominio e alla versione di Windows 10. Nell'ambiente possono essere presenti due tipi di nomi dell'entità utente di AD locale:

 - Nome dell'entità utente instradabile: ha un dominio verificato valido, registrato con un registrar. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.org è il dominio primario in AD locale di proprietà di Contoso e [verificato in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).
 
 - Nome dell'entità utente non instradabile: non ha un dominio verificato. È applicabile solo all'interno della rete privata dell'organizzazione. Ad esempio, se contoso.com è il dominio primario in Azure AD, contoso.local è il dominio primario in AD locale, ma non è un dominio verificabile in Internet e viene usato solo nella rete di Contoso.
 
La tabella seguente contiene informazioni sul supporto per questi nomi dell'entità utente di AD locale nell'aggiunta ad Azure AD ibrido di Windows 10

|Tipo di nome dell'entità utente di AD locale|Tipo di dominio|Versione di Windows 10|DESCRIZIONE|
|-----|-----|-----|-----|
|Instradabile|Federato |Dalla versione 1703|Disponibile a livello generale|
|Instradabile|Gestito|Dalla versione 1709|Attualmente in anteprima privata. La reimpostazione della password self-service di Azure AD non è supportata |
|Non instradabile|Federato|Dalla versione 1803|Disponibile a livello generale|
|Non instradabile|Gestito|Non supportate||



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare l'aggiunta all'identità ibrida di Azure Active Directory per i domini federati](hybrid-azuread-join-federated-domains.md)
> [Configurare l'aggiunta all'identità ibrida di Azure Active Directory per i domini gestiti](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
