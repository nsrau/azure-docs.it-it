---
title: 'Servizio di sincronizzazione Azure AD Connect: abilitare il Cestino di Active Directory | Microsoft Docs'
description: "Questo argomento consiglia l'utilizzo della funzionalità Cestino di Active Directory con Azure AD Connect."
services: active-directory
keywords: Cestino di Active Directory, eliminazione accidentale, ancoraggio di origine
documentationcenter: 
author: cychua
manager: mtillman
editor: 
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 36a9b5a50a18b73626b0c6e03ed258e387d5c20b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Servizio di sincronizzazione Azure AD Connect: abilitare il Cestino di Active Directory
È consigliabile abilitare la funzionalità Cestino di Active Directory per le Active Directory locali che vengono sincronizzate con Azure AD. 

Se si elimina accidentalmente un oggetto utente di AD locale e lo si ripristina usando la funzionalità, Azure AD ripristina l'oggetto utente Azure AD corrispondente.  Per informazioni sulla funzionalità Cestino di Active Directory, fare riferimento all'articolo [Scenario Overview for Restoring Deleted Active Directory Objects (Panoramica sullo scenario per il ripristino di oggetti di Active Directory eliminati)](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Vantaggi dell'abilitazione del Cestino di Active Directory
Questa funzionalità consente di ripristinare gli oggetti utente di Azure AD effettuando le operazioni seguenti:

* Se si elimina accidentalmente un oggetto utente di AD locale, l’oggetto utente di Azure AD corrispondente verrà eliminato nel ciclo di sincronizzazione successivo. Per impostazione predefinita, Azure AD mantiene per 30 giorni l'oggetto utente di Azure AD eliminato in uno stato di eliminazione temporanea.

* Se la funzionalità Cestino di Active Directory locale è abilitata, è possibile ripristinare l'oggetto utente di AD locale eliminato senza modificarne il valore di ancoraggio di origine. Quando l’oggetto utente di AD locale ripristinato viene sincronizzato con Azure AD, quest’ultimo ripristinerà il corrispondente oggetto utente di Azure AD che si trova nello stato di eliminazione temporanea. Per informazioni sull'attributo di ancoraggio di origine, fare riferimento all'articolo [Azure AD Connect: Concetti relativi alla progettazione](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Se la funzionalità Cestino di Active Directory locale non è abilitata, potrebbe essere necessario creare un oggetto utente AD per sostituire l'oggetto eliminato. Se il servizio di sincronizzazione di Azure AD Connect è configurato per usare l'attributo di AD generato dal sistema (ad esempio ObjectGuid) per l'attributo di ancoraggio di origine, l'oggetto utente di AD appena creato non avrà lo stesso valore di ancoraggio di origine dell'oggetto utente di AD eliminato. Quando l'oggetto utente di AD appena creato viene sincronizzato con Azure AD, quest’ultimo crea un nuovo oggetto utente di Azure AD anziché ripristinare l'oggetto utente di Azure AD in stato di eliminazione temporanea.

> [!NOTE]
> Per impostazione predefinita, Azure AD mantiene gli oggetti utente di Azure AD in uno stato di eliminazione temporanea per 30 giorni, prima che vengano eliminati definitivamente. Tuttavia, gli amministratori possono accelerare l'eliminazione di tali oggetti. Dopo che gli oggetti sono stati eliminati in modo permanente, non possono più essere ripristinati, anche se è abilitata la funzionalità Cestino di Active Directory locale.



## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
