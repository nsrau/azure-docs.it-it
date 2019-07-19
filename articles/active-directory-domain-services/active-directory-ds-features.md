---
title: 'Azure Active Directory Domain Services: Funzionalità | Microsoft Docs'
description: Funzionalità di Servizi di dominio Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: e0f38ed8367f076c9f9c4da77440bb3e9603d514
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234201"
---
# <a name="features-provided-by-azure-active-directory-domain-services"></a>Funzionalità fornite da Azure Active Directory Domain Services

Nei domini gestiti di Servizi di dominio Azure AD sono disponibili le funzionalità seguenti.

* **Esperienza di distribuzione semplice:** bastano pochi clic per abilitare Azure AD Domain Services per la directory di Azure AD. Il dominio gestito include account utente solo cloud e account utente sincronizzati da una directory locale.
* **Supporto per l'aggiunta al dominio:** è possibile aggiungere facilmente al dominio i computer nella rete virtuale di Azure in cui è disponibile il dominio gestito. L'esperienza di aggiunta al dominio nei sistemi operativi client e server Windows funziona perfettamente con i domini gestiti da Servizi di dominio Azure AD. È inoltre possibile usare gli strumenti automatizzati per l'aggiunta al dominio per questi domini.
* **Una sola istanza di dominio per ogni directory di Azure AD:** è possibile creare un singolo dominio di Active Directory per ogni directory di Azure AD.
* **Creare domini con nomi personalizzati:** è possibile creare domini con nomi personalizzati, ad esempio "contoso100.com", tramite Azure AD Domain Services. È possibile usare i nomi di dominio sia verificati che non verificati. Facoltativamente è possibile anche creare un dominio con il suffisso di dominio predefinito (ovvero "*.onmicrosoft.co") offerto dalla directory di Azure AD.
* **Integrazione con Azure AD:** non è necessario configurare o gestire la replica con Azure AD Domain Services. Gli account utente, le appartenenze ai gruppi e le credenziali utente (password) dalla directory di Azure AD sono automaticamente disponibili in Servizi di dominio Azure AD. I nuovi utenti, i gruppi o le modifiche per gli attributi provenienti dal tenant di Azure AD o dalla directory locale vengono sincronizzati automaticamente in Servizi di dominio Azure AD.
* **Autenticazione NTLM e Kerberos:** con il supporto per l'autenticazione NTLM e Kerberos, è possibile distribuire applicazioni basate sull'autenticazione integrata di Windows.
* **Usare le credenziali e/o le password aziendali:** le password degli utenti nel tenant di Azure AD sono valide per Azure AD Domain Services. Gli utenti possono usare le credenziali aziendali per aggiungere computer a un dominio, accedere in modo interattivo o tramite Desktop remoto ed eseguire l'autenticazione nel dominio gestito.
* **Supporto di binding LDAP e operazioni di lettura LDAP:** è possibile usare applicazioni che si basano sui binding LDAP per l'autenticazione degli utenti in domini gestiti da Azure AD Domain Services. Inoltre, anche le applicazioni che usano operazioni di lettura LDAP per eseguire query sugli attributi utente/computer dalla directory possono interagire con Servizi di dominio Azure AD.
* **LDAP sicuro (LDAPS):** è possibile abilitare l'accesso alla directory tramite LDAP sicuro (LDAPS). L'accesso LDAP sicuro è disponibile all'interno della rete virtuale per impostazione predefinita. È tuttavia possibile abilitare l'accesso LDAP sicuro tramite Internet.
* **Criteri di gruppo:** è possibile usare un singolo oggetto Criteri di gruppo predefinito per i contenitori degli utenti e dei computer, in modo da garantire la conformità ai criteri di sicurezza richiesti per gli account utente e per i computer aggiunti al dominio. È anche possibile creare GPO personalizzati e assegnarli all'unità organizzativa personalizzata per [gestire criteri di gruppo](manage-group-policy.md).
* **Gestione DNS:** i membri del gruppo "AAD DC Administrators" possono gestire il sistema DNS per il dominio gestito tramite strumenti di amministrazione DNS noti, come lo snap-in di MMC per amministrazione DNS.
* **Creare unità organizzative (OU) personalizzate:** i membri del gruppo "AAD DC Administrators" possono creare unità organizzative personalizzate nel dominio gestito. A questi utenti vengono concessi privilegi amministrativi completi sulle unità organizzative personalizzate e quindi possono aggiungere e rimuovere account di servizio, computer, gruppi e così via all'interno di queste unità organizzative personalizzate.
* **Disponibile in molte aree globali di Azure:** Per informazioni sulle aree di Azure in cui è disponibile Azure AD Domain Services, vedere la pagina relativa ai [servizi di Azure per area](https://azure.microsoft.com/regions/#services/) .
* **Disponibilità elevata:** Azure AD Domain Services offre un'elevata disponibilità per il dominio. Questa funzionalità garantisce tempi di attività e resilienza agli errori migliori. Il monitoraggio dell'integrità incluso offre funzionalità automatizzate di correzione degli errori, attivando nuove istanze per sostituire quelle in errore e offrire la continuità del servizio per il dominio.
* **Protezione del blocco degli account AD:** gli account degli utenti vengono bloccati per 30 minuti se vengono inserite 5 password non valide nell'arco di 2 minuti. Gli account vengono sbloccati automaticamente dopo 30 minuti.
* **Usare strumenti di gestione familiari:** è possibile usare strumenti di gestione di Windows Server Active Directory familiari come Centro di amministrazione di Active Directory o PowerShell di Active Directory per amministrare i domini gestiti.
