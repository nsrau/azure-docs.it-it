---
title: Azure AD Connect e privacy dell'utente | Microsoft Docs
description: Questo documento descrive come ottenere la conformità al GDPR con Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2dedd85445a4c04ffee35260b33dedee277d3592
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477354"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Privacy dell'utente e Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Il presente articolo affronta Azure AD Connect e la privacy dell'utente.  Per informazioni su Azure AD Connect Health e la privacy dell'utente, vedere l'articolo [qui](reference-connect-health-user-privacy.md).

Migliorare la privacy dell’utente per le installazioni Azure AD Connect in due modi:

1.  Qualora richiesto, estrarre i dati per un utente e rimuovere i dati di tale utente dalle installazioni.
2.  Verificare che nessun dato venga conservato per più di 48 ore.

Il team di Azure AD Connect consiglia la seconda opzione perché è molto più semplice da implementare e gestire.

Un server di sincronizzazione Azure AD Connect archivia i dati sulla privacy dell'utente seguenti:
1.  Dati relativi a un utente nel **database di Azure AD Connect**
2.  Dati nei file del **Log eventi di Windows** che potrebbero contenere informazioni relative a un utente
3.  Dati nei **file di log dell'installazione di Azure AD Connect** che potrebbero contenere informazioni relative a un utente

I clienti di Azure AD Connect devono usare le linee guida seguenti quando rimuovono i dati di un utente:
1.  Eliminare regolarmente (almeno ogni 48 ore) il contenuto della cartella che contiene i file di log dell'installazione di Azure AD Connect
2.  Questo prodotto potrebbe anche creare Log eventi.  Per ulteriori informazioni sui log di Log eventi, vedere la [documentazione qui](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

I dati relativi a un utente vengono automaticamente rimossi dal database di Azure AD Connect quando i dati di tale utente vengono rimossi dal sistema di origine. Non è richiesta alcuna azione specifica da parte degli amministratori per la conformità al GDPR.  Tuttavia, è necessario che i dati di Azure AD Connect vengano sincronizzati con l'origine dati almeno ogni due giorni.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Eliminare il contenuto della cartella del file di log dell'installazione di Azure AD Connect
Controllare regolarmente ed eliminare il contenuto della cartella **c:\programdata\aadconnect**, eccetto per il file **PersistedState.Xml**. Questo file mantiene lo stato dell'installazione precedente di Azure AD Connect e viene usato quando viene eseguita un'installazione dell'aggiornamento. Questo file non contiene dati relativi a un utente e non deve essere eliminato.

>[!IMPORTANT]
>Non eliminare il file PersistedState.xml.  Questo file non contiene alcuna informazione relativa agli utenti e mantiene lo stato dell'installazione precedente.

È possibile esaminare ed eliminare questi file tramite Esplora risorse oppure è possibile usare uno script simile al seguente per eseguire le azioni necessarie:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Pianificare l'esecuzione di questo script ogni 48 ore
Usare la procedura seguente per pianificare l'esecuzione dello script ogni 48 ore.

1.  Salvare lo script in un file con estensione **&#46;PS1** e quindi aprire il Pannello di controllo e fare clic su **Sistema e sicurezza**.
    ![Sistema](./media/reference-connect-user-privacy/gdpr2.png)

2.  Nell'intestazione Strumenti di amministrazione, fare clic su **Pianifica attività**.
    ![Attività](./media/reference-connect-user-privacy/gdpr3.png)
3.  In Utilità di pianificazione, fare clic con il pulsante destro del mouse su **Libreria Utilità di pianificazione** e fare clic su **Crea attività di base...**
4.  Immettere il nome per la nuova attività e fare clic su **Avanti**.
5.  Selezionare **Ogni giorno** per l'attivazione dell'attività e fare clic su **Avanti**.
6.  Impostare la ricorrenza su **2 giorni** e fare clic su **Avanti**.
7.  Selezionare **Avvio programma** come azione e fare clic su **Avanti**.
8.  Digitare **PowerShell** nella casella Programma/script, mentre nella casella **Aggiungi argomenti (facoltativo):** immettere il percorso completo dello script creato in precedenza e quindi fare clic su **Avanti**.
9.  La schermata successiva mostra un riepilogo dell'attività che si sta per creare. Verificare i valori e fare clic su **Fine** per creare l'attività.



## <a name="next-steps"></a>Passaggi successivi
* [Consultare l'Informativa sulla privacy Microsoft nel Trust Center](https://www.microsoft.com/trustcenter)
- [Azure AD Connect Health e privacy dell'utente](reference-connect-health-user-privacy.md)
