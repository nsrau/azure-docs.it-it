---
title: Che cos'è il provisioning cloud di Azure AD Connect. | Microsoft Docs
description: Descrive il provisioning cloud di Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a5a69bedb45f8037f3a97f5ebf4e2128930330a
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728353"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Che cos'è il provisioning cloud di Azure AD Connect?
Il provisioning cloud di Azure AD Connect è un nuovo agente Microsoft progettato per soddisfare e raggiungere gli obiettivi di identità ibrida per la sincronizzazione di utenti, gruppi e contatti con Azure AD.  Può essere usato insieme al servizio di sincronizzazione di Azure AD Connect e offre i vantaggi seguenti:
    
- Supporto per la sincronizzazione con un tenant di Azure AD da un ambiente di Active Directory con più foreste disconnesse: gli scenari comuni includono fusioni e acquisizioni, in cui le foreste di AD dell'azienda acquisita sono isolate da quelle della casa madre, e le aziende che solitamente hanno più foreste di AD.
- Installazione semplificata con agenti di provisioning leggeri: gli agenti fungono da bridge tra AD e Azure AD, con tutta la configurazione di sincronizzazione gestita nel cloud. 
- Per semplificare le distribuzioni a disponibilità elevata, in particolare per le organizzazioni che si basano sulla sincronizzazione degli hash delle password tra AD e Azure AD, è possibile usare più agenti di provisioning.


![Cos'è Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Quali sono le differenze tra il provisioning cloud di Azure AD Connect e il servizio di sincronizzazione di Azure AD Connect?
Con il provisioning cloud di Azure AD Connect, il provisioning tra AD e Azure AD è orchestrato in Microsoft Online Services. Le organizzazioni devono solo distribuire, nel proprio ambiente locale e in quello ospitato da IaaS, un agente leggero che funge da bridge tra Azure AD e AD. La configurazione del provisioning viene archiviata in Azure AD e gestita come parte del servizio.

## <a name="azure-ad-connect-cloud-provisioning-video"></a>Video sul provisioning cloud di Azure AD Connect
Il breve video seguente offre una panoramica eccellente del provisioning cloud di Azure AD Connect:

> [!VIDEO https://youtube/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-provisioning"></a>Confronto tra Azure AD Connect e il provisioning cloud

La tabella seguente fornisce un confronto tra Azure AD Connect e il provisioning cloud di Azure AD Connect:

| Funzionalità | Servizio di sincronizzazione di Azure Active Directory Connect| Provisioning cloud di Azure Active Directory Connect |
|:--- |:---:|:---:|
|Connessione a singola foresta AD locale|● |● |
| Connessione a più foreste AD locali |● |● |
| Connessione a più foreste di AD locali disconnesse | |● |
| Modello con installazione di agente leggero | |● |
| Più agenti attivi per la disponibilità elevata | |● |
| Connessione a directory LDAP|●| | 
| Supporto per oggetti utente |● |● |
| Supporto per oggetti gruppo |● |● |
| Supporto per oggetti contatto |● |● |
| Supporto per oggetti dispositivo |● | |
| Personalizzazione di base per i flussi di attributi |● |● |
| Sincronizzazione degli attributi online di Exchange |● |● |
| Sincronizzazione degli attributi di estensioni 1-15 |● |● |
| Sincronizzazione di attributi AD definiti dall'utente (estensioni della directory) |● | |
| Supporto per la sincronizzazione degli hash delle password |●|●|
| Supporto per l'autenticazione pass-through |●||
| Supporto per la federazione |●|●|
| Accesso Single Sign-On facile (Seamless SSO)|● |●|
| Supporta l'installazione in un controller di dominio |● |● |
| Supporto per Windows Server 2012 e Windows Server 2012 R2 |● |● |
| Applicazioni di filtri in base a domini/unità organizzative/gruppi |● |● |
| Applicazione di filtri in base a valori di attributo degli oggetti |● | |
| Possibilità di sincronizzare un set minimo di attributi (MinSync) |● |● |
| Possibilità di interrompere il flusso di attributi da AD ad Azure AD |● |● |
| Personalizzazione avanzata dei flussi di attributi |● | |
| Supporto per il writeback (password, dispositivi, gruppi) |● | |
| Supporto per Azure AD Domain Services|● | |
| [Writeback della distribuzione ibrida Exchange](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Supporto per più di 50.000 oggetti per dominio di AD |● | |

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Installare il provisioning cloud](how-to-install.md)
