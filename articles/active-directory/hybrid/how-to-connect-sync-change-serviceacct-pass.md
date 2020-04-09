---
title: "Sincronizzazione di Azure AD Connect: modifica dell'account del servizio ADSync Documenti Microsoft"
description: Questo argomento descrive la chiave di crittografia e come abbandonarla dopo la modifica della password.
services: active-directory
keywords: Account del servizio Azure AD Sync, password
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
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b2a0d0b77b6db481b13785907a1359d2bbe3e9b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984505"
---
# <a name="changing-the-adsync-service-account-password"></a>Modifica della password dell'account del servizio ADSync
Se si modifica la password dell'account del servizio ADSync, il servizio di sincronizzazione non sarà in grado di avviarsi correttamente fino a quando non si è abbandonata la chiave di crittografia e reinizializzata la password dell'account del servizio ADSync. 

Azure AD Connect, come parte di Synchronization Services usa una chiave di crittografia per archiviare le password dell'account del connettore di Servizi di dominio Active Directory e dell'account del servizio ADSync.  Questi account vengono crittografati prima di essere archiviati nel database. 

La chiave di crittografia usata viene protetta con [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI protegge la chiave di crittografia utilizzando l'account del **servizio ADSync.** 

Se è necessario modificare la password dell'account del servizio, è possibile utilizzare le procedure descritte in Abbandonare la chiave di [crittografia dell'account](#abandoning-the-adsync-service-account-encryption-key) del servizio ADSync per eseguire questa operazione.  Queste procedure devono essere usate anche se è necessario abbandonare la chiave di crittografia per qualsiasi altro motivo.

## <a name="issues-that-arise-from-changing-the-password"></a>Problemi derivati dalla modifica della password
Quando si modifica la password dell'account del servizio, è necessario eseguire due operazioni.

Prima di tutto, è necessario modificare la password in Gestione controllo servizi di Windows.  Finché non si risolve questo problema, verranno visualizzati gli errori seguenti:


- Se si prova ad avviare il servizio di sincronizzazione in Gestione controllo servizi di Windows, viene visualizzato l'errore "**Impossibile avviare il servizio Microsoft Azure AD Sync su computer locale**". **Errore 1069: Il servizio non è stato avviato a causa di un errore in fase di accesso**".
- Nel Visualizzatore eventi di Windows il registro eventi di sistema contiene un errore con **ID evento 7038** e il messaggio "**Il servizio ADSync non è stato in grado di accedere con la password al momento configurata, a causa del seguente errore: Password o nome utente errato**".

In secondo luogo, se in condizioni specifiche la password viene aggiornata, il servizio di sincronizzazione non può più recuperare la chiave di crittografia tramite DPAPI. Senza la chiave di crittografia, il servizio di sincronizzazione non può decrittografare le password necessarie per eseguire la sincronizzazione a/da Active Directory e Azure AD locali.
Verranno visualizzati gli errori seguenti:

- In Gestione controllo servizi di Windows, se si tenta di avviare il servizio di sincronizzazione e non è possibile recuperare la chiave di crittografia, non riesce con errore<strong>"Windows non è stato possibile avviare la sincronizzazione di Microsoft Azure AD nel computer locale. Per ulteriori informazioni, esaminare il registro eventi di sistema. Se si tratta di un servizio non Microsoft, contattare il fornitore del servizio e fare riferimento al codice di errore specifico del servizio -21451857952</strong>."
- Nel Visualizzatore eventi di Windows, il registro eventi dell'applicazione contiene un errore con **ID evento 6028** e il messaggio di errore "Impossibile accedere alla chiave di *crittografia del server".*

Per assicurarsi di non ricevere questi errori, seguire le procedure descritte in Abbandonare la chiave di [crittografia dell'account](#abandoning-the-adsync-service-account-encryption-key) del servizio ADSync quando si modifica la password.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>Abbandono della chiave di crittografia dell'account del servizio ADSync
>[!IMPORTANT]
>Le procedure seguenti si applicano solo ad Azure AD Connect build 1.1.443.0 o precedenti.

Usare le procedure seguenti per abbandonare la chiave di crittografia.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Che cosa fare se è necessario abbandonare la chiave di crittografia

Se è necessario abbandonare la chiave di crittografia, usare le procedure seguenti.

1. [Arrestare il servizio di sincronizzazione](#stop-the-synchronization-service)

1. [Abbandonare la chiave di crittografia esistente](#abandon-the-existing-encryption-key)

2. [Fornire la password dell'account del connettore di Servizi di dominio Active Directory](#provide-the-password-of-the-ad-ds-connector-account)

3. [Reinizializzare la password dell'account del servizio ADSync](#reinitialize-the-password-of-the-adsync-service-account)

4. [Avviare il servizio di sincronizzazione](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>Arrestare il servizio di sincronizzazione
Innanzitutto è possibile arrestare il servizio in Gestione controllo servizi di Windows.  Assicurarsi che il servizio non sia in esecuzione quando si tenta di arrestarlo.  In tal caso, attendere il termine dell'operazione e quindi arrestarlo.


1. Passare a Gestione controllo servizi di Windows (START → Servizi).
2. Selezionare **Microsoft Azure AD Sync** e fare clic su Arresta.

#### <a name="abandon-the-existing-encryption-key"></a>Abbandonare la chiave di crittografia esistente
Abbandonare la chiave di crittografia esistente per poterne creare una nuova:

1. Accedere al server Azure AD Connect come amministratore.

2. Avviare una nuova sessione di PowerShell.

3. Passare alla cartella `'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Eseguire il comando `./miiskmu.exe /a`

![Utilità per la chiave di crittografia del servizio di sincronizzazione Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Fornire la password dell'account del connettore di Servizi di dominio Active Directory
Poiché le password esistenti archiviate all'interno del database non possono più essere decrittografate, è necessario fornire al servizio di sincronizzazione la password dell'account del connettore di Servizi di dominio Active Directory. Il servizio di sincronizzazione crittografa le password usando la nuova chiave di crittografia:

1. Avviare Synchronization Service Manager (START → Servizio di sincronizzazione).
</br>![Synchronization Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Passare alla scheda **Connettori**.
3. Selezionare il connettore **AD Connector** corrispondente ad Active Directory locale. Se sono presenti più connettori, ripetere i passaggi seguenti per ognuno.
4. In **Azioni** selezionare **Proprietà**.
5. Nella finestra di dialogo popup selezionare **Connetti a Foresta Active Directory**:
6. Immettere la nuova password dell'account Active Directory Domain Services nella casella di testo **Password**. Se non si conosce questa password, è necessario impostarla su un valore noto prima di eseguire questo passaggio.
7. Fare clic su **OK** per salvare la nuova password e chiudere la finestra di dialogo popup.
![Utilità per la chiave di crittografia del servizio di sincronizzazione Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Reinizializzare la password dell'account del servizio ADSync
Non è possibile fornire direttamente la password dell'account del servizio Azure AD al servizio di sincronizzazione. È invece necessario usare il cmdlet **Add-ADSyncAADServiceAccount** per reinizializzare l'account del servizio Azure AD. Il cmdlet reimposta la password dell'account e la rende disponibile al servizio di sincronizzazione:

1. Avviare una nuova sessione di PowerShell nel server Azure AD Connect.
2. Eseguire il cmdlet `Add-ADSyncAADServiceAccount`.
3. Nella finestra di dialogo popup immettere le credenziali di amministratore globale di Azure AD per il tenant di Azure AD.
![Utilità per la chiave di crittografia del servizio di sincronizzazione Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Se questo passaggio riesce, verrà visualizzato il prompt dei comandi di PowerShell.

#### <a name="start-the-synchronization-service"></a>Avviare il servizio di sincronizzazione
Ora che il servizio di sincronizzazione ha accesso alla chiave di crittografia e a tutte le password necessarie, è possibile riavviare il servizio in Gestione controllo servizi di Windows:


1. Passare a Gestione controllo servizi di Windows (START → Servizi).
2. Selezionare **Microsoft Azure AD Sync** e fare clic su Riavvia.

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
