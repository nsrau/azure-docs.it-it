---
title: Ripristinare un gruppo di Office 365 eliminato nell&quot;anteprima di Azure Active Directory | Microsoft Docs
description: Come ripristinare un gruppo eliminato, visualizzare i gruppi ripristinabili ed eliminare in modo permanente un gruppo in Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 4e2fe22953ff74214c0c20fdf2fabceeda63be35
ms.lasthandoff: 04/27/2017


---

# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory

Quando si elimina un gruppo di Office 365 nell'anteprima di Azure Active Directory (Azure AD), il gruppo eliminato viene conservato ma non è visibile per 30 giorni dalla data di eliminazione. In questo modo il gruppo e i relativi contenuti possono essere ripristinati se necessario. Questa funzionalità è limitata esclusivamente ai gruppi di Office 365 in Azure AD. Non è disponibile per i gruppi di sicurezza e i gruppi di distribuzione.

Per ripristinare un gruppo sono necessarie una delle autorizzazioni seguenti:

Ruolo  | autorizzazioni 
--------- | ---------
Amministratore della società, supporto partner di livello 2 e amministratori del servizio di InTune | Possono ripristinare qualsiasi gruppo di Office 365 eliminato 
Amministratore dell'account utente e supporto partner di livello 1 | Possono ripristinare qualsiasi gruppo di Office 365 eliminato, ad eccezione di quelli assegnati al ruolo di amministratore della società 
Utente | Possono ripristinare qualsiasi gruppo di Office 365 eliminato che era di loro proprietà 


## <a name="how-to-view-deleted-office-365-groups-that-are-available-to-restore"></a>Come visualizzare i gruppi di Office 365 eliminati che è possibile ripristinare
I cmdlet seguenti consente di visualizzare i gruppi eliminati per verificare che il gruppo o i gruppi a cui l'utente è interessato non siano stati ancora eliminati definitivamente. I cmdlet fanno parte del [modulo in anteprima Azure Active Directory PowerShell V2](https://www.powershellgallery.com/packages/AzureADPreview). Altre informazioni su questo modulo sono reperibili nell'articolo [PowerShell di Azure Active Directory versione 2](/powershell/azure/install-adv2?view=azureadps-2.0).
Si noti che i cmdlet per la gestione dell'eliminazione temporanea e del recupero dati sono disponibili in anteprima pubblica ed è talvolta necessario apportare modifiche significative ai cmdlet in anteprima. Per questo motivo, l'uso di questi cmdlet in un ambiente di produzione è sconsigliato.

1.    Eseguire il cmdlet seguente per visualizzare tutti i gruppi di Office 365 eliminati nel tenant per cui è ancora possibile il ripristino.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.    In alternativa, se si conosce l'objectID di un gruppo specifico (ed è possibile ottenerlo dal cmdlet nel passaggio 1), eseguire il cmdlet seguente per verificare che il gruppo eliminato specifico non sia stato ancora definitivamente eliminato.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-an-office-365-group"></a>Come ripristinare un gruppo di Office 365
Dopo avere verificato che è ancora possibile ripristinare il gruppo, ripristinare il gruppo eliminato con uno dei passaggi seguenti. Se il gruppo contiene documenti, siti SP o altri oggetti persistenti, potrebbero volerci fino a 24 ore per ripristinare completamente un gruppo e il relativo contenuto.

1.    Eseguire il cmdlet seguente per ripristinare il gruppo e il relativo contenuto.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

In alternativa, è possibile eseguire il cmdlet seguente per rimuovere definitivamente il gruppo eliminato.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>Come si verifica che l'azione è riuscita?
Per verificare di aver ripristinato correttamente un gruppo di Office 365, eseguire il cmdlet `Get-AzureADGroup –ObjectId <objectId>` per visualizzare informazioni relative al gruppo. Al completamento della richiesta di ripristino:
- Il gruppo verrà visualizzato nella barra di navigazione a sinistra su Exchange
- In Planner verrà visualizzato il piano per il gruppo
- I siti di Sharepoint e tutti i contenuti saranno disponibili
- È possibile accedere al gruppo da qualsiasi endpoint di Exchange e da altri carichi di lavoro di Office 365 che supportano i gruppi di Office 365


## <a name="next-steps"></a>Passaggi successivi
Questi articoli contengono informazioni aggiuntive sui gruppi di Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)

