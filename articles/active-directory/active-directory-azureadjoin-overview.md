---
title: "Estensione delle funzionalità del cloud ai dispositivi Windows 10 con Aggiunta ad Azure Active Directory | Documentazione Microsoft"
description: Panoramica dettagliata dell&quot;uso di Aggiunta ad Azure AD da parte di dispositivi Windows 10 per la registrazione in Azure Active Directory.
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 0cd4942f-7d54-474e-bd12-8e6764b0d42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: d3a3d3efe1c43caff3b8d2956c14e8c90d05d22b
ms.contentlocale: it-it
ms.lasthandoff: 06/05/2017


---
# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory
## <a name="what-is-azure-active-directory-join"></a>Informazioni su Aggiunta ad Azure Active Directory
Aggiunta ad Azure Active Directory (Aggiunta ad Azure AD) è la funzionalità che permette di registrare i dispositivi di proprietà di una società in Azure Active Directory per poterli gestire in modo centralizzato. Permette agli utenti come studenti o dipendenti di connettersi al cloud dell'organizzazione tramite Azure Active Directory. In questo modo è possibile ottenere distribuzioni di Windows semplificate e accesso alle app e risorse aziendali da qualsiasi dispositivo Windows, di proprietà della società o degli utenti (BYOD).

Aggiunta ad Azure AD è destinato alle aziende basate prima di tutto o esclusivamente sul cloud, in genere piccole e medie imprese che non hanno un'infrastruttura Windows Server Active Directory locale. Aggiunta ad Azure AD può essere usato anche dalle aziende di grandi dimensioni sui dispositivi che non sono in grado di eseguire un'aggiunta a un dominio tradizionale, ad esempio i dispositivi mobili, o per gli utenti che necessitano principalmente di accedere a Office 365 o ad altre app SaaS integrate con Azure AD.

Nonostante l'aggiunta al dominio tradizionale offra ancora un'esperienza migliore a livello locale nei dispositivi compatibili, Aggiunta ad Azure AD è adatto ai dispositivi che non supportano l'aggiunta a un dominio. Aggiunta ad Azure AD è adatto anche per la gestione degli utenti nel cloud, usando funzionalità di gestione di dispositivi mobili anziché attraverso strumenti di gestione del dominio tradizionali, come Criteri di gruppo e System Center Configuration Manager (SCCM).

![Panoramica dei dispositivi aziendali e dei dispositivi personali con Azure AD e Active Directory in locale](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)

## <a name="why-should-enterprises-adopt-azure-ad-join"></a>Vantaggi dell'adozione di Aggiunta ad Azure AD
* **Aziende che operano prevalentemente nel cloud**: se è stato eseguito o pianificato il passaggio a un modello che prevede la riduzione della superficie in locale per una maggiore operatività nel cloud, Aggiunta ad Azure AD può risultare molto utile. Gli account Azure AD creati manualmente o con la sincronizzazione di Active Directory in locale possono essere usati per accedere a Windows 10. Gli utenti possono aggiungere i propri computer ad Azure AD usando la Configurazione guidata o il menu Impostazioni. Dopo l'aggiunta gli utenti potranno usufruire dell'accesso Single Sign-On (SSO) a risorse cloud come Office 365, nel browser o nelle applicazioni Office.
* **Istituti di istruzione**: gli istituti di istruzione hanno due tipi di utenti, i docenti e gli studenti. I docenti sono considerati membri dell'organizzazione più a lungo termine, per i quali è consigliabile creare account locali. Gli studenti sono invece membri dell'organizzazione a breve termine e possono quindi essere gestiti in Azure AD. Ciò significa che è possibile eseguire il push di tutta la directory nel cloud anziché archiviarle in locale. Significa anche che gli studenti possono accedere a Windows con i propri account Azure AD e usufruire delle risorse di Office 365, nel browser o nelle applicazioni Office.
* **Attività commerciali**: alcuni clienti hanno bisogno di soluzioni che permettano di gestire più facilmente i lavoratori stagionali.  Anche in questo caso, è possibile creare account locali per i dipendenti a tempo pieno con contratti a lungo termine in computer aggiunti a un dominio. I lavoratori stagionali sono invece membri dell'organizzazione più a breve termine ed è quindi preferibile gestirli dove è possibile spostare le licenze utente con maggiore facilità. La creazione degli account utente nel cloud con licenze di Office 365 permette agli utenti di sfruttare i vantaggi dell'accesso a Windows e alle applicazioni Office con un account Azure AD e offre ai datori di lavoro una maggiore flessibilità con le relative licenze al termine della collaborazione.
* **Altre aziende**: l'aggiunta degli utenti ad Azure AD può rappresentare un vantaggio anche se si gestiscono gli utenti nell'istanza di Active Directory locale. Azure AD, infatti, semplifica notevolmente l'esperienza di aggiunta, la gestione efficiente dei dispositivi e la registrazione per la gestione di dispositivi mobili automatica, oltre a offrire funzionalità Single Sign-On per le risorse locali e di Azure AD.  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>Funzionalità offerte da Aggiunta di Azure AD
Aggiunta di Azure AD offre le funzionalità seguenti:

* **Provisioning self-service dei dispositivi di proprietà della società**: con Windows 10, gli utenti possono configurare un dispositivo completamente nuovo con wrapping di riduzione nella Configurazione guidata, senza rivolgersi al reparto IT.
* **Supporto per i fattori di forma moderni**: Aggiunta ad Azure AD funziona anche sui dispositivi che non hanno funzionalità di aggiunta a un dominio tradizionali.  
* **Supporto degli account aziendali esistenti**: gli utenti non devono più creare e gestire un account Microsoft personale per avere un'esperienza ottimale sui dispositivi dell'azienda, come accade con Windows 8. Possono usare l'account aziendale esistente in Azure AD. Per molte organizzazioni, questo significa che gli utenti possono configurare e accedere a Windows con le stesse credenziali usate per accedere a Office 365.
* **Registrazione per la gestione di dispositivi mobili automatica**: quando sono connessi ad Azure AD i dispositivi vengono registrati automaticamente nella gestione di dispositivi mobili. Il processo funziona con Microsoft Intune e soluzioni partner di gestione di dispositivi mobili. Se la gestione dei dispositivi viene eseguita con Intune, gli amministratori IT possono monitorare e gestire i dispositivi aggiunti ad Azure AD insieme ai dispositivi aggiunti a un dominio nella console di gestione di SCCM.
* **Accesso Single Sign-On alle risorse aziendali**: gli utenti possono usufruire dell'accesso Single Sign-On dal desktop di Windows alle risorse e alle app nel cloud, ad esempio Office 365 e migliaia di applicazioni aziendali che si basano su Azure AD per l'autenticazione tramite [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Anche i dispositivi di proprietà della società aggiunti ad Azure AD possono usare la funzionalità Single Sign-On per accedere alle risorse locali quando il dispositivo si trova nella rete aziendale e per accedere da qualsiasi luogo quando tali risorse sono esposte tramite il [proxy dell'applicazione di Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).
* **Roaming dello stato del sistema operativo**: impostazioni di accessibilità, siti Web, password Wi-Fi e altre impostazioni vengono sincronizzati in tutti i dispositivi di proprietà della società senza la necessità di un account Microsoft personale.
* **Windows Store per le aziende**: Windows Store supporta l'acquisizione e la gestione delle licenze delle app con account Azure AD. Le organizzazioni possono acquisire contratti multilicenza per le app e metterle a disposizione degli utenti dell'organizzazione.

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>Differenze di funzionamento dei dispositivi con Aggiunta ad Azure AD
| Dispositivo aziendale (aggiunto a un dominio locale) | Dispositivo aziendale (aggiunto al cloud) | Dispositivo personale |
| --- | --- | --- |
| Gli utenti possono accedere a Windows con le credenziali dell'account aziendale, come già accade attualmente. |Gli utenti possono accedere a Windows con le credenziali dell'account aziendale gestite in Azure AD. Questo riguarda i dispositivi aziendali in tre scenari: <ol><li>L'organizzazione non ha Active Directory in locale, ad esempio nel caso di un'azienda di piccole dimensioni.</li><li>L'organizzazione non crea tutti gli account utente in Active Directory, ad esempio nel caso di account per studenti, consulenti o lavoratori stagionali che non vengono creati in Active Directory.</li><li>L'organizzazione usa dispositivi aziendali che non possono essere aggiunti a un dominio locale, come telefoni o tablet che eseguono uno SKU per dispositivi mobili, ad esempio nel caso di un dispositivo secondario usato in un reparto di produzione o di vendita.</li></ol> Aggiunta ad Azure AD supporta l'aggiunta di dispositivi aziendali per le organizzazioni federate e quelle gestite. |Gli utenti accedono a Windows con le credenziali dell'account Microsoft, come già accade attualmente. |
| Gli utenti hanno accesso alle impostazioni di roaming e alla sezione aziendale di Windows Store. Questi servizi funzionano con gli account aziendali e non richiedono un account Microsoft personale. L'organizzazione deve connettere l'istanza di Active Directory locale ad Azure AD. |Gli utenti possono eseguire la configurazione self-service. Possono eseguire il completamente dell'installazione tramite l'account aziendale, in alternativa al provisioning dei dispositivi da parte del reparto IT. Entrambi i metodi sono supportati. |Gli utenti possono aggiungere facilmente un account aziendale gestito in Active Directory o Azure AD. |
| Gli utenti possono usare la funzionalità SSO per accedere alle app aziendali, ai siti Web e alle risorse, incluse le risorse locali e le app per cloud che usano Azure AD per l'autenticazione. |I dispositivi vengono registrati automaticamente nella directory dell'organizzazione, in Azure AD, e registrati automaticamente nella gestione di dispositivi mobili. Questa è una funzionalità di Azure AD Premium. |Gli utenti possono usare la funzionalità SSO per accedere a tutte le app, ai siti Web e alle risorse con l'account aziendale. |
| Gli utenti possono aggiungere l'account Microsoft personale per accedere a file e immagini personali senza conseguenze per i dati dell'organizzazione. Le impostazioni di roaming continuano a funzionare con gli account aziendali. L'account Microsoft abilita l'accesso SSO e non determina più il roaming delle impostazioni. |Gli utenti possono eseguire una reimpostazione password self-service su winlogon, vale a dire che possono reimpostare una password dimenticata. Questa è una funzionalità di Azure AD Premium. |Gli utenti hanno accesso alla sezione aziendale di Windows Store per acquistare e usare app line-of-business dai dispositivi personali. |

## <a name="additional-information"></a>Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)


