---
title: 'Azure Active Directory Domain Services: funzionalità | Microsoft Docs'
description: Funzionalità di Servizi di dominio Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: a64401792fd034fde98fe1330340b1ffaa7dfcc1
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505413"
---
# <a name="azure-ad-domain-services"></a>Servizi di dominio Azure Active Directory
## <a name="features"></a>Funzionalità
Nei domini gestiti di Servizi di dominio Azure AD sono disponibili le funzionalità seguenti.

* **Esperienza di distribuzione semplice:** bastano pochi clic per abilitare Azure AD Domain Services per la directory di Azure AD. Il dominio gestito include account utente solo cloud e account utente sincronizzati da una directory locale.
* **Supporto dell'aggiunta al dominio**: è possibile aggiungere facilmente al dominio i computer nella rete virtuale di Azure in cui è disponibile il dominio gestito. L'esperienza di aggiunta al dominio nei sistemi operativi client e server Windows funziona perfettamente con i domini gestiti da Servizi di dominio Azure AD. È inoltre possibile usare gli strumenti automatizzati per l'aggiunta al dominio per questi domini.
* **Una sola istanza di dominio per ogni directory di Azure AD:** è possibile creare un singolo dominio di Active Directory per ogni directory di Azure AD.
* **Creare domini con nomi personalizzati**: è possibile creare domini con nomi personalizzati, ad esempio "contoso100.com", tramite Servizi di dominio Azure AD. È possibile usare i nomi di dominio sia verificati che non verificati. Facoltativamente è possibile anche creare un dominio con il suffisso di dominio predefinito (ovvero "*.onmicrosoft.co") offerto dalla directory di Azure AD.
* **Integrazione con Azure AD:** non è necessario configurare o gestire la replica con Servizi di dominio Azure AD. Gli account utente, le appartenenze ai gruppi e le credenziali utente (password) dalla directory di Azure AD sono automaticamente disponibili in Servizi di dominio Azure AD. I nuovi utenti, i gruppi o le modifiche per gli attributi provenienti dal tenant di Azure AD o dalla directory locale vengono sincronizzati automaticamente in Servizi di dominio Azure AD.
* **Autenticazione NTLM e Kerberos:** con il supporto per l'autenticazione NTLM e Kerberos, è possibile distribuire applicazioni basate sull'autenticazione integrata di Windows.
* **Usare le credenziali e/o le password aziendali:** le password degli utenti nel tenant di Azure AD sono valide per Servizi di dominio Azure AD. Gli utenti possono usare le credenziali aziendali per aggiungere computer al dominio, effettuare l'accesso in modo interattivo o tramite desktop remoto e autenticarsi nel dominio gestito.
* **Supporto di binding LDAP e operazioni di lettura LDAP**: è possibile usare applicazioni che si basano sui binding LDAP per l'autenticazione degli utenti in domini gestiti da Servizi di dominio Azure AD. Inoltre, anche le applicazioni che usano operazioni di lettura LDAP per eseguire query sugli attributi utente/computer dalla directory possono interagire con Servizi di dominio Azure AD.
* **LDAP sicuro (LDAPS):** è possibile abilitare l'accesso alla directory tramite LDAP sicuro (LDAPS). L'accesso LDAP sicuro è disponibile all'interno della rete virtuale per impostazione predefinita. È tuttavia possibile abilitare l'accesso LDAP sicuro tramite Internet.
* **Criteri di gruppo:** è possibile usare un singolo oggetto Criteri di gruppo predefinito per i contenitori degli utenti e dei computer, in modo da garantire la conformità ai criteri di sicurezza richiesti per gli account utente e per i computer aggiunti al dominio. È anche possibile creare GPO personalizzati e assegnarli all'unità organizzativa personalizzata per [gestire criteri di gruppo](active-directory-ds-admin-guide-administer-group-policy.md).
* **Gestione DNS**: i membri del gruppo "AAD DC Administrators" possono gestire il sistema DNS per il dominio gestito tramite strumenti di amministrazione DNS noti, come lo snap-in MMC per amministrazione DNS.
* **Creare unità organizzative (OU) personalizzate**: i membri del gruppo "AAD DC Administrators" possono creare unità organizzative personalizzate nel dominio gestito. A questi utenti vengono concessi privilegi amministrativi completi sulle unità organizzative personalizzate e quindi possono aggiungere e rimuovere account di servizio, computer, gruppi e così via all'interno di queste unità organizzative personalizzate.
* **Disponibile in molte aree globali di Azure:** per informazioni sulle aree di Azure in cui è disponibile Azure AD Domain Services, vedere la pagina [Servizi in base all'area](https://azure.microsoft.com/regions/#services/).
* **Disponibilità elevata:** Servizi di dominio Azure AD offre un'elevata disponibilità per il dominio. Questa funzionalità garantisce tempi di attività e resilienza agli errori migliori. Il monitoraggio dell'integrità incluso offre funzionalità automatizzate di correzione degli errori, attivando nuove istanze per sostituire quelle in errore e offrire la continuità del servizio per il dominio.
* **Blocco degli account Active Directory come metodo di protezione:** gli account degli utenti vengono bloccati per 30 minuti se vengono inserite 5 password non valide in 2 minuti. Gli account vengono sbloccati automaticamente dopo 30 minuti.
* **Usare strumenti di gestione familiari**: è possibile usare strumenti di gestione di Windows Server Active Directory familiari come Centro di amministrazione di Active Directory o Active Directory PowerShell per amministrare i domini gestiti.
