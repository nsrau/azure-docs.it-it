<properties
    pageTitle="Pubblicare un'app in Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come pubblicare app e risorse in Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2015"
    ms.author="elizapo" />


# Come pubblicare un'app in RemoteApp

Dopo aver creato la raccolta RemoteApp, è necessario pubblicare le app o le risorse che si desidera rendere disponibili agli utenti. Nelle immagini modello fornite con la sottoscrizione sono pubblicate solo alcune app per impostazione predefinita. Per condividere le altre app, è necessario pubblicarle.

> [AZURE.NOTE]È necessario aggiornare un'app? È prima necessario [aggiornare l'immagine](remoteapp-update.md).

Nella scheda **Pubblicazione** del portale fare clic su **Pubblica**. È possibile aggiungere un'app dal menu **Start** dell'immagine modello oppure specificare il percorso in cui è installata l'app nell'immagine modello. Se si decide di aggiungere l'app dal menu **Start**, scegliere l'app da pubblicare dall'elenco. Se si decide di fornire il percorso dell'app, specificare il nome e il percorso dell'app. Usare le variabili nel percorso, ad esempio "%systemdrive%" anziché "c:".

> [AZURE.NOTE]Se si vuole aggiungere l'app dal menu **Start**, è necessario aver *aggiunto l'app al menu **Start** nell'immagine modello.* In caso contrario, RemoteApp visualizzerà solo gli elementi *presenti* nel menu **Start**, creando confusione.

>Per assicurarsi che l’applicazione venga visualizzata nel menu **Start**, inserire un file di collegamento **.Ink** nella cartella %systemdrive%\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programmi.

> Se si dimentica di aggiungere l'app al menu **Start** al momento della creazione del modello, aggiungere il percorso all'app (in alternativa, ricreare l'immagine modello, tuttavia questa procedura è piuttosto complicata).


 

<!---HONumber=Sept15_HO3-->