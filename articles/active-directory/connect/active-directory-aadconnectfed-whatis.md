---
title: Azure AD Connect e federazione | Documentazione Microsoft
description: "Questa pagina è il punto centrale per tutta la documentazione correlata alle operazioni di ADFS che usano Azure AD Connect."
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 28df9bfec4db8e3f57db61eecc631545e389085a
ms.lasthandoff: 03/28/2017


---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect e federazione
Azure Active Directory (Azure AD) consente di configurare la federazione con Active Directory Federation Services (ADFS) locale e Azure AD. Grazie all'accesso federato, gli utenti possono accedere ai servizi basati su Azure AD con le proprie password locali e, se usano la rete aziendale, senza dover immettere di nuovo le password. Usando l'opzione di federazione con ADFS, è possibile distribuire una nuova installazione di ADFS oppure specificare un'installazione esistente in una farm di Windows Server 2012 R2.

Questo argomento è il punto centrale per le informazioni sulle funzionalità relative alla federazione per Azure AD Connect e contiene un elenco di collegamenti ad altri argomenti correlati. Per collegamenti relativi ad Azure AD Connect, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect - Argomenti sulla federazione
| Argomento | Contenuti e destinatari |
|:--- |:--- |
| **Opzioni di accesso utente di Azure AD Connect** | |
| [Informazioni sulle opzioni di accesso utente](active-directory-aadconnect-user-signin.md) |Informazioni sulle diverse opzioni di accesso utente e del modo in cui influiscono sull'esperienza utente di accesso di Azure. |
| **Installare ADFS con Azure AD Connect** | |
| [Prerequisiti](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Vedere i prerequisiti per una corretta installazione di ADFS con Azure AD Connect. |
| [Configurare una farm ADFS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Installare una nuova farm ADFS tramite Azure AD Connect. |
| **Modifica della configurazione di ADFS** | |
| [Ripristinare il trust](active-directory-aadconnect-federation-management.md#repairthetrust) |Ripristinare il trust corrente tra l'istanza di ADFS locale e Office 365/Azure. |
| [Aggiungere un nuovo server ADFS](active-directory-aadconnect-federation-management.md#addadfsserver) |Espandere la farm ADFS con un server ADFS aggiuntivo dopo l'installazione iniziale. |
| [Aggiungere un nuovo server WAP ADFS](active-directory-aadconnect-federation-management.md#addwapserver) |Espandere la farm ADFS con un server Web Application Proxy (WAP) aggiuntivo dopo l'installazione iniziale. |
| [Aggiungere un nuovo dominio federato](active-directory-aadconnect-federation-management.md#addfeddomain) |Aggiungere un altro dominio per la federazione con Azure AD. |
| [Aggiornare il certificato SSL](active-directory-aadconnectfed-ssl-update.md)| Aggiornare il certificato SSL per una farm ADFS. |
| **Attività successive all'installazione** | |
| [Aggiungere l'illustrazione o il logo personalizzato della società](active-directory-aadconnect-federation-management.md#customlogo) |Come modificare l'esperienza di accesso specificando il logo personalizzato che viene visualizzato nella pagina di accesso di ADFS. |
| [Aggiungere una descrizione di accesso](active-directory-aadconnect-federation-management.md#addsignindescription) |Modificare la descrizione di accesso nella pagina di accesso di ADFS. |
| [Modificare le regole attestazioni per AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Modificare o aggiungere le regole attestazioni ADFS corrispondenti alla configurazione della sincronizzazione di Azure AD Connect. |

## <a name="additional-resources"></a>Risorse aggiuntive
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
* [Distribuzione di AD FS in Azure](active-directory-aadconnect-azure-adfs.md)
* [Distribuzione di ADFS a disponibilità elevata tra aree geografiche in Azure con Gestione traffico di Azure](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

