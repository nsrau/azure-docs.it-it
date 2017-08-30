---
title: Introduzione alla gestione dei dispositivi in Azure Active Directory | Microsoft Docs
description: Informazioni su come la gestione dei dispositivi consente di ottenere il controllo sui dispositivi che accedono alle risorse nell'ambiente.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: c8b8408f76534a808fd60e331282f8191905df58
ms.contentlocale: it-it
ms.lasthandoff: 08/23/2017

---
# <a name="introduction-to-device-management-in-azure-active-directory"></a>Introduzione alla gestione dei dispositivi in Azure Active Directory

In un mondo in cui i dispositivi mobili e il cloud hanno sempre più importanza, Azure Active Directory (Azure AD) consente ovunque l'accesso Single Sign-On a dispositivi, app e servizi. Con il proliferare dei dispositivi, inclusi i dispositivi Bring Your Own Device (BYOD), i professionisti IT hanno due obiettivi opposti:

- Fare in modo che gli utenti finali siano produttivi sempre e ovunque
- Proteggere gli asset aziendali in qualsiasi momento

Tramite i dispositivi gli utenti hanno accesso alle risorse aziendali. Per proteggere le risorse aziendali, gli amministratori IT vogliono avere il controllo su questi dispositivi. Ciò consente di verificare che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard per sicurezza e conformità. 

Questo argomento illustra come la gestione dei dispositivi in Azure AD consente di raggiungere questo obiettivo.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Controllo dei dispositivi tramite Azure AD

Per controllare un dispositivo tramite Azure AD, sono disponibili due opzioni:

- Registrazione 
- Aggiunta

La **registrazione** di un dispositivo in Azure AD consente di gestire l'identità di un dispositivo. Quando un dispositivo viene registrato, Registrazione dispositivo Azure AD fornisce al dispositivo un'identità che viene usata per autenticare il dispositivo quando un utente accede ad Azure AD. È possibile usare l'identità per abilitare o disabilitare un dispositivo.

Con una soluzione di gestione di dispositivi mobili (MDM), ad esempio Microsoft Intune, gli attributi del dispositivo in Azure AD vengono aggiornati con informazioni aggiuntive sul dispositivo. Ciò permette di creare regole di accesso condizionale che subordinano l'accesso dai dispositivi al rispetto dei propri standard di sicurezza e conformità. Per altre informazioni sulla registrazione dei dispositivi in Microsoft Intune, vedere Registrare i dispositivi per la gestione in Intune.

L'**aggiunta** di un dispositivo è un'estensione della registrazione di un dispositivo. Offre infatti tutti i vantaggi della registrazione di un dispositivo, oltre a modificarne lo stato locale. Modificando lo stato locale, gli utenti possono accedere a un dispositivo usando un account aziendale o dell'istituto di istruzione invece di un account personale.

## <a name="azure-ad-registered-devices"></a>Dispositivi registrati in Azure AD   

L'obiettivo dei dispositivi registrati in Azure AD è di fornire supporto per lo scenario **Bring Your Own Device (BYOD)**. In questo scenario un utente può accedere alle risorse dell'organizzazione controllate da Azure Active Directory usando un dispositivo personale.  

![Dispositivi registrati in Azure AD](./media/device-management-introduction/03.png)

L'accesso si basa su un account aziendale o dell'istituto di istruzione immesso nel dispositivo.  
Windows 10, ad esempio, consente agli utenti di aggiungere un account aziendale o dell'istituto di istruzione a un computer, un tablet o un telefono personale.  
Dopo l'aggiunta dell'account aziendale o dell'istituto di istruzione il dispositivo viene registrato in Azure AD e, facoltativamente, anche nel sistema di gestione di dispositivi mobili (MDM) configurato dall'organizzazione. Gli utenti dell'organizzazione possono aggiungere un account aziendale o dell'istituto di istruzione a un dispositivo personale in modo pratico:

- Durante il primo accesso a un'applicazione di lavoro
- Manualmente tramite il menu **Impostazioni** nel caso di Windows 10 

È possibile configurare dispositivi registrati in Azure AD per Windows 10, iOS, Android e macOS.

## <a name="azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD

L'obiettivo dei dispositivi aggiunti ad Azure AD è di semplificare:

- Distribuzioni Windows di dispositivi di proprietà dell'azienda 
- Accesso ad app e risorse aziendali da qualsiasi dispositivo Windows

![Dispositivi registrati in Azure AD](./media/device-management-introduction/02.png)


