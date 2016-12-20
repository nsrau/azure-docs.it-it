---
title: Pubblicare un&quot;app in Azure RemoteApp | Microsoft Docs
description: Informazioni su come pubblicare app e risorse in Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: c7e1a2cd-8e1f-4a33-bd43-8032ec9ac952
ms.service: remoteapp
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0b81ef8425e44a9813fc0220f0ab314a3110f265


---
# <a name="how-to-publish-an-app-in-remoteapp"></a>Come pubblicare un'app in RemoteApp
> [!IMPORTANT]
> Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Dopo aver creato la raccolta RemoteApp, è necessario pubblicare le app o le risorse che si desidera rendere disponibili agli utenti. Nelle immagini modello fornite con la sottoscrizione sono pubblicate solo alcune app per impostazione predefinita. Per condividere le altre app, è necessario pubblicarle.

> [!NOTE]
> È necessario aggiornare un'app? È prima necessario [aggiornare l'immagine](remoteapp-update.md) .
> 
> 

Nella scheda **Publishing** (Pubblicazione) del portale fare clic su **Publish** (Pubblica). È possibile aggiungere un'app dal menu **Start** dell'immagine modello oppure specificare il percorso in cui è installata l'app nell'immagine modello. Se si decide di aggiungere l'app dal menu **Start** , scegliere l'app da pubblicare dall'elenco. Se si decide di fornire il percorso dell'app, specificare il nome e il percorso dell'app. Usare le variabili nel percorso, ad esempio "%systemdrive%" anziché "c:\".

> [!NOTE]
> Se si vuole aggiungere l'app dal menu **Start**, è necessario aver *aggiunto l'app al menu **Start** nell'immagine modello.* In caso contrario, RemoteApp visualizzerà solo gli elementi *presenti* nel menu **Start**, creando confusione. 
> 
> Per assicurarsi che l'applicazione venga visualizzata nel menu **Start**, inserire un file di collegamento **.Ink** nella cartella %systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programmi.
> 
> Se si dimentica di aggiungere l'app al menu **Start** al momento della creazione del modello, aggiungere il percorso all'app (in alternativa, ricreare l'immagine modello, tuttavia questa procedura è piuttosto complicata).
> 
> 




<!--HONumber=Nov16_HO3-->


