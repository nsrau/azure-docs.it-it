<properties
   pageTitle="Panoramica dei vari portali necessari per creare un'offerta per Marketplace | Microsoft Azure"
   description="Panoramica dei vari portali necessari per creare un'offerta per Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/27/2016"
   ms.author="hascipio" />


# Portali necessari
Prima di iniziare il processo di pubblicazione di un’offerta, è necessario fornire alcune informazioni sui diversi portali che saranno necessari. Di seguito sono riepilogati i portali (centro per sviluppatori, portale di pubblicazione di Azure e portale di Azure) nell'ordine in cui si interagirà con loro.
## Centro per sviluppatori
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)
### Descrizione
La creazione dell'account del Centro per sviluppatori Microsoft è un'attività una-tantum. Assicurarsi che la società non possieda già un account per il Centro per sviluppatori prima di tentare di crearne uno. Durante il processo, Microsoft raccoglie le informazioni sul conto bancario, i dati fiscali e le informazioni sull'indirizzo dell'azienda.

> [AZURE.NOTE] Se si intende pubblicare solo offerte gratuite (oppure offerte Bring Your Own License), le informazioni fiscali e bancarie non sono necessarie.

### Identità/account usato
Idealmente, questa è una lista di distribuzione o un gruppo di sicurezza (ad esempio azurepublishing@*partnercompany*.com). La lista di distribuzione o il gruppo di sicurezza **devono** essere registrati come un account Microsoft.

> [AZURE.TIP] È consigliabile usare una lista di distribuzione o un gruppo di sicurezza perché in questo modo si elimina la dipendenza da una singola persona. È comunque possibile usare anche un singolo account.

## Portale di pubblicazione
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### Descrizione
Questo è il portale per lavorare sull'offerta e pubblicarla (marketing, prezzi, pubblicazione, eventuale certificazione e così via).

### Identità/account usato
Il gruppo di protezione o l’elenco distribuzione precedente devono essere utilizzati per la prima volta che si accede al portale di pubblicazione. Successivamente, altri utenti possono essere aggiunti come coamministratori. In questo modo viene eseguita l'associazione ai dati di registrazione del Centro per sviluppatori.

## Portale di Azure
[https://portal.azure.com](https://portal.azure.com)
### Descrizione
Questo è il portale in cui è possibile visualizzare le proprie offerte in gestione temporanea e pubblicate in Azure Marketplace (applicabile per VM, modelli di soluzioni e servizi per gli sviluppatori basati su Gestione risorse di Azure)
### Identità/account usato
Durante la gestione temporanea dell'offerta dal portale di pubblicazione, deve essere consentito un ID di sottoscrizione. La stessa sottoscrizione (sono associati un nome utente e una password) deve essere usata durante l'accesso a questo portale per testare l'offerta in gestione temporanea.

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0803_2016-->