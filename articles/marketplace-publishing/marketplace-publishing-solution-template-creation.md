<properties
   pageTitle="Guida alla creazione di un modello di soluzione per il Marketplace | Microsoft Azure"
   description="Istruzioni dettagliate su come creare, certificare e distribuire un modello di soluzione con un'immagine con più macchine virtuali per l'acquisto in Azure Marketplace."
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
      ms.date="04/29/2016"
      ms.author="hascipio; v-divte" />

# Guida alla creazione di un modello di soluzione per Azure Marketplace
Dopo aver completato il passaggio 1, [Creazione e registrazione dell'account][link-acct-creation], sono state fornite indicazioni per la creazione di un modello di soluzione compatibile con Azure in [Prerequisiti tecnici per la creazione di un modello di soluzione](marketplace-publishing-solution-template-creation-prerequisites.md). Ora verranno illustrati i passaggi per la creazione di un modello di soluzione per più macchine virtuali sul [portale di pubblicazione][link-pubportal] per Azure Marketplace.

## Creare l'offerta di modello di soluzione nel portale di pubblicazione
Passare alla pagina [https://publish.windowsazure.com](http://publish.windowsazure.com). Quando si accede per la prima volta al [portale di pubblicazione](https://publish.windowsazure.com/), usare lo stesso account con cui è stato registrato il profilo venditore dell'azienda. Successivamente è possibile aggiungere qualsiasi dipendente dell'azienda come coamministratore nel portale di pubblicazione.

### 1\. Selezionare "Modelli di soluzione"

  ![disegno][img-pubportal-menu-sol-templ]

### 2\. Creare un nuovo modello di soluzione

  ![disegno][img-pubportal-sol-templ-new]

### 3\. Iniziare con le topologie
Un modello di soluzione è un elemento padre per tutte le relative topologie. È possibile definire più topologie in un singolo modello di soluzione/offerta. Quando un'offerta passa alla fase di gestione temporanea, passano a tale fase anche tutte le relative topologie. Per definire l'offerta, eseguire i passaggi seguenti:
- Creare una topologia. "Identificatore topologia" è in genere il nome della topologia per il modello di soluzione. L'identificatore topologia viene usato nell'URL come illustrato di seguito:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Portale di Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}

- Aggiungere una nuova versione

### 4\. Ottenere la certificazione per le versioni della topologia
Caricare un file ZIP contenente tutti i file necessari per eseguire il provisioning della specifica versione della topologia. Il file zip deve contenere quanto segue:
- File *mainTemplate.json* e *createUiDefinition.json* nella directory radice.
- Tutti i modelli collegati e tutti gli script necessari.

Dopo aver caricato il file ZIP, fare clic su **Richiedi certificazione**. Il team di certificazione Microsoft esaminerà i file e certificherà la topologia.

  > [AZURE.TIP] Mentre gli sviluppatori lavorano alla creazione delle topologie del modello di soluzione e alla relativa certificazione, il reparto commerciale, marketing e/o legale dell'azienda può occuparsi dei contenuti di marketing e legali.

## Passaggi successivi
Dopo aver creato il modello di soluzione e inviato il file ZIP con i file richiesti per la certificazione, è possibile proseguire con le istruzioni della [Guida ai contenuti marketing nel Marketplace](marketplace-publishing-push-to-staging.md) prima di preparare l'offerta per il test in gestione temporanea. In alternativa, per visualizzare il set completo di articoli di pubblicazione nel Marketplace, vedere [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md).

Articoli correlati:

- Immagini di macchina virtuale: [Informazioni sulle immagini di macchine virtuali in Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

- Estensioni di macchina virtuale: [Panoramica sull'agente VM e sulle estensioni VM](https://msdn.microsoft.com/library/azure/dn832621.aspx) e [Estensioni VM e funzionalità di Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)

- Gestione risorse di Azure: [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md) e [semplici esempi di modelli ARM](https://github.com/rjmax/ArmExamples)

- Limitazioni degli account di archiviazione: [Post di blog relativo al monitoraggio della limitazione degli account di archiviazione](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) e [Archiviazione Premium](../storage/storage-premium-storage.md#scalability-and-performance-targets-whit-ITing-premium-storage)

[img-pubportal-menu-sol-templ]: media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]: media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=AcomDC_0504_2016-->