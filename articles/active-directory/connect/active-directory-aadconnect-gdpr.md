---
title: Azure AD Connect e il Regolamento generale sulla protezione dei dati | Microsoft Docs
description: "Questo documento descrive come ottenere la conformità al GDPR con Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: 68b36e1806d3338b07d4c2c400de233aff270132
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect"></a>Conformità al GDPR e Azure AD Connect 

A maggio 2018 entrerà in vigore una legge sulla privacy europea, il [Regolamento generale sulla protezione dei dati (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm). Il GDPR impone nuove regole per le aziende, gli enti pubblici, le organizzazioni no profit e altre organizzazioni che offrono beni e servizi a persone nell'Unione europea o che raccolgono e analizzano dati collegati a persone residenti nell'Unione europea. Il GDPR si applica indipendentemente da dove si trovano tali aziende, enti e organizzazioni. 

Sono oggi disponibili prodotti e servizi Microsoft che aiutano a soddisfare i requisiti del GDPR. Per altri dettagli sull'Informativa sulla privacy Microsoft, consultare il [Trust Center](https://www.microsoft.com/trustcenter)

>[!NOTE] 
>Il presente articolo affronta la conformità al GDPR con Azure AD Connect.  Per informazioni su Azure AD Connect Health e sulla conformità al GDPR, vedere l'articolo [qui](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

È possibile ottenere la conformità al Regolamento generale sulla protezione dei dati per le installazioni di Azure AD Connect in due modi:

1.  Qualora richiesto, estrarre i dati per un utente e rimuovere i dati di tale utente dalle installazioni.
2.  Verificare che nessun dato venga conservato per più di 48 ore.

Il team di Azure AD Connect consiglia la seconda opzione.  Questo perché si tratta della modalità di gran lunga più semplice da implementare e gestire.

Un server di sincronizzazione di Azure AD Connect archivia i dati seguenti che rientrano nell'ambito della conformità al GDPR:
1.  Dati relativi a un utente nel **database di Azure AD Connect**
2.  Dati nei file del **Log eventi di Windows** che potrebbero contenere informazioni relative a un utente
3.  Dati nei **file di log dell'installazione di Azure AD Connect** che potrebbero contenere informazioni relative a un utente

Per essere conformi al GDPR, i clienti di Azure AD Connect devono usare le linee guida seguenti:
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
    ![Sistema](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  Nell'intestazione Strumenti di amministrazione, fare clic su **Pianifica attività**.
    ![Task](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  In Utilità di pianificazione, fare clic con il pulsante destro del mouse su **Libreria Utilità di pianificazione** e fare clic su **Crea attività di base...**
4.  Immettere il nome per la nuova attività e fare clic su **Avanti**.
5.  Selezionare **Ogni giorno** per l'attivazione dell'attività e fare clic su **Avanti**.
6.  Impostare la ricorrenza su **2 giorni** e fare clic su **Avanti**.
7.  Selezionare **Avvio programma** come azione e fare clic su **Avanti**.
8.  Digitare **PowerShell** nella casella Programma/script, mentre nella casella **Aggiungi argomenti (facoltativo):** immettere il percorso completo dello script creato in precedenza e quindi fare clic su **Avanti**.
9.  La schermata successiva mostra un riepilogo dell'attività che si sta per creare. Verificare i valori e fare clic su **Fine** per creare l'attività.



## <a name="next-steps"></a>Passaggi successivi
- [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
- [Azure AD Connect Health e il GDPR](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)