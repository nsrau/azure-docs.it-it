---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 499aeccdf00980eeb66ac6ee06e45267fd515143
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180111"
---
Raccolte di immagini condivise consentono di immagini di condivisione tramite RBAC. È possibile usare RBAC per condividere le immagini all'interno del tenant e anche per singoli utenti all'esterno del tenant. Tuttavia, se si vuole condividere immagini di fuori del tenant di Azure, su larga scala, è consigliabile creare una registrazione dell'app per facilitare la condivisione.  Tramite la registrazione di un'app è possibile abilitare la condivisione di scenari più complessa, ad esempio: 

* Gestione condivise immagini quando un'azienda ne acquisisce un'altra, e l'infrastruttura di Azure viene distribuito tra separare i tenant. 
* Partner di Azure la gestione dell'infrastruttura di Azure per conto dei clienti. Personalizzazione delle immagini viene eseguita all'interno del tenant di partner, ma le distribuzioni di infrastrutture avviene nel tenant del cliente. 


## <a name="create-the-app-registration"></a>Creare la registrazione dell'app

Creare la registrazione di un'applicazione che verrà usata da entrambi i tenant per condividere le risorse della raccolta immagini.
1. Aprire il [registrazioni per l'App (anteprima) nel portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Selezionare **nuova registrazione** dal menu nella parte superiore della pagina.
1. Nelle **Name**, digitare *myGalleryApp*.
1. Nelle **tipi di account supportati**, selezionare **gli account in qualsiasi directory dell'organizzazione e gli account Microsoft personali**.
1. Nelle **URI di reindirizzamento**, digitare *https://www.microsoft.com* e quindi selezionare **registrare**. Dopo aver creata la registrazione dell'app, verrà aperta la pagina di panoramica.
1. Nella pagina Panoramica, copiare il **ID applicazione (client)** e salvarli per un uso in un secondo momento.   
1. Selezionare **certificati e i segreti**, quindi selezionare **nuovo segreto client**.
1. Nelle **Description**, digitare *segreto di app cross-tenant condiviso immagine della raccolta*.
1. Nelle **Expires**, lasciare il valore predefinito **tra 1 anno** e quindi selezionare **Add**.
1. Copiare il valore del segreto e salvarlo in un luogo sicuro. È possibile recuperarlo dopo aver chiuso la pagina.


Concedere l'autorizzazione di registrazione di app per usare la raccolta di immagini condivise.
1. Nel portale di Azure, selezionare la raccolta di immagini condivise che si desidera condividere con un altro tenant.
1. Selezionare **selezionare il controllo di accesso (IAM)** , quindi in **aggiungere un'assegnazione di ruolo** seleziona *Add*. 
1. Sotto **ruolo**, selezionare **lettore**.
1. Sotto **assegna accesso a:** , non modificare **utente, gruppo o entità servizio Azure AD**.
1. Sotto **selezionate**, digitare *myGalleryApp* e selezionarlo quando viene visualizzato nell'elenco. Al termine, selezionare **salvare**.


## <a name="give-tenant-2-access"></a>Concedere l'accesso Tenant 2

Concedere l'accesso Tenant 2 per l'applicazione richiede un accesso aggiuntivo usando un browser. Sostituire *<Tenant2 ID>* con l'ID tenant per il tenant di cui si desidera condividere con la raccolta di immagini. Sostituire *< ID applicazione (client) >* con l'ID applicazione di registrazione dell'app è stato creato. Dopo aver eseguito le sostituzioni, incollare l'URL in un browser e seguire le istruzioni di accesso per accedere al Tenant 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Nel [portale di Azure](https://portal.azure.com) accedere come Tenant 2 e concedere l'accesso alla registrazione dell'app al gruppo di risorse in cui si desidera creare la macchina virtuale.

1. Selezionare il gruppo di risorse e quindi selezionare **controllo di accesso (IAM)** . Sotto **aggiungere un'assegnazione di ruolo** selezionate **Add**. 
1. Sotto **ruolo**, digitare **collaboratore**.
1. Sotto **assegna accesso a:** , non modificare **utente, gruppo o entità servizio Azure AD**.
1. Sotto **selezionate** tipo *myGalleryApp* quindi selezionarlo quando viene visualizzato nell'elenco. Al termine, selezionare **salvare**.

> [!NOTE]
> È necessario attendere che la versione dell'immagine terminare completamente corso di compilazione e replicati prima di poter usare la stessa immagine gestita per creare un'altra versione dell'immagine.

