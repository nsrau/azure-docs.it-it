---
title: 'Servizio di sincronizzazione Azure AD Connect: modifica dell''account del servizio di sincronizzazione Azure AD Connect | Microsoft Docs'
description: Questo argomento descrive la chiave di crittografia e come abbandonarla dopo la modifica della password.
services: active-directory
keywords: Account del servizio Azure AD Sync, password
documentationcenter: 
author: cychua
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
ms.openlocfilehash: 0e4f143779a33bc2511974884fa16894611050f8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Modifica della password dell'account del servizio di sincronizzazione Azure AD Connect
Se si modifica la password dell'account del servizio di sincronizzazione Azure AD Connect, il servizio di sincronizzazione non verrà avviato correttamente finché non si abbandona la chiave di crittografia e non si reinizializza la password dell'account del servizio. 

Azure AD Connect, parte dei servizi di sincronizzazione, usa una chiave di crittografia per archiviare le password degli account di servizio Active Directory Domain Services e Azure AD.  Questi account vengono crittografati prima di essere archiviati nel database. 

La chiave di crittografia usata viene protetta con [Windows Data Protection (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI protegge la chiave di crittografia usando la **password dell'account del servizio di sincronizzazione Azure AD Connect**. 

Se è necessario modificare la password dell'account del servizio, è possibile usare le procedure contenute in [Abbandono della chiave di crittografia del servizio di sincronizzazione Azure AD Connect](#abandoning-the-azure-ad-connect-sync-encryption-key).  Queste procedure devono essere usate anche se è necessario abbandonare la chiave di crittografia per qualsiasi altro motivo.

##<a name="issues-that-arise-from-changing-the-password"></a>Problemi derivati dalla modifica della password
Quando si modifica la password dell'account del servizio, è necessario eseguire due operazioni.

Prima di tutto, è necessario modificare la password in Gestione controllo servizi di Windows.  Finché non si risolve questo problema, verranno visualizzati gli errori seguenti:


- Se si prova ad avviare il servizio di sincronizzazione in Gestione controllo servizi di Windows, viene visualizzato l'errore "**Impossibile avviare il servizio Microsoft Azure AD Sync su computer locale**". **Errore 1069: Il servizio non è stato avviato a causa di un errore in fase di accesso**".
- Nel Visualizzatore eventi di Windows il registro eventi di sistema contiene un errore con **ID evento 7038** e il messaggio "**Il servizio ADSync non è stato in grado di accedere con la password al momento configurata, a causa del seguente errore: Password o nome utente errato**".

In secondo luogo, se in condizioni specifiche la password viene aggiornata, il servizio di sincronizzazione non può più recuperare la chiave di crittografia tramite DPAPI. Senza la chiave di crittografia, il servizio di sincronizzazione non può decrittografare le password necessarie per eseguire la sincronizzazione a/da Active Directory e Azure AD locali.
Verranno visualizzati gli errori seguenti:

- Se in Gestione controllo servizi di Windows si prova ad avviare il servizio di sincronizzazione e questo non riesce a recuperare la chiave di crittografia, viene restituito l'errore "**Impossibile avviare il servizio Microsoft Azure AD Sync su computer locale. Per maggiori informazioni, consultare il registro eventi di sistema. Se non si tratta di un servizio Microsoft, contattare il fornitore del servizio e fare riferimento al codice di errore **-21451857952****".
- Nel Visualizzatore eventi di Windows il registro eventi dell'applicazione contiene un errore con **ID evento 6028** e il messaggio di errore *"**Impossibile accedere alla chiave di crittografia del server**".*

Per assicurarsi di non ricevere più questi errori, seguire le procedure contenute in [Abbandono della chiave di crittografia del servizio di sincronizzazione Azure AD Connect](#abandoning-the-azure-ad-connect-sync-encryption-key) quando si modifica la password.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Abbandono della chiave di crittografia del servizio di sincronizzazione Azure AD Connect
>[!IMPORTANT]
>Le procedure seguenti si applicano solo ad Azure AD Connect build 1.1.443.0 o precedenti.

Usare le procedure seguenti per abbandonare la chiave di crittografia.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Che cosa fare se è necessario abbandonare la chiave di crittografia

Se è necessario abbandonare la chiave di crittografia, usare le procedure seguenti.

1. [Abbandonare la chiave di crittografia esistente](#abandon-the-existing-encryption-key)

2. [Specificare la password dell'account Active Directory Domain Services](#provide-the-password-of-the-ad-ds-account)

3. [Reinizializzare la password dell'account Azure AD Sync](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Avviare il servizio di sincronizzazione](#start-the-synchronization-service)

#### <a name="abandon-the-existing-encryption-key"></a>Abbandonare la chiave di crittografia esistente
Abbandonare la chiave di crittografia esistente per poterne creare una nuova:

1. Accedere al server Azure AD Connect come amministratore.

2. Avviare una nuova sessione di PowerShell.

3. Passare alla cartella `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Eseguire il comando `./miiskmu.exe /a`

![Utilità per la chiave di crittografia del servizio di sincronizzazione Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Specificare la password dell'account Active Directory Domain Services
Poiché le password esistenti archiviate nel database non possono essere più decrittografate, è necessario immettere nel servizio di sincronizzazione la password dell'account Active Directory Domain Services. Il servizio di sincronizzazione crittografa le password usando la nuova chiave di crittografia:

1. Avviare Synchronization Service Manager (START → Servizio di sincronizzazione).
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Passare alla scheda **Connettori**.
3. Selezionare il connettore **AD Connector** corrispondente ad Active Directory locale. Se sono presenti più connettori, ripetere i passaggi seguenti per ognuno.
4. In **Azioni** selezionare **Proprietà**.
5. Nella finestra di dialogo popup selezionare **Connetti a Foresta Active Directory**:
6. Immettere la nuova password dell'account Active Directory Domain Services nella casella di testo **Password**. Se non si conosce questa password, è necessario impostarla su un valore noto prima di eseguire questo passaggio.
7. Fare clic su **OK** per salvare la nuova password e chiudere la finestra di dialogo popup.
![Utilità per la chiave di crittografia del servizio di sincronizzazione Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Reinizializzare la password dell'account Azure AD Sync
Non è possibile fornire direttamente la password dell'account del servizio Azure AD al servizio di sincronizzazione. È invece necessario usare il cmdlet **Add-ADSyncAADServiceAccount** per reinizializzare l'account del servizio Azure AD. Il cmdlet reimposta la password dell'account e la rende disponibile al servizio di sincronizzazione:

1. Avviare una nuova sessione di PowerShell nel server Azure AD Connect.
2. Eseguire il cmdlet `Add-ADSyncAADServiceAccount`.
3. Nella finestra di dialogo popup immettere le credenziali di amministratore globale di Azure AD per il tenant di Azure AD.
![Utilità per la chiave di crittografia del servizio di sincronizzazione Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. Se questo passaggio riesce, verrà visualizzato il prompt dei comandi di PowerShell.

#### <a name="start-the-synchronization-service"></a>Avviare il servizio di sincronizzazione
Ora che il servizio di sincronizzazione ha accesso alla chiave di crittografia e a tutte le password necessarie, è possibile riavviare il servizio in Gestione controllo servizi di Windows:


1. Passare a Gestione controllo servizi di Windows (START → Servizi).
2. Selezionare **Microsoft Azure AD Sync** e fare clic su Riavvia.

## <a name="next-steps"></a>Passaggi successivi
**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)

* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
