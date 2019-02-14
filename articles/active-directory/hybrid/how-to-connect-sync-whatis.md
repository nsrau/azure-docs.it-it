---
title: 'Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione | Microsoft Docs'
description: Spiega come funziona il servizio di sincronizzazione Azure AD Connect e come personalizzarlo.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3b87f40d75d4045155e7dd953dc76ffd9de2b34
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182341"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione
Il servizio di sincronizzazione Azure Active Directory Connect (servizio di sincronizzazione Azure AD Connect) è un componente importante di Azure AD Connect. Si occupa di tutte le operazioni che riguardano la sincronizzazione dei dati relativi alle identità tra l'ambiente locale e Azure AD. Il servizio di sincronizzazione Azure AD Connect è il successore di DirSync, Azure AD Sync e Forefront Identity Manager con il connettore Azure Active Directory configurato.

Questo argomento fornisce informazioni di base sul **servizio di sincronizzazione Azure AD Connect**, detto anche **motore di sincronizzazione**, e include un elenco di collegamenti a tutti gli altri argomenti correlati. Per collegamenti relativi ad Azure AD Connect, vedere [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).

Il servizio di sincronizzazione è costituito da due componenti: la **sincronizzazione di Azure AD Connect** locale e il servizio di Azure AD chiamato **servizio di sincronizzazione di Azure AD Connect**.

## <a name="azure-ad-connect-sync-topics"></a>Argomenti relativi al servizio di sincronizzazione Azure AD Connect
| Argomento | Contenuti e destinatari |
| --- | --- |
| **Concetti fondamentali sul servizio di sincronizzazione Azure AD Connect** | |
| [Informazioni sull'architettura](concept-azure-ad-connect-sync-architecture.md) |Per chi non ha familiarità con il motore di sincronizzazione e vuole avere informazioni sull'architettura e sulla terminologia usata. |
| [Concetti tecnici](how-to-connect-sync-technical-concepts.md) |Versione breve dell'argomento relativo all'architettura, con una rapida spiegazione della terminologia usata. |
| [Topologie per Azure AD Connect](plan-connect-topologies.md) |Illustra le diverse topologie e i diversi scenari supportati dal motore di sincronizzazione. |
| **Configurazione personalizzata** | |
| [Nuova esecuzione dell'installazione guidata](how-to-connect-installation-wizard.md) |Spiega quali opzioni sono disponibili se si esegue di nuovo l'installazione guidata di Azure AD Connect. |
| [Informazioni sul provisioning dichiarativo](concept-azure-ad-connect-sync-declarative-provisioning.md) |Descrive il modello di configurazione denominato provisioning dichiarativo. |
| [Informazioni sulle espressioni di provisioning dichiarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Descrive la sintassi del linguaggio delle espressioni usato nel provisioning dichiarativo. |
| [Informazioni sulla configurazione predefinita](concept-azure-ad-connect-sync-default-configuration.md) |Illustra le regole predefinite e la configurazione predefinita. Illustra anche il modo in cui le regole interagiscono per consentire il funzionamento degli scenari predefiniti. |
| [Informazioni su utenti e contatti](concept-azure-ad-connect-sync-user-and-contacts.md) |Continua l'argomento precedente e illustra l'interazione tra le configurazioni di utenti e contatti, in particolare in un ambiente con più foreste. |
| [Servizio di sincronizzazione Azure AD Connect: Come apportare modifiche alla configurazione predefinita](how-to-connect-sync-change-the-configuration.md) |Illustra in dettaglio come creare una configurazione comune per i flussi di attributi. |
| [Procedure consigliate per modificare la configurazione predefinita](how-to-connect-sync-best-practices-changing-default-configuration.md) |Limitazioni del supporto e come apportare modifiche alla configurazione predefinita. |
| [Configurare il filtro](how-to-connect-sync-configure-filtering.md) |Illustra le diverse opzioni per limitare gli oggetti da sincronizzare in Azure AD e fornisce procedure dettagliate per la configurazione di queste opzioni. |
| **Funzionalità e scenari** | |
| [Impedire eliminazioni accidentali](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Illustra la funzionalità che *impedisce le eliminazioni accidentali* e spiega come configurarla. |
| [Utilità di pianificazione](how-to-connect-sync-feature-scheduler.md) |Illustra l'utilità di pianificazione predefinita, che importa, sincronizza ed esporta i dati. |
| [Implementare la sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) |Illustra il funzionamento della sincronizzazione password, spiega come implementarla, usarla e risolverne i problemi. |
| [Writeback dei dispositivi](how-to-connect-device-writeback.md) |Illustra il funzionamento del writeback dei dispositivi in Azure AD Connect. |
| [Estensioni della directory](how-to-connect-sync-feature-directory-extensions.md) |Illustra come estendere lo schema di Azure AD con i propri attributi personalizzati. |
| [PreferredDataLocation di Office 365](how-to-connect-sync-feature-preferreddatalocation.md) |Descrive come inserire le risorse di Office 365 dell'utente nella stessa area dell'utente. |
| **Servizio di sincronizzazione** | |
| [Funzionalità del servizio di sincronizzazione di Azure AD Connect](how-to-connect-syncservice-features.md) |Descrive il servizio di sincronizzazione e la modifica delle impostazioni di sincronizzazione in Azure AD. |
| [Resilienza degli attributi duplicati](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Descrive come abilitare e usare la resilienza dei valori di attributo duplicati **userPrincipalName** e **proxyAddresses**. |
| **Operazioni e interfaccia utente** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Descrive l'interfaccia utente di Synchronization Service Manager, comprese le schede [Operations](how-to-connect-sync-service-manager-ui-operations.md) (Operazioni), [Connectors](how-to-connect-sync-service-manager-ui-connectors.md) (Connettori), [Metaverse Designer](how-to-connect-sync-service-manager-ui-mvdesigner.md) (Progettazione metaverse) e [Metaverse Search](how-to-connect-sync-service-manager-ui-mvsearch.md) (Ricerca metaverse). |
| [Attività operative e considerazioni](how-to-connect-sync-operations.md) |Illustra aspetti operativi, ad esempio il ripristino di emergenza. |
| **Procedura:** | |
| [Reimpostare l'account Azure AD](how-to-connect-azureadaccount.md) |Come reimpostare le credenziali dell'account del servizio usato per la connessione dal servizio di sincronizzazione Azure AD Connect ad Azure AD. |
| **Altri riferimenti e informazioni** | |
| [Porte](reference-connect-ports.md) |Elenca le porte da aprire tra il motore di sincronizzazione e le directory locali e Azure AD. |
| [Attributi sincronizzati con Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Elenca tutti gli attributi che vengono sincronizzati tra l'istanza locale di AD e Azure AD. |
| [Riferimento alle funzioni](reference-connect-sync-functions-reference.md) |Elenca tutte le funzioni disponibili nel provisioning dichiarativo. |

## <a name="additional-resources"></a>Risorse aggiuntive
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
