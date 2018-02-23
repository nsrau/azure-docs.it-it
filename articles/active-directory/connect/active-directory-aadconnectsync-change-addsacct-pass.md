---
title: "Sincronizzazione Azure AD Connect: modifica della password dell’account AD DS | Microsoft Docs"
description: Il documento di questo argomento descrive come aggiornare Azure AD Connect dopo aver modificato la password dell'account Active Directory Domain Services.
services: active-directory
keywords: account Active Directory Domain Services, account di Active Directory, password
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 5bc296b594917f68f35d7d15653b7b0c60cde514
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="changing-the-ad-ds-account-password"></a>Modifica della password dell'account Active Directory Domain Services
L'account Active Directory Domain Services fa riferimento all'account utente usato da Azure AD Connect per comunicare con Active Directory locale. Se si modifica la password dell'account Active Directory Domain Services, è necessario aggiornare il servizio di sincronizzazione servizio Azure AD Connect con la nuova password. In caso contrario, la sincronizzazione non verrà più eseguita in maniera corretta con Active Directory locale e si verificheranno gli errori seguenti:

* In Synchronization Service Manager, qualsiasi operazione di importazione o esportazione con AD locale ha esito negativo con errore **no-start-credentials**.

* In Visualizzatore eventi di Windows, il registro eventi dell'applicazione contiene un errore con **Event ID 6000** e il messaggio **'The management agent "contoso.com" failed to run because the credentials were invalid'**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Come aggiornare il servizio di sincronizzazione con la nuova password per l’account Active Directory Domain Services
Per aggiornare il servizio di sincronizzazione con la nuova password:

1. Avviare Synchronization Service Manager (START → Servizio di sincronizzazione).
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  

2. Passare alla scheda **Connettori**.

3. Selezionare il **connettore AD** che corrisponde all'account Active Directory Domain Services per cui è stata modificata la password.

4. In **Azioni** selezionare **Proprietà**.

5. Nella finestra di dialogo popup, selezionare **Connetti a Foresta Active Directory**:

6. Immettere la nuova password dell'account Active Directory Domain Services nella casella di testo **Password**.

7. Fare clic su **OK** per salvare la nuova password e chiudere la finestra di dialogo popup.

8. Riavviare il servizio di sincronizzazione Azure AD Connect in Gestione controllo servizi di Windows. In questo modo si garantisce che qualsiasi riferimento alla vecchia password venga rimosso dalla cache in memoria.

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
