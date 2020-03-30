---
title: Informazioni su Azure AD Connect 1.4.xx.x e la scomparsa del dispositivo Documenti Microsoft
description: Questo documento descrive un problema che si verifica con la versione 1.4.xx.x di Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176034"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Informazioni su Azure AD Connect 1.4.xx.x e scomparsa del dispositivoUnderstanding Azure AD Connect 1.4.xx.x and device disappearance
Con la versione 1.4.xx.x di Azure AD Connect, alcuni clienti potrebbero vedere alcuni o tutti i dispositivi Windows scomparire da Azure AD. Questo non è un motivo di preoccupazione, poiché queste identità del dispositivo non vengono usate da Azure AD durante l'autorizzazione di accesso condizionale. Questa modifica non eliminerà i dispositivi Windows registrati correttamente con Azure AD per l'aggiunta ad Azure AD ibrido.

Se viene visualizzata l'eliminazione di oggetti dispositivo in Azure AD che supera la soglia di eliminazione dell'esportazione, è consigliabile che il cliente consenta l'attraversamento delle eliminazioni. [Procedura: consentire il flusso delle eliminazioni quando superano la soglia di eliminazioneHow To: allow deletes to flow when they exceed the deletion threshold](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Background
I dispositivi Windows registrati come aggiunta ad Azure AD ibrido sono rappresentati in Azure AD come oggetti dispositivo. Questi oggetti dispositivo possono essere utilizzati per l'accesso condizionale. I dispositivi Windows 10 vengono sincronizzati nel cloud tramite Azure AD Connect, i dispositivi Windows di livello inferiore vengono registrati direttamente tramite AD FS o Single Sign-On senza soluzione di continuità.

## <a name="windows-10-devices"></a>Dispositivi Windows 10
Solo i dispositivi Windows 10 con un valore di attributo userCertificate specifico configurato dall'aggiunta ad Azure AD ibrido dovrebbero essere sincronizzati con il cloud da Azure AD Connect.Only Windows 10 devices with a specific userCertificate attribute value configured by Hybrid Azure AD Join are supposed to be synced to the cloud by Azure AD Connect. Nelle versioni precedenti di Azure AD Connect questo requisito non veniva applicato in modo rigoroso, generando oggetti dispositivo non necessari in Azure AD. Tali dispositivi in Azure AD sono sempre rimasti nello stato "in sospeso" perché questi dispositivi non erano destinati a essere registrati con Azure AD. 

Questa versione di Azure AD Connect sincronizzerà solo i dispositivi Windows 10 configurati correttamente per essere aggiunti ad Azure AD ibrido. Gli oggetti dispositivo di Windows 10 senza l'account utente specifico di Azure AD verranno rimossi da Azure AD.

## <a name="down-level-windows-devices"></a>Dispositivi Windows di livello inferiore
Azure AD Connect non deve mai sincronizzare i dispositivi Windows di [livello inferiore.](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices) Tutti i dispositivi in Azure AD sincronizzati in modo non corretto verranno eliminati da Azure AD. Se Azure AD Connect sta tentando di eliminare i dispositivi Windows di [livello inferiore,](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)il dispositivo non è quello creato dal file MSI Di aggiunta a [Microsoft Workplace per computer non Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) e non può essere utilizzato da altre funzionalità di Azure AD.

Alcuni clienti potrebbero dover rivedere [procedura: pianificare l'implementazione di join ibrida](../devices/hybrid-azuread-join-plan.md) di Azure Active Directory per registrare correttamente i dispositivi Windows e assicurarsi che tali dispositivi possano partecipare completamente all'accesso condizionale basato su dispositivo. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Come posso verificare quali dispositivi vengono eliminati con questo aggiornamento?

Per verificare quali dispositivi vengono eliminati, è possibile usare questo script di PowerShell:To verify which devices are deleted, you can use this PowerShell script:https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Questo script genera un report sui certificati archiviati negli oggetti Computer di Active Directory, in particolare i certificati emessi dalla funzionalità di aggiunta ad Azure AD ibrido.
Controlla i certificati presenti nella proprietà UserCertificate di un oggetto Computer in Active Directory e, per ogni certificato non scaduto presente, verifica se il certificato è stato emesso per la funzionalità di aggiunta ad Azure AD ibrido (ad esempio, Il nome del soggetto corrisponde a CN , ObjectGUID ).
Prima, Azure AD Connect si sincronizzava con Azure AD qualsiasi computer che conteneva almeno un certificato valido, ma a partire da Azure AD Connect versione 1.4, il motore di sincronizzazione è in grado di identificare i certificati di aggiunta di Azure AD ibrido e oggetto computer dalla sincronizzazione ad Azure AD, a meno che non sia presente un certificato di aggiunta ad Azure AD ibrido valido.
I dispositivi Azure AD che sono già stati sincronizzati con Active Directory ma che non dispongono di un certificato di aggiunta ad Azure AD ibrido valido verranno eliminati (CloudFiltered- TRUE) dal motore di sincronizzazione.

## <a name="next-steps"></a>Passaggi successivi
- [Cronologia delle versioni di Azure AD ConnectAzure AD Connect Version history](reference-connect-version-history.md)
