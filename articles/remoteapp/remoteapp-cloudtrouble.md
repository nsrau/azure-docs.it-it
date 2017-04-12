---
title: Risolvere i problemi delle raccolte cloud di RemoteApp - Creazione | Documentazione Microsoft
description: Informazioni su come risolvere i problemi relativi agli errori di creazione delle raccolte cloud di RemoteApp
services: remoteapp
documentationcenter: 
author: vkbucha
manager: mbaldwin
ms.assetid: 95eb7797-7b5e-4781-ad23-f15dd85b213d
ms.service: remoteapp
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 304ba7c5057b27c459bccbb75d3a711567757675
ms.lasthandoff: 03/31/2017


---
# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>Risoluzione dei problemi di creazione di raccolte di cloud di RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Se si riscontrano problemi durante la creazione di una raccolta di cloud, consultare le informazioni seguenti.

## <a name="your-image-is-invalid"></a>L'immagine non è valida
Se viene visualizzato un messaggio del tipo "GoldImageInvalid" quando si è in attesa di Azure per eseguire il provisioning per la raccolta, significa che l'immagine del modello non soddisfa i [requisiti definiti per l’immagine](remoteapp-imagereqs.md). Quindi, leggere quei [requisiti](remoteapp-imagereqs.md), correggere l'immagine e cercare di creare nuovamente la raccolta.

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Errori più frequenti visualizzati nel portale di gestione di Azure:
    DNS server could not be reached
    ProvisioningTimeout

La creazione di raccolte cloud spesso non riesce perché si usano immagini personalizzate.  Se si verifica uno degli errori precedenti e si usa un'immagine personalizzata per creare la raccolta, verificare quanto segue:

* Assicurarsi che l'immagine personalizzata che è stata caricata soddisfi i requisiti relativi all'immagine.
* Molto spesso il problema risiede nel fatto che l'immagine non è stata preparata correttamente con Sysprep.  
* Verificare l'immagine di avvio all'interno di Hyper-V o provare a creare una VM IAAS direttamente nella sottoscrizione di Azure usando l'immagine. Gli errori nell'avvio della macchina virtuale indicano, in genere, che l'immagine personalizzata non è stata preparata correttamente ed è necessario correggerla.  Verificare che l'immagine personalizzata sia stata creata seguendo la procedura per la creazione di un'immagine modello personalizzata per RemoteApp

Se si usa una delle immagini Microsoft incluse nella sottoscrizione, provare a creare nuovamente la raccolta. Se il problema persiste, contattare il supporto Microsoft.

    PlatformImageTrialModeOnly

Se viene visualizzato questo errore in genere significa che è stato effettuato l'aggiornamento a un account a pagamento, ma si sta tentando di usare un'immagine fornita da Microsoft che è valida solo durante la modalità di valutazione del servizio. In questo caso, provare a creare nuovamente la raccolta cloud, ma assicurarsi di specificare l'immagine corretta.


