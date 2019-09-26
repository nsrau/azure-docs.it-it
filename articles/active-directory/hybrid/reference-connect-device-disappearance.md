---
title: Informazioni Azure AD Connect 1.4. XX. x e la scomparsa del dispositivo | Microsoft Docs
description: Questo documento descrive un problema che si verifica con la versione 1.4. XX. x di Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 1ba17feea880bb55c3b4a14a06b3d803dba2350a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316954"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Informazioni Azure AD Connect 1.4. XX. x e la scomparsa del dispositivo
Con la versione 1.4. XX. x di Azure AD Connect, alcuni clienti potrebbero visualizzare alcuni o tutti i dispositivi Windows scomparsi da Azure AD. Questo non è un problema, poiché queste identità dei dispositivi non vengono usate da Azure AD durante l'autorizzazione dell'accesso condizionale. Questa modifica non eliminerà i dispositivi Windows registrati correttamente con Azure AD per Aggiunta ad Azure AD ibrido.

Se viene visualizzata l'eliminazione degli oggetti dispositivo in Azure AD superando la soglia di eliminazione dell'esportazione, è consigliabile che il cliente consenta il passaggio delle eliminazioni. [Procedura: consentire la propagazione delle eliminazioni quando superano la soglia di eliminazione](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Sfondo
I dispositivi Windows registrati come Azure AD ibrido Uniti sono rappresentati in Azure AD come oggetti dispositivo. Questi oggetti dispositivo possono essere utilizzati per l'accesso condizionale. I dispositivi Windows 10 vengono sincronizzati con il cloud tramite Azure AD Connect, i dispositivi Windows di livello inferiore vengono registrati direttamente usando AD FS o Single Sign-On Seamless.

## <a name="windows-10-devices"></a>Dispositivi Windows 10
Solo i dispositivi Windows 10 con un valore dell'attributo userCertificate specifico configurato da Aggiunta ad Azure AD ibrido devono essere sincronizzati con il Cloud Azure AD Connect. Nelle versioni precedenti di Azure AD Connect questo requisito non è stato applicato rigorosamente, causando la Azure AD di oggetti dispositivo non necessari. Tali dispositivi in Azure AD sempre rimasti nello stato "in sospeso" perché questi dispositivi non erano destinati alla registrazione con Azure AD. 

Con questa versione di Azure AD Connect verranno sincronizzati solo i dispositivi Windows 10 configurati correttamente per essere Azure AD ibrido Uniti in join. Gli oggetti dispositivo Windows 10 senza il Azure AD userCertificate specifico join verranno rimossi da Azure AD.

## <a name="down-level-windows-devices"></a>Dispositivi Windows di livello inferiore
Azure AD Connect non devono mai sincronizzare i [dispositivi Windows di livello inferiore](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Tutti i dispositivi in Azure AD precedentemente sincronizzati in modo non corretto verranno eliminati da Azure AD. Se Azure AD Connect sta tentando di eliminare i [dispositivi Windows di livello inferiore](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), il dispositivo non è quello creato da [Microsoft workplace join per i computer non Windows 10 MSI](https://www.microsoft.com/download/details.aspx?id=53554) e non può essere utilizzato da altre funzionalità di Azure ad.

È possibile che alcuni clienti debbano [rivedere come: Pianificare l'implementazione](../devices/hybrid-azuread-join-plan.md) ibrida di Azure Active Directory join per ottenere la registrazione corretta dei dispositivi Windows e assicurarsi che tali dispositivi possano partecipare completamente all'accesso condizionale basato su dispositivo. 

## <a name="next-steps"></a>Passaggi successivi
- [Cronologia delle versioni di Azure AD Connect](reference-connect-version-history.md)