Per raggiungere questi obiettivi, è possibile fornire agli utenti un'esperienza self-service per porre i dispositivi di proprietà dell'azienda sotto il controllo di Azure AD.  
L'**aggiunta ad Azure AD** è destinata alle organizzazioni basate prima di tutto o esclusivamente sul cloud. Si tratta in genere piccole e medie imprese che non hanno un'infrastruttura Active Directory di Windows Server locale. 

L'implementazione di dispositivi aggiunti ad Azure AD offre i vantaggi seguenti:

- **Single Sign-On (SSO)** alle app e ai servizi SaaS gestiti da Azure. Gli utenti non visualizzano richieste di autenticazione aggiuntive quando accedono alle risorse. La funzionalità SSO è attiva anche quando non sono connessi alla rete di dominio disponibile.

- **Roaming conforme ai criteri dell'organizzazione** per le impostazioni utente tra dispositivi aggiunti. Non è necessario che gli utenti connettano un account Microsoft (ad esempio, Hotmail) per visualizzare le impostazioni tra dispositivi.

- **Accesso a Windows Store per le aziende** tramite l'account AD. Gli utenti possono scegliere da un inventario di applicazioni preselezionate dall'organizzazione.

- Supporto di **Windows Hello** per un accesso sicuro e agevole alle risorse aziendali.

- **Limitazione dell'accesso** alle app solo dai dispositivi che soddisfano i criteri di conformità.

Anche se l'aggiunta ad Azure AD è destinata soprattutto alle organizzazioni prive di un'infrastruttura Active Directory di Windows Server locale, è ovviamente possibile usarla anche in scenari in cui:

- Non è possibile usare un'aggiunta a un dominio locale, ad esempio se è necessario avere il controllo di dispositivi mobili come tablet e telefoni.

- Gli utenti hanno soprattutto necessità di accedere a Office 365 o ad altre app SaaS integrate con Azure AD.

- Si vuole gestire un gruppo di utenti in Azure AD invece che in Active Directory, ad esempio lavoratori stagionali, terzisti o studenti.

- Si vogliono fornire funzionalità di join ai lavoratori in succursali remote con un'infrastruttura locale limitata.

È possibile configurare dispositivi aggiunti ad Azure AD per dispositivi Windows 10.


## <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti all'identità ibrida di Azure AD

Per oltre un decennio, molte organizzazioni hanno usato l'aggiunta a un dominio ad Active Directory locale per consentire:

- Ai reparti IT di gestire i dispositivi di proprietà dell'azienda da una posizione centrale.

- Agli utenti di accedere ai dispositivi con il proprio account aziendale o dell'istituto di istruzione di Active Directory. 

In genere le aziende con un footprint locale si basano su metodi di creazione dell'immagine per effettuare il provisioning dei dispositivi e, per gestirli, usano **System Center Configuration Manager (SCCM)** o **Criteri di gruppo**.

Se l'ambiente ha un footprint AD locale e si vogliono anche sfruttare le funzionalità offerte da Azure Active Directory, è possibile implementare dispositivi aggiunti all'identità ibrida di Azure AD. Questi dispositivi vengono aggiunti sia ad Active Directory locale che ad Azure Active Directory.

![Dispositivi registrati in Azure AD](./media/device-management-introduction/01.png)


È consigliabile usare dispositivi aggiunti all'identità ibrida di Azure AD se:

- Sono state distribuite app Win32 in questi dispositivi che usano NTLM/Kerberos.

- Sono necessari Criteri di gruppo o SCCM/DCM per gestire i dispositivi.

- Si vuole continuare a usare soluzioni di creazione dell'immagine per configurare i dispositivi per i dipendenti.

È possibile configurare dispositivi aggiunti all'identità ibrida di Azure AD per Windows 10 e dispositivi di livello inferiore, ad esempio Windows 8 e Windows 7.

## <a name="summary"></a>Riepilogo

Con la gestione dei dispositivi in Azure AD, è possibile: 

- Semplificare il processo per mettere i dispositivi sotto il controllo di Azure AD

- Fornire agli utenti un accesso facile da usare alle risorse basate sul cloud dell'organizzazione

Come regola generale, è consigliabile usare:

- Dispositivi registrati in Azure AD per i dispositivi personali

- Dispositivi aggiunti ad Azure AD per i dispositivi non aggiunti ad AD locale 

- Dispositivi aggiunti all'identità ibrida di Azure AD per i dispositivi aggiunti ad AD locale     




## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'accesso condizionale basato su dispositivo, vedere l'articolo su come [configurare criteri di accesso condizionale basati sul dispositivo in Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

- Per configurare dispositivi aggiunti all'identità ibrida di Azure AD, vedere [Come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md).



