<properties 
   pageTitle="Livello Ricerca di Azure (Modelli di architettura di Azure)" 
   description="Il modello Livello Ricerca di Azure è incluso nell'area Fondamenta, descritta in modo dettagliato nella documentazione sull'architettura di CPIF." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# Livello Ricerca di Azure (Modelli di architettura di Azure)

Il [Framework di integrazione piattaforma cloud (CPIF, Cloud Platform Integration Framework)](azure-architectures-cpif-overview.md) fornisce materiale sussidiario sull'integrazione dei carichi di lavoro per il caricamento di applicazioni in una soluzione cloud Microsoft.

Il CPIF descrive il modo in cui le organizzazioni, i clienti e i partner devono progettare e distribuire i carichi di lavoro destinati al cloud usando la piattaforma cloud ibrida e le capacità di gestione di Azure, System Center e Windows Server.

Il modello **Livello Ricerca di Azure** è incluso nell'area **Fondamenta**, descritta in modo dettagliato nella documentazione sull'architettura di CPIF.

##  Livello Ricerca di Azure

Il modello di progettazione Livello Ricerca di Azure include informazioni dettagliate sulle funzionalità e sui servizi di Azure necessari per offrire servizi di ricerca che possono fornire prestazioni prevedibili e disponibilità elevata oltre i limiti geografici e offre un modello architettonico per l'uso di Ricerca di Azure per rendere disponibile una soluzione di ricerca. Ricerca di Azure è una funzionalità di "ricerca come servizio" inclusa in Microsoft Azure che permette agli sviluppatori di incorporare capacità di ricerca nelle applicazioni senza dovere distribuire, gestire o mantenere servizi di infrastruttura per fornire questa capacità alle applicazioni. Lo scopo di questo modello consiste nel fornire una soluzione ripetibile destinata all'uso in diverse situazioni e progettazioni.

## Panoramica del modello architettonico 

Questo documento descrive un modello di base per l'uso di Ricerca di Azure con due variazioni per illustrare la flessibilità architettonica del servizio. Il modello di base è costituito da Ricerca di Azure e dei servizi di Azure correlati ed è stato progettato per fornire materiale sussidiario per la creazione di progetti end-to-end. Le variazioni del modello, in particolare i modelli Servizio condiviso e Concorrenza, sono incluse in questa sezione per fornire materiale sussidiario in base ai diversi requisiti, ai diversi Contratti di servizio e ad altre condizioni specifiche.

##  Risorse aggiuntive
[Livello Ricerca di Azure (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

## Vedere anche
[Architettura di CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Livello Web globale con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Livello Dati con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Rete ibrida](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Livello Elaborazione batch](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=July15_HO1-->