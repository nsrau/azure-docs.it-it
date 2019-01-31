---
title: 'Soluzione ibrida di gestione delle identità: confronto degli strumenti di integrazione directory | Microsoft Docs'
description: Questa pagina contiene tabelle complete che confrontano i vari strumenti che possono essere usati per l'integrazione directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 26c00ba1dc6a4d597db8fe067d28d687f50412b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194491"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Confronto degli strumenti di integrazione directory per la soluzione ibrida di gestione delle identità
Nel corso degli anni gli strumenti di integrazione di directory sono aumentati e si sono evoluti.  Questo documento offre una panoramica consolidata di questi strumenti e un confronto delle funzionalità disponibili in ognuno.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect include i componenti e le funzionalità resi disponibili in precedenza come DirSync e AAD Sync. Questi strumenti non vengono più resi disponibili singolarmente e tutti i miglioramenti futuri verranno inclusi negli aggiornamenti per Azure AD Connect, in modo da poter individuare sempre da dove ottenere la funzionalità più aggiornata.
> 
> DirSync e Azure AD Sync sono funzionalità deprecate. Altre informazioni sono disponibili [qui](reference-connect-dirsync-deprecated.md).
> 
> 

Usare la seguente chiave per ognuna delle tabelle.

● = Disponibile  
VF = Versione futura  
AP = Anteprima pubblica  

## <a name="on-premises-to-cloud-synchronization"></a>Sincronizzazione da locale a cloud
| Funzionalità | Azure Active Directory Connect | Servizi di sincronizzazione di Azure Active Directory (AAD Sync) - NON PIÙ SUPPORTATI | Strumento di sincronizzazione di Azure Active Directory (DirSync) - NON PIÙ SUPPORTATO | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Connessione a singola foresta AD locale |● |● |● |● |● |
| Connessione a più foreste AD locali |● |● | |● |● |
| Connessione a più organizzazioni di Exchange |● | | | | |
| Connessione a singola directory LDAP locale |●* | | |● |● | 
| Connessione a più directory LDAP locali |●*  | | |● |● | 
| Connessione a più directory AD locali e LDAP locali |●* | | |● |● | 
| Connessione a sistemi personalizzati (ad esempio, SQL, Oracle, MySQL e così via) |VF | | |● |● |
| Sincronizzazione di attributi definiti dall'utente (estensioni della directory) |● | | | | |
| Connessione alle risorse umane locali (ad esempio, SAP, Oracle eBusiness, PeopleSoft) |VF | | |● |● |
| Supporto delle regole di sincronizzazione FIM e dei connettori per il provisioning ai sistemi locali. | | | |● |● |

 
&#42; Attualmente, sono supportate due opzioni.  Sono: 

   1. È possibile usare il connettore LDAP generico e abilitarlo all'esterno di Azure AD Connect.  Questa opzione è complessa e richiede un partner per l'onboarding e un contratto di supporto tecnico Premier per la gestione.  Questa opzione consente di gestire directory LDAP singole o multiple. 

   2. È possibile sviluppare soluzioni personalizzate per lo spostamento di oggetti da LDAP ad Active Directory.  Sincronizzare quindi gli oggetti con Azure AD Connect.  È possibile usare MIM o FIM come soluzioni per lo spostamento di oggetti. 

## <a name="cloud-to-on-premises-synchronization"></a>Sincronizzazione da cloud a locale
| Funzionalità | Azure Active Directory Connect | Servizi di sincronizzazione di Azure Active Directory - NON PIÙ SUPPORTATI  | Strumento di sincronizzazione di Azure Active Directory (DirSync) - NON PIÙ SUPPORTATO  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Writeback di dispositivi |● | |● | | |
| Writeback degli attributi (per una distribuzione ibrida di Exchange) |● |● |● |● |● |
| Writeback di oggetti gruppo |● | | | | |
| Writeback delle password (da password reimpostate autonomamente e modificate) |● |● | | | |

## <a name="authentication-feature-support"></a>Supporto delle funzionalità di autenticazione
| Funzionalità | Azure Active Directory Connect | Servizi di sincronizzazione di Azure Active Directory - NON PIÙ SUPPORTATI  | Strumento di sincronizzazione di Azure Active Directory (DirSync) - NON PIÙ SUPPORTATO  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Sincronizzazione hash password per una singola foresta AD locale |●|●|● | | |
| Sincronizzazione hash password per più foreste AD locali |●|● | | | |
| Autenticazione pass-through per singole foreste AD locali |●| | | | |
| Single Sign-On con federazione |● |● |● |● |● |
| Accesso Single Sign-On facile (Seamless SSO)|● |||||
| Writeback delle password (da password reimpostate autonomamente e modificate) |● |● | | | |

## <a name="set-up-and-installation"></a>Configurazione e installazione
| Funzionalità | Azure Active Directory Connect | Servizi di sincronizzazione di Azure Active Directory - NON PIÙ SUPPORTATI  | Strumento di sincronizzazione di Azure Active Directory (DirSync) - NON PIÙ SUPPORTATO  | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Supporta l'installazione in un controller di dominio |● |● |● | |
| Supporta l'installazione con SQL Express |● |● |● | |
| Aggiornamento facile da DirSync |● | | | |
| Localizzazione dell'esperienza utente per l'amministratore nelle lingue di Windows Server |● |● |● | |
| Localizzazione dell'esperienza utente per l'utente finale nelle lingue di Windows Server | | | |● |
| Supporto per Windows Server 2008 e Windows Server 2008 R2 |● per la sincronizzazione, non per la federazione |● |● |● |
| Supporto per Windows Server 2012 e Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filtro e configurazione
| Funzionalità | Azure Active Directory Connect | Servizi di sincronizzazione di Azure Active Directory - NON PIÙ SUPPORTATI  | Strumento di sincronizzazione di Azure Active Directory (DirSync) - NON PIÙ SUPPORTATO  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filtro di domini e unità organizzative |● |● |● |● |● |
| Filtro in base ai valori di attributo degli oggetti |● |● |● |● |● |
| Possibilità di sincronizzare un set minimo di attributi (MinSync) |● |● | | | |
| Possibilità di applicare differenti modelli di servizio ai flussi di attributi |● |● | | | |
| Possibilità di interrompere il flusso di attributi da AD ad Azure AD |● |● | | | |
| Personalizzazione avanzata dei flussi di attributi |● |● | |● |● |

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).

