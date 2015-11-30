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
   ms.date="10/05/2015"
   ms.author="hascipio" />


# Portali necessari
Prima di iniziare il processo di pubblicazione di un’offerta, è necessario fornire alcune informazioni sui diversi portali che saranno necessari. Di seguito sono riepilogati i portali (dashboard venditori, portale di pubblicazione e portale di anteprima di Azure) nell'ordine in cui si interagirà con loro.
## Dashboard venditori
[https://sellerdashboard.microsoft.com](https://sellerdashboard.microsoft.com)
### Descrizione
La creazione dell'account per il dashboard venditori è un'operazione che si esegue una sola volta. Assicurarsi che l'azienda non possieda già un account per il Dashboard venditori prima di tentare di crearne uno. Durante il processo, Microsoft raccoglie le informazioni sul conto bancario, i dati fiscali e le informazioni sull'indirizzo dell'azienda.

> [AZURE.NOTE]Se si intende pubblicare solo offerte gratuite (oppure offerte Bring Your Own License), le informazioni fiscali e bancarie non sono necessarie.

### Identità/account usato
Idealmente, questa è una lista di distribuzione o un gruppo di sicurezza (ad esempio, azurepublishing@*partnercompany*.com). La lista di distribuzione o il gruppo di sicurezza **devono** essere registrati come un account Microsoft.

> [AZURE.TIP]È consigliabile usare una lista di distribuzione o un gruppo di sicurezza perché in questo modo si elimina la dipendenza da una singola persona. È comunque possibile usare anche un singolo account.

## Portale di pubblicazione
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### Descrizione
Questo è il portale per lavorare sull'offerta e pubblicarla (marketing, prezzi, pubblicazione, eventuale certificazione e così via).

### Identità/account usato
Il gruppo di protezione o l’elenco distribuzione precedente devono essere utilizzati per la prima volta che si accede al portale di pubblicazione. Successivamente, altri utenti possono essere aggiunti come coamministratori. In questo modo viene eseguita l'associazione ai dati di registrazione del dashboard venditori.

## Portale di anteprima di Azure
[https://ms.portal.azure.com](https://ms.portal.azure.com)
### Descrizione
Questo è il portale in cui è possibile visualizzare le proprie offerte in gestione temporanea e pubblicate in Azure Marketplace (applicabile per VM, modelli di soluzioni e servizi per gli sviluppatori basati su Gestione risorse di Azure)
### Identità/account usato
Durante la gestione temporanea dell'offerta dal portale di pubblicazione, deve essere consentito un ID di sottoscrizione. La stessa sottoscrizione (sono associati un nome utente e una password) deve essere usata durante l'accesso a questo portale per testare l'offerta in gestione temporanea.

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO4-->