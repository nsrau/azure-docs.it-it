<properties 
   pageTitle="Livello Web globale con carico bilanciato (Modelli di architettura di Azure)" 
   description="Il modello Livello Web globale con carico bilanciato è incluso nell'area Fondamenta, descritta in modo dettagliato nella documentazione sull'architettura CPIF." 
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

# Livello Web globale con carico bilanciato (Modelli di architettura di Azure)

Il [Framework di integrazione piattaforma cloud (CPIF, Cloud Platform Integration Framework)](azure-architectures-cpif-overview.md) fornisce materiale sussidiario sull'integrazione dei carichi di lavoro per il caricamento di applicazioni in una soluzione cloud Microsoft.

Il CPIF descrive il modo in cui le organizzazioni, i clienti e i partner devono progettare e distribuire i carichi di lavoro destinati al cloud usando la piattaforma cloud ibrida e le capacità di gestione di Azure, System Center e Windows Server.

Il modello **Livello Web globale con carico bilanciato** è incluso nell'area **Fondamenta**, descritta in modo dettagliato nella documentazione sull'architettura CPIF.

##  Livello Web globale con carico bilanciato

Il modello di progettazione Livello Web globale con carico bilanciato fornisce informazioni dettagliate sulle funzionalità e sui servizi di Azure necessari per offrire i servizi di livello Web in grado di fornire prestazioni prevedibili e disponibilità elevata oltre i limiti geografici.

Per le finalità di questo modello di progettazione, un livello Web è definito come un livello di servizio che fornisce contenuti HTTP/HTTPS o servizi dell'applicazione tradizionali in modo isolato o come parte di un'applicazione Web a più livelli. In questo modello il bilanciamento del carico del livello Web viene fornito sia localmente all'interno dell'area geografica che in aree diverse. Dal punto di vista del calcolo, questi servizi possono essere forniti tramite le macchine virtuali o i servizi Web di Microsoft Azure oppure tramite una combinazione di entrambi. Le macchine virtuali che forniscono servizi Web possono ospitare contenuto usando qualsiasi sistema operativo guest Microsoft Windows o Linux supportato in Microsoft Azure.


## Panoramica del modello architettonico 

Questo documento descrive un modello per fornire l'accesso ai servizi Web o a contenuto di server Web in più aree geografiche per ottenere disponibilità e ridondanza. I servizi critici sono illustrati di seguito, senza riferimenti ai vincoli della piattaforma Web o alla metodologia di sviluppo entro il servizio Web stesso. Sono disponibili due variazioni di questo modello, ovvero una che ospita il contenuto o i servizi Web nelle macchine virtuali (usando i sistemi operativi e le famiglie di prodotti supportati da Azure) e una che usa Siti Web di Azure. Il diagramma seguente è una semplice illustrazione dei servizi rilevanti e del modo in cui vengono usati come parte di questo modello mediante l'esempio relativo alle macchine virtuali.

##  Risorse aggiuntive
[Livello Web globale con carico bilanciato (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

## Vedere anche
[Architettura di CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Livello Dati con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Rete ibrida](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Livello Ricerca di Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[Livello Elaborazione batch](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO3-->