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
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903692"
---
Tuttavia, se si vuole condividere immagini all'esterno del tenant di Azure, è necessario creare una registrazione dell'app per semplificare la condivisione.  L'uso di una registrazione dell'app può consentire scenari di condivisione più complessi, ad esempio: 

* Gestione delle immagini condivise quando un'azienda acquisisce un altro e l'infrastruttura di Azure viene distribuita tra tenant distinti. 
* I partner di Azure gestiscono l'infrastruttura di Azure per conto dei clienti. La personalizzazione delle immagini viene eseguita nel tenant partner, ma le distribuzioni dell'infrastruttura si verificheranno nel tenant del cliente. 


## <a name="create-the-app-registration"></a>Creare la registrazione dell'app

Creare una registrazione dell'applicazione che verrà usata da entrambi i tenant per condividere le risorse della raccolta immagini.
1. Aprire il [registrazioni app (anteprima) nel portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Selezionare **nuova registrazione** dal menu nella parte superiore della pagina.
1. In **nome**digitare *myGalleryApp*.
1. In **tipi di account supportati**selezionare **account in qualsiasi directory organizzativa e account Microsoft personali**.
1. In **URI di reindirizzamento**Digitare *https://www.microsoft.com* e quindi selezionare **registra**. Dopo la creazione della registrazione dell'app, viene visualizzata la pagina panoramica.
1. Nella pagina Panoramica copiare l'ID dell' **applicazione (client)** e salvarlo per usarlo in seguito.   
1. Selezionare **certificati & segreti**, quindi selezionare **nuovo segreto client**.
1. In **Descrizione**Digitare *raccolta immagini condivisa chiave privata app multi-tenant*.
1. In **scade**, lasciare l'impostazione predefinita di **in 1 anno** e quindi selezionare **Aggiungi**.
1. Copiare il valore del segreto e salvarlo in una posizione sicura. Non è possibile recuperarlo dopo aver lasciato la pagina.


Concedere all'app l'autorizzazione di registrazione per usare la raccolta di immagini condivise.
1. Nella portale di Azure selezionare la raccolta di immagini condivise che si vuole condividere con un altro tenant.
1. Selezionare **Seleziona controllo di accesso (IAM)** e in **Aggiungi assegnazione ruolo** Selezionare *Aggiungi*. 
1. In **ruolo**selezionare **Reader**.
1. In **assegna accesso a:** lasciare questo nome **Azure ad utente, gruppo o entità servizio**.
1. In **Seleziona**digitare *myGalleryApp* e selezionarlo quando viene visualizzato nell'elenco. Al termine, selezionare **Salva**.


## <a name="give-tenant-2-access"></a>Concedere l'accesso al tenant 2

Concedere all'applicazione l'accesso al tenant 2 richiedendo un accesso usando un browser. Sostituire *\<ID Tenant2 >* con l'ID tenant per il tenant con cui si vuole condividere la raccolta immagini. Sostituire *\<ID applicazione (client) >* con l'ID applicazione della registrazione dell'app creata. Al termine delle operazioni di sostituzione, incollare l'URL in un browser e seguire le istruzioni di accesso per accedere al tenant 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Nella [portale di Azure](https://portal.azure.com) accedere come tenant 2 e concedere all'app la registrazione per il gruppo di risorse in cui si vuole creare la macchina virtuale.

1. Selezionare il gruppo di risorse e quindi selezionare **controllo di accesso (IAM)** . In **Aggiungi assegnazione ruolo** selezionare **Aggiungi**. 
1. In **ruolo**digitare **collaboratore**.
1. In **assegna accesso a:** lasciare questo nome **Azure ad utente, gruppo o entità servizio**.
1. In **Seleziona** tipo *myGalleryApp* quindi selezionarlo quando viene visualizzato nell'elenco. Al termine, selezionare **Salva**.

> [!NOTE]
> È necessario attendere che la versione dell'immagine completi la compilazione e la replica prima di poter usare la stessa immagine gestita per creare un'altra versione dell'immagine.

