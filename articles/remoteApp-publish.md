<properties title="Publish an app in RemoteApp" pageTitle="Pubblicare un'app in RemoteApp" description="Informazioni su come pubblicare applicazioni e risorse in RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="elizapo" />

#Come pubblicare un'app in RemoteApp

Dopo aver creato la raccolta cloud o ibrida, è necessario pubblicare le app o le risorse che si desidera rendere disponibili agli utenti. Nelle immagini modello fornite con la sottoscrizione sono pubblicate solo alcune app per impostazione predefinita. Per condividere le altre app, è necessario pubblicarle.
 
Nella scheda **Pubblicazione** nel portale fare clic su **Pubblica**. È possibile aggiungere un'app dal menu Start dell'immagine modello oppure specificare il percorso in cui è installata l'app nell'immagine modello. Se si decide di aggiungere l'app dal menu Start, scegliere l'app da pubblicare nell'elenco. Se si decide di fornire il percorso dell'app, specificare il nome e il percorso dell'app. Usare le variabili nel percorso, ad esempio "%systemdrive%" anziché "c:\".

**Nota:** se si vuole aggiungere l'app dal menu Start, è necessario avere *aggiunto tale app nel menu Start dell'immagine modello*. In caso contrario, RemoteApp vedrà solo ciò che *è presente* nel menu Start e si confonderà. Se si è dimenticato di aggiungere l'app al menu Start durante la creazione del modello, scegliere di aggiungere il percorso dell'app (oppure ricreare l'immagine modello, ma se si tratta di una procedura più laboriosa).

<!--HONumber=35.2-->
