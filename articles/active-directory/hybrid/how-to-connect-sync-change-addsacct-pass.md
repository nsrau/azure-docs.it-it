---
title: "Servizio di sincronizzazione Azure AD Connect:  modifica della password dell'account Active Directory Domain Services | Microsoft Docs"
description: Il documento di questo argomento descrive come aggiornare Azure AD Connect dopo aver modificato la password dell'account Active Directory Domain Services.
services: active-directory
keywords: account Active Directory Domain Services, account di Active Directory, password
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60241601"
---
# <a name="changing-the-ad-ds-account-password"></a>Modifica della password dell'account Active Directory Domain Services
L'account Active Directory Domain Services fa riferimento all'account utente usato da Azure AD Connect per comunicare con Active Directory locale. Se si modifica la password dell'account Active Directory Domain Services, è necessario aggiornare il servizio di sincronizzazione servizio Azure AD Connect con la nuova password. In caso contrario, la sincronizzazione non verrà più eseguita in maniera corretta con Active Directory locale e si verificheranno gli errori seguenti:

* In Synchronization Service Manager, qualsiasi operazione di importazione o esportazione con AD locale ha esito negativo con errore **no-start-credentials**.

* In Visualizzatore eventi di Windows, il registro eventi dell'applicazione contiene un errore con **Event ID 6000** e il messaggio **'The management agent "contoso.com" failed to run because the credentials were invalid'** .


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Come aggiornare il servizio di sincronizzazione con la nuova password per l’account Active Directory Domain Services
Per aggiornare il servizio di sincronizzazione con la nuova password:

1. Avviare Synchronization Service Manager (START → Servizio di sincronizzazione).
</br>![Synchronization Service Manager](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Passare alla scheda **Connettori**.

3. Selezionare il **connettore AD** che corrisponde all'account Active Directory Domain Services per cui è stata modificata la password.

4. In **Azioni** selezionare **Proprietà**.

5. Nella finestra di dialogo popup, selezionare **Connetti a Foresta Active Directory**:

6. Immettere la nuova password dell'account Active Directory Domain Services nella casella di testo **Password**.

7. Fare clic su **OK** per salvare la nuova password e chiudere la finestra di dialogo popup.

8. Riavviare il servizio di sincronizzazione Azure AD Connect in Gestione controllo servizi di Windows. In questo modo si garantisce che qualsiasi riferimento alla vecchia password venga rimosso dalla cache in memoria.

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
