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
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903692"
---
Tuttavia, se si desidera condividere immagini all'esterno del tenant di Azure, su larga scala, è necessario creare una registrazione dell'app per facilitare la condivisione.  L'uso della registrazione di un'app può abilitare scenari di condivisione più complessi, ad esempio:Using an app registration can enable more complex sharing scenarios, like: 

* Gestione delle immagini condivise quando un'azienda ne acquisisce un'altra e l'infrastruttura di Azure viene distribuita tra tenant separati. 
* I partner di Azure gestiscono l'infrastruttura di Azure per conto dei clienti. La personalizzazione delle immagini viene eseguita all'interno del tenant dei partner, ma le distribuzioni dell'infrastruttura verranno eseguite nel tenant del cliente. 


## <a name="create-the-app-registration"></a>Creare la registrazione dell'appCreate the app registration

Creare una registrazione dell'applicazione che verrà utilizzata da entrambi i tenant per condividere le risorse della raccolta immagini.
1. Aprire [le registrazioni dell'app (anteprima) nel portale](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)di Azure.    
1. Seleziona **Nuova registrazione** dal menu nella parte superiore della pagina.
1. In **Nome**digitare *myGalleryApp*.
1. In **Tipi di account supportati**selezionare Account in qualsiasi directory **dell'organizzazione e account Microsoft personali**.
1. In URI di *https://www.microsoft.com* **reindirizzamento**digitare e selezionare **Registra**. Dopo aver creato la registrazione dell'app, si aprirà la pagina di panoramica.
1. Nella pagina di panoramica copiare **l'ID applicazione (client)** e salvarla per utilizzarla in un secondo momento.   
1. Selezionare **Certificati & segreti**, quindi Nuovo **segreto client**.
1. In **Descrizione**digitare *Segreto app cross-tenant*per la raccolta di immagini condivise .
1. In **Scadenza**lasciare il valore predefinito **In 1 anno** e quindi selezionare **Aggiungi**.
1. Copiare il valore del segreto e salvarlo in un luogo sicuro. Non è possibile recuperarlo dopo aver lasciato la pagina.


Concedere all'app l'autorizzazione di registrazione per utilizzare la raccolta di immagini condivise.
1. Nel portale di Azure selezionare la raccolta di immagini condivise che si vuole condividere con un altro tenant.
1. Selezionare **Selezionare Controllo di accesso (IAM)** e in **Aggiungi assegnazione ruolo** selezionare *Aggiungi*. 
1. In **Ruolo**selezionare **Lettore**.
1. In **Assegna accesso a:** lasciare questo nome **utente, gruppo o entità servizio**di Azure AD.
1. In **Seleziona**digitare *myGalleryApp* e selezionarla quando viene visualizzata nell'elenco. Al termine, selezionare **Salva**.


## <a name="give-tenant-2-access"></a>Concedere l'accesso al tenant 2

Concedere a Tenant 2 l'accesso all'applicazione richiedendo un accesso tramite un browser. Sostituire * \<Tenant2 ID>* con l'ID tenant per il tenant con cui si vuole condividere la raccolta immagini. Sostituire * \<il>ID applicazione (client)* con l'ID applicazione della registrazione dell'app creata. Al termine delle sostituzioni, incollare l'URL in un browser e seguire le istruzioni di accesso per accedere al tenant 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Nel [portale](https://portal.azure.com) di Azure accedere come tenant 2 e concedere alla registrazione dell'app l'accesso al gruppo di risorse in cui si vuole creare la macchina virtuale.

1. Selezionare il gruppo di risorse e quindi selezionare **Controllo di accesso (IAM)**. In **Aggiungi assegnazione ruolo** selezionare **Aggiungi**. 
1. In **Ruolo**digitare **Collaboratore**.
1. In **Assegna accesso a:** lasciare questo nome **utente, gruppo o entità servizio**di Azure AD.
1. In **Seleziona digitare** *myGalleryApp,* quindi selezionarla quando viene visualizzata nell'elenco. Al termine, selezionare **Salva**.

> [!NOTE]
> È necessario attendere che la versione dell'immagine termini completamente la compilazione e la replica prima di poter utilizzare la stessa immagine gestita per creare un'altra versione dell'immagine.

