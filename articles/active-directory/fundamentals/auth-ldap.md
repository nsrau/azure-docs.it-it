---
title: Autenticazione LDAP con Azure Active Directory
description: Indicazioni sull'architettura per ottenere questo modello di autenticazione
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a70cb4754d98f4573670860c510692a7a2d134c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114424"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>Autenticazione LDAP con Azure Active Directory

LDAP (Lightweight Directory Access Protocol) è un protocollo dell'applicazione per l'utilizzo di diversi servizi directory. I servizi directory, ad esempio Active Directory, [archiviano le informazioni relative a utenti e account](https://www.dnsstuff.com/active-directory-service-accounts)e informazioni di sicurezza come le password. Il servizio consente quindi di condividere le informazioni con altri dispositivi della rete. Le applicazioni aziendali come la posta elettronica, i responsabili delle relazioni con i clienti (dispongono) e il software per le risorse umane possono usare LDAP per l'autenticazione, l'accesso e la ricerca di informazioni. 

Azure Active Directory (Azure AD) supporta questo modello tramite Azure AD Domain Services (AD DS). Consente alle organizzazioni che adottano una strategia cloud-First di modernizzare il proprio ambiente spostando le risorse LDAP locali nel cloud. I vantaggi immediati saranno: 

* Integrato con Azure AD. Le aggiunte di utenti e gruppi o le modifiche degli attributi ai relativi oggetti vengono sincronizzate automaticamente dal tenant Azure AD ai servizi di dominio Active Directory. Le modifiche apportate agli oggetti in Active Directory locali vengono sincronizzate con Azure AD e quindi in servizi di dominio Active Directory.

* Semplificare le operazioni. Riduce la necessità di eseguire manualmente e applicare patch alle infrastrutture locali. 

* Attendibile. Ottieni servizi gestiti a disponibilità elevata 

## <a name="use-when"></a>Casi di utilizzo

È necessario che un'applicazione o un servizio usi l'autenticazione LDAP.

![Diagramma dell'architettura](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>Componenti del sistema

* **Utente**: accede alle applicazioni dipendenti da LDAP tramite un browser.

* **Web browser**: l'interfaccia con cui l'utente interagisce per accedere all'URL esterno dell'applicazione.

* **Rete virtuale**: una rete privata in Azure attraverso la quale l'applicazione legacy può utilizzare i servizi LDAP. 

* **Applicazioni legacy**: applicazioni o carichi di lavoro del server che richiedono LDAP distribuito in una rete virtuale in Azure o che hanno visibilità per gli IP delle istanze di servizi di dominio Active Directory tramite route di rete. 

* **Azure ad**: Sincronizza le informazioni di identità dalla directory locale dell'organizzazione tramite Azure ad Connect.

* **Azure ad Domain Services (ad DS)**: esegue una sincronizzazione unidirezionale da Azure ad per consentire l'accesso a un insieme centrale di utenti, gruppi e credenziali. L'istanza di servizi di dominio Active Directory viene assegnata a una rete virtuale. Le applicazioni, i servizi e le VM in Azure che si connettono alla rete virtuale assegnata a servizi di dominio Active Directory possono usare funzionalità comuni di servizi di dominio Active Directory, ad esempio LDAP, aggiunta a un dominio, criteri di gruppo, Kerberos e autenticazione NTLM.
   > [!NOTE]
   >  Negli ambienti in cui l'organizzazione non è in grado di sincronizzare gli hash delle password o l'accesso degli utenti tramite smart card, è consigliabile utilizzare una foresta di risorse in servizi di dominio Active Directory. 

* **Azure ad Connect**: strumento per sincronizzare le informazioni sull'identità locale con Microsoft Azure ad. La distribuzione guidata e le esperienze guidate consentono di configurare i prerequisiti e i componenti necessari per la connessione, inclusa la sincronizzazione e l'accesso da Active Directory a Azure AD. 

* **Active Directory**: servizio directory in cui vengono archiviate [informazioni sull'identità locale, ad esempio informazioni su utenti e account](https://www.dnsstuff.com/active-directory-service-accounts), e informazioni di sicurezza, ad esempio le password.

## <a name="implement-ldap-authentication-with-azure-ad"></a>Implementare l'autenticazione LDAP con Azure AD

* [Creare e configurare un'istanza di Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance) 

* [Configurare la rete virtuale per un'istanza di Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-networking) 

* [Configurare LDAP sicuro per un dominio gestito Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps) 

* [Creare un trust tra foreste in uscita per un dominio locale in Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-forest-trust)

 
