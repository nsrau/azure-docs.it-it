---
title: Informazioni su Azure AD Connect provisioning cloud. | Microsoft Docs
description: Viene descritto Azure AD Connect provisioning cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333a3b93d872c1a99d1ec18d4941adc8986d4a03
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914561"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Che cos'è il provisioning cloud di Azure AD Connect?
Azure AD Connect provisioning cloud è un nuovo agente Microsoft progettato per soddisfare e raggiungere gli obiettivi di identità ibrida per la sincronizzazione di utenti, gruppi e contatti con Azure AD.  Può essere usato insieme a Azure AD Connect Sync e offre i vantaggi seguenti:
    
- Supporto per la sincronizzazione in un tenant di Azure AD da un ambiente con foreste Active Directory disconnesse a più foreste: gli scenari comuni includono l'acquisizione & di merge, in cui le foreste di Active Directory della società acquisita sono isolate dall'annuncio della società padre. foreste e società che hanno storicamente più foreste di Active Directory.
- Installazione semplificata con agenti di provisioning leggero: gli agenti agiscono da un Bridge da AD a Azure AD, con tutta la configurazione di sincronizzazione gestita nel cloud. 
- Per semplificare le distribuzioni a disponibilità elevata, in particolare per le organizzazioni che si basano sulla sincronizzazione degli hash delle password da AD a Azure AD, è possibile usare più agenti di provisioning.


![Cos'è Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>In che modo Azure AD Connect provisioning cloud è diverso dalla sincronizzazione Azure AD Connect?
Con Azure AD Connect provisioning nel cloud, il provisioning da AD a Azure AD è orchestrato in Microsoft Online Services. Un'organizzazione deve solo distribuire, nel proprio ambiente locale e ospitato da IaaS, un agente leggero che funge da Bridge tra Azure AD e AD. La configurazione del provisioning viene archiviata in Azure AD e gestita come parte del servizio.

La tabella seguente fornisce un confronto tra Azure AD Connect e Azure AD Connect il provisioning cloud:

| Funzionalità | Sincronizzazione Azure Active Directory Connect| Provisioning di Azure Active Directory Connect Cloud |
|:--- |:---:|:---:|
|Connessione a singola foresta AD locale|● |● |
| Connessione a più foreste AD locali |● |● |
| Connettersi a più foreste AD locali disconnesse | |● |
| Modello di installazione Lightweight Agent | |● |
| Più agenti attivi per la disponibilità elevata | |● |
| Connettersi alle directory LDAP|●| | 
| Supporto per gli oggetti utente |● |● |
| Supporto per oggetti gruppo |● |● |
| Supporto per oggetti contatto |● |● |
| Supporto per oggetti dispositivo |● | |
| Consenti personalizzazione di base per i flussi di attributi |● |● |
| Sincronizzare gli attributi di Active Directory definiti dal cliente (estensioni di directory) |● | |
| Supporto per la sincronizzazione degli hash delle password |●|●|
| Supporto per l'autenticazione pass-through |●||
| Supporto per la Federazione |●|●|
| Accesso Single Sign-On facile (Seamless SSO)|● |●|
| Supporta l'installazione in un controller di dominio |● |● |
| Supporto per Windows Server 2012 e Windows Server 2012 R2 |● |● |
| Filtrare in domini/unità organizzative/gruppi |● |● |
| Filtro in base ai valori di attributo degli oggetti |● | |
| Possibilità di sincronizzare un set minimo di attributi (MinSync) |● |● |
| Possibilità di interrompere il flusso di attributi da AD ad Azure AD |● |● |
| Personalizzazione avanzata dei flussi di attributi |● | |
| Supporto per il writeback (password, dispositivi, gruppi) |● | |
| Supporto Azure AD Domain Services|● | |

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è il provisioning?](what-is-provisioning.md)
- [Installare il provisioning cloud](how-to-install.md)
