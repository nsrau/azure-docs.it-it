---
title: Aggiornare la raccolta di Azure RemoteApp | Microsoft Docs
description: Informazioni su come aggiornare la raccolta di Azure RemoteApp
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: e553d432-e581-48fe-b996-c432357eb64a
ms.service: remoteapp
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: compute
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 41c8a13ebd008ed4f9d8a5399bf8e272bf0fd7b2
ms.lasthandoff: 03/31/2017


---
# <a name="update-a-collection-in-azure-remoteapp"></a>Aggiornare una raccolta in Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Ad un certo punto sarà necessario, inevitabilmente, aggiornare le app o l’immagine nella raccolta di Azure RemoteApp. Se si usa una delle immagini incluse nella sottoscrizione di Azure RemoteApp, in una raccolta cloud o ibrida, tutti gli aggiornamenti vengono gestiti automaticamente da Azure RemoteApp.

Tuttavia, se si utilizza un'immagine personalizzata (che è stata compilata da zero o creata modificando une delle immagini disponibili), si è responsabili della manutenzione dell'immagine e delle app. Se è necessario aggiornare l'immagine o una qualsiasi delle app all'interno, è necessario creare una nuova versione aggiornata dell'immagine e quindi sostituire l'immagine esistente nella raccolta con la nuova immagine aggiornata.

Quindi, come si esegue l'aggiornamento della raccolta? È piuttosto semplice:

1. Aggiornare l'immagine utilizzata nella raccolta. Applicare le patch o gli aggiornamenti necessari e quindi salvarla con un nuovo nome.
2. [Caricare](remoteapp-uploadimage.md) o [importare](remoteapp-image-on-azurevm.md) l'immagine in RemoteApp.
3. A questo punto, nella pagina della raccolta, fare clic su **Aggiorna**.
4. Scegliere la nuova immagine dall’elenco **Immagine modello** .
5. Questa è la parte più complicata, è necessario decidere come gestire gli utenti che utilizzano attualmente un'app della raccolta. L'utente ha a disposizione le seguenti opzioni:
   
   * **Concedere agli utenti 60 minuti dopo l'aggiornamento**. Non appena completato l'aggiornamento, in Azure RemoteApp viene visualizzato un messaggio con cui viene richiesto a tutti gli utenti attivi di salvare il lavoro, disconnettersi e accedere di nuovo. Dopo 60 minuti, tutti gli utenti attivi che non si sono disconnessi vengono disconnessi automaticamente. Gli utenti possono accedere di nuovo immediatamente.
   * **Disconnettere gli utenti immediatamente**. Non appena completato l'aggiornamento, disconnettere tutti gli utenti automaticamente senza alcun avviso. Se si sceglie questa opzione, gli utenti potrebbero perdere i dati. Tuttavia, possano riconnettersi all'app immediatamente.
6. Fare clic sul segno di spunta per avviare l'aggiornamento.


