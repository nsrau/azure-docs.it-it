<properties 
   pageTitle="Livello Dati multisito (Modelli di architettura di Azure)" 
   description="Il modello Livello Dati multisito è incluso nell'area Fondamenta, descritta in modo dettagliato nella documentazione sull'architettura CPIF." 
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

# Livello Dati multisito (Modelli di architettura di Azure)

Il [Framework di integrazione piattaforma cloud (CPIF, Cloud Platform Integration Framework)](azure-architectures-cpif-overview.md) fornisce materiale sussidiario sull'integrazione dei carichi di lavoro per il caricamento di applicazioni in una soluzione cloud Microsoft.

Il CPIF descrive il modo in cui le organizzazioni, i clienti e i partner devono progettare e distribuire i carichi di lavoro destinati al cloud usando la piattaforma cloud ibrida e le capacità di gestione di Azure, System Center e Windows Server.

Il modello **Livello Dati multisito** è incluso nell'area **Fondamenta**, descritta in modo dettagliato nella documentazione sull'architettura CPIF.

## Livello Dati multisito

Il modello di progettazione Livello Dati multisito fornisce informazioni dettagliate sulle funzionalità e sui servizi di Azure necessari per offrire i servizi di livello dati in grado di fornire prestazioni prevedibili e disponibilità elevata oltre i limiti geografici. Per le finalità di questo modello di progettazione, un livello dati è definito come un livello di servizio che fornisce servizi della piattaforma dati tradizionali in modo isolato o come parte di un'applicazione a più livelli. In questo modello il bilanciamento del carico del livello dati viene fornito sia localmente all'interno dell'area geografica che in aree diverse.

Introdotti con SQL Server 2012, i gruppi di disponibilità AlwaysOn sono una funzionalità a disponibilità elevata e per il ripristino di emergenza completamente supportata nei servizi di infrastruttura di Azure. Informazioni dettagliate e l'annuncio ufficiale del supporto per i gruppi di disponibilità AlwaysOn nei servizi di infrastruttura di Microsoft Azure sono disponibili nell'articolo relativo ai gruppi di disponibilità AlwaysOn.

Questo documento fornisce una panoramica architettonica di un Livello dati multisito in Azure usando i gruppi di disponibilità AlwaysOn di SQL, con un nodo secondario di sola lettura facoltativo in un data center aggiuntivo di Azure per offrire funzionalità aggiuntive e ripristino di emergenza. L'uso di AlwaysOn di SQL in Azure offre un livello dati a disponibilità elevata che può essere usato da livelli Web o livelli applicazione.

Questo documento è incentrato sui modelli architettonici e sulle procedure consigliate. Materiale sussidiario completo sullo sviluppo è disponibile nelle esercitazioni ufficiali, che illustrano la configurazione dei gruppi di disponibilità AlwaysOn in Azure e la configurazione del listener del gruppo di disponibilità AlwaysOn.

## Panoramica del modello architettonico 

Questo documento descrive un modello per fornire l'accesso a contenuti Microsoft SQL Server in più aree geografiche per ottenere disponibilità e ridondanza. I servizi critici sono illustrati di seguito, senza riferimenti all'applicazione o al livello Web che accederà ai dati stessi. Il diagramma seguente è una semplice illustrazione dei servizi rilevanti e del modo in cui vengono usati come parte di questo modello.

Ogni area principale del servizio è illustrata in modo più dettagliato nel diagramma.
 
![Parte relativa ai tag nella risorsa e nei pannelli relativi ai gruppi di risorse](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  Risorse aggiuntive
[Livello Dati con carico bilanciato (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## Vedere anche
[Architettura di CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Livello Web globale con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Rete ibrida](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Livello Ricerca di Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[Livello Elaborazione batch](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=July15_HO3-->