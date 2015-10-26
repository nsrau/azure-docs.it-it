<properties
   pageTitle="Distribuzione dell'offerta in Azure Marketplace | Microsoft Azure"
   description="Informazioni e istruzioni dettagliate per distribuire l'offerta (ad esempio, immagine di macchina virtuale, servizio per sviluppatori, servizio dati e così via) in Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="AzureStore"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Distribuzione dell'offerta in Azure Marketplace
Quando si è soddisfatti dell'offerta (ad esempio, è stato eseguito il testing degli scenari dei clienti, del contenuto marketing e così via) e si è pronti per il lancio, richiedere **Push in produzione** nella scheda **Pubblica**.

1.	Questi 4 passaggi nella pagina della procedura dettagliata devono essere completati e di colore verde.

  ![disegno][img-pubportal-walkthru-checked]

2. Selezionare la scheda **Pubblica** dall'elenco a sinistra.

  ![disegno][img-pubportal-menu-publish]

3. Dopo avere selezionato questa opzione, verrà visualizzata la pagina mostrata di seguito. Fare clic sull'opzione **Richiedi approvazione per push in produzione**. Una volta effettuata la richiesta, il team di approvazione esegue una verifica finale e quindi l'offerta sarà resa pubblicamente disponibile nel Marketplace. Lo stato dell'offerta verrà modificato in **"Elencata"**.

  ![disegno][img-pubportal-publish-pushproduction]

> [AZURE.IMPORTANT]È sempre possibile eliminare l'offerta mentre è in uno stato **"bozza"** (ovvero quando non è **push in gestione temporanea**, **push in produzione**). Nella scheda **Cronologia** fare clic su **Elimina bozza** nella parte inferiore della pagina per eliminare una bozza.

> Per le **immagini di macchina virtuale**, la replica tra i data center può richiedere fino a 24-48 ore. Una volta completata la replica, l'offerta verrà elencata in [Azure Marketplace](http://azure.microsoft.com/marketplace).

## Passaggi successivi
Una volta che l'offerta è pubblicata, testare gli scenari dei clienti per convalidare il corretto funzionamento di tutti i contratti e le funzionalità nell'ambiente di produzione, come testato e convalidato nell'ambiente di gestione temporanea.

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]: media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]: media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]: media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png

<!---HONumber=Oct15_HO3-->