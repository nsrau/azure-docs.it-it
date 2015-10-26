<properties
   pageTitle="Aggiornare la raccolta di Azure RemoteApp | Microsoft Azure"
   description="Informazioni su come aggiornare la raccolta di Azure RemoteApp"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="09/03/2015"
   ms.author="elizapo"/>

# Aggiornare una raccolta in Azure RemoteApp

Ad un certo punto sarà necessario, inevitabilmente, aggiornare le app o l’immagine nella raccolta di Azure RemoteApp. Se si utilizza una delle immagini incluse nella sottoscrizione di Azure RemoteApp, in una raccolta cloud o ibrida, tutti gli aggiornamenti sono gestiti da Azure RemoteApp.

Tuttavia, se si utilizza un'immagine personalizzata (che è stata compilata da zero o creata modificando une delle immagini disponibili), si è responsabili della manutenzione dell'immagine e delle app.

Quindi, come si esegue l'aggiornamento della raccolta? È piuttosto semplice:

1. Aggiornare l'immagine utilizzata nella raccolta. Applicare le patch o gli aggiornamenti necessari e quindi salvarla con un nuovo nome.
2. [Caricare](remoteapp-uploadimage.md) o [importare](remoteapp-image-on-azurevm.md) l’immagine in RemoteApp.
3. A questo punto, nella pagina della raccolta, fare clic su **Aggiorna**.
4. Scegliere la nuova immagine dall’elenco **Immagine modello**.
4. Questa è la parte più complicata, è necessario decidere come gestire gli utenti che utilizzano attualmente un'app della raccolta. L'utente ha a disposizione le seguenti opzioni:
	- **Concedere agli utenti 60 minuti dopo l'aggiornamento**. Non appena completato l'aggiornamento, in Azure RemoteApp viene visualizzato un messaggio con cui viene richiesto a tutti gli utenti attivi di salvare il lavoro, disconnettersi e accedere di nuovo. Dopo 60 minuti, tutti gli utenti attivi che non si sono disconnessi vengono disconnessi automaticamente. Gli utenti possono accedere di nuovo immediatamente. 
	- **Disconnettere gli utenti immediatamente**. Non appena completato l'aggiornamento, disconnettere tutti gli utenti automaticamente senza alcun avviso. Se si sceglie questa opzione, gli utenti potrebbero perdere i dati. Tuttavia, possano riconnettersi all'app immediatamente.

1. Fare clic sul segno di spunta per avviare l'aggiornamento.

 

<!---HONumber=Oct15_HO3-->