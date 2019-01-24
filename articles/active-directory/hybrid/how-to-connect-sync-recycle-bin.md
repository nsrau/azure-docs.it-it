---
title: 'Servizio di sincronizzazione Azure AD Connect: Abilitare il Cestino di Active Directory | Microsoft Docs'
description: Questo argomento consiglia l'utilizzo della funzionalità Cestino di Active Directory con Azure AD Connect.
services: active-directory
keywords: Cestino di Active Directory, eliminazione accidentale, ancoraggio di origine
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4836ffc8c6ab013ef5ad7a661db0df3254b6d4e1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468761"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Servizio di sincronizzazione Azure AD Connect: Abilitare il Cestino di AD
È consigliabile abilitare la funzionalità Cestino di Active Directory per le Active Directory locali che vengono sincronizzate con Azure AD. 

Se si elimina accidentalmente un oggetto utente di AD locale e lo si ripristina usando la funzionalità, Azure AD ripristina l'oggetto utente Azure AD corrispondente.  Per informazioni sulla funzionalità Cestino di Active Directory, fare riferimento all'articolo [Scenario Overview for Restoring Deleted Active Directory Objects (Panoramica sullo scenario per il ripristino di oggetti di Active Directory eliminati)](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Vantaggi dell'abilitazione del Cestino di Active Directory
Questa funzionalità consente di ripristinare gli oggetti utente di Azure AD effettuando le operazioni seguenti:

* Se si elimina accidentalmente un oggetto utente di AD locale, l’oggetto utente di Azure AD corrispondente verrà eliminato nel ciclo di sincronizzazione successivo. Per impostazione predefinita, Azure AD mantiene per 30 giorni l'oggetto utente di Azure AD eliminato in uno stato di eliminazione temporanea.

* Se la funzionalità Cestino di Active Directory locale è abilitata, è possibile ripristinare l'oggetto utente di AD locale eliminato senza modificarne il valore di ancoraggio di origine. Quando l’oggetto utente di AD locale ripristinato viene sincronizzato con Azure AD, quest’ultimo ripristinerà il corrispondente oggetto utente di Azure AD che si trova nello stato di eliminazione temporanea. Per informazioni sull'attributo di ancoraggio di origine, consultare l'articolo [Azure AD Connect: Concetti relativi alla progettazione](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Se la funzionalità Cestino di Active Directory locale non è abilitata, potrebbe essere necessario creare un oggetto utente AD per sostituire l'oggetto eliminato. Se il servizio di sincronizzazione di Azure AD Connect è configurato per usare l'attributo di AD generato dal sistema (ad esempio ObjectGuid) per l'attributo di ancoraggio di origine, l'oggetto utente di AD appena creato non avrà lo stesso valore di ancoraggio di origine dell'oggetto utente di AD eliminato. Quando l'oggetto utente di AD appena creato viene sincronizzato con Azure AD, quest’ultimo crea un nuovo oggetto utente di Azure AD anziché ripristinare l'oggetto utente di Azure AD in stato di eliminazione temporanea.

> [!NOTE]
> Per impostazione predefinita, Azure AD mantiene gli oggetti utente di Azure AD in uno stato di eliminazione temporanea per 30 giorni, prima che vengano eliminati definitivamente. Tuttavia, gli amministratori possono accelerare l'eliminazione di tali oggetti. Dopo che gli oggetti sono stati eliminati in modo permanente, non possono più essere ripristinati, anche se è abilitata la funzionalità Cestino di Active Directory locale.

>[!NOTE]
>In precedenza, quando un utente veniva rimosso dall'ambito della sincronizzazione in locale ed eliminato nel cloud, lo stato DirSyncEnabled dell'account veniva impostato erroneamente su "False". Se, in seguito, tale utente veniva ripristinato manualmente dal cestino di Azure AD, si visualizzava lo stato erroneo "Solo Cloud". Questo problema ora è stato risolto e il valore dello stato DirSyncEnabled viene sempre mantenuto su "True" quando un utente viene rimosso dall'ambito di sincronizzazione, quindi eliminato temporaneamente e ripristinato manualmente dal cestino di Azure AD.

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
