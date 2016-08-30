
<properties
    pageTitle="Risolvere i problemi delle raccolte cloud di RemoteApp - Creazione | Microsoft Azure"
    description="Informazioni su come risolvere i problemi relativi agli errori di creazione delle raccolte cloud di RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# Risoluzione dei problemi di creazione di raccolte di cloud di RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l'[annuncio](https://go.microsoft.com/fwlink/?linkid=821148).

Se si riscontrano problemi durante la creazione di una raccolta di cloud, consultare le informazioni seguenti.

## L'immagine non è valida ##
Se viene visualizzato un messaggio del tipo "GoldImageInvalid" quando si è in attesa di Azure per eseguire il provisioning per la raccolta, significa che l'immagine del modello non soddisfa i [requisiti definiti per l’immagine](remoteapp-imagereqs.md). Quindi, leggere quei [requisiti](remoteapp-imagereqs.md), correggere l'immagine e cercare di creare nuovamente la raccolta.

## Errori più frequenti visualizzati nel portale di gestione di Azure:

	DNS server could not be reached
	ProvisioningTimeout

La creazione di raccolte cloud spesso non riesce perché si usano immagini personalizzate. Se si verifica uno degli errori precedenti e si usa un'immagine personalizzata per creare la raccolta, verificare quanto segue:

- Assicurarsi che l'immagine personalizzata che è stata caricata soddisfi i requisiti relativi all'immagine.
- Molto spesso il problema risiede nel fatto che l'immagine non è stata preparata correttamente con Sysprep.
- Verificare l'immagine di avvio all'interno di Hyper-V o provare a creare una VM IAAS direttamente nella sottoscrizione di Azure usando l'immagine. Gli errori nell'avvio della macchina virtuale indicano, in genere, che l'immagine personalizzata non è stata preparata correttamente ed è necessario correggerla. Verificare che l'immagine personalizzata sia stata creata seguendo la procedura per la creazione di un'immagine modello personalizzata per RemoteApp

Se si usa una delle immagini Microsoft incluse nella sottoscrizione, provare a creare nuovamente la raccolta. Se il problema persiste, contattare il supporto Microsoft.

	PlatformImageTrialModeOnly

Se viene visualizzato questo errore in genere significa che è stato effettuato l'aggiornamento a un account a pagamento, ma si sta tentando di usare un'immagine fornita da Microsoft che è valida solo durante la modalità di valutazione del servizio. In questo caso, provare a creare nuovamente la raccolta cloud, ma assicurarsi di specificare l'immagine corretta.

<!---HONumber=AcomDC_0817_2016-->