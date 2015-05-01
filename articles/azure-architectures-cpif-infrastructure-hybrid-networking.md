<properties 
   pageTitle="Rete ibrida (Modelli di architettura di Azure)" 
   description="Il modello Rete ibrida è incluso nell'area Infrastruttura, descritta in modo dettagliato nella documentazione sull'architettura di CPIF." 
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

# Rete ibrida (Modelli di architettura di Azure)

Il [Framework di integrazione piattaforma cloud (CPIF, Cloud Platform Integration Framework)](azure-architectures-cpif-overview.md) fornisce materiale sussidiario sull'integrazione dei carichi di lavoro per il caricamento di applicazioni in una soluzione cloud Microsoft.  

Il CPIF descrive il modo in cui le organizzazioni, i clienti e i partner devono progettare e distribuire i carichi di lavoro destinati al cloud usando la piattaforma cloud ibrida e le capacità di gestione di Azure, System Center e Windows Server. 

Il modello **Rete ibrida** è incluso nell'area **Infrastruttura**, descritta in modo dettagliato nella documentazione sull'architettura di CPIF. 

##  Rete ibrida

Il modello di progettazione Rete ibrida fornisce informazioni dettagliate sulle funzionalità e sui servizi di Azure necessari per offrire funzionalità di rete in grado di fornire prestazioni prevedibili e disponibilità elevata oltre i limiti geografici.  Per un elenco completo delle aree geografiche di Microsoft Azure e dei servizi disponibili in ogni area, vedere il sito relativo alla documentazione di Microsoft Azure.  Questo documento fornisce una panoramica delle capacità di rete di Microsoft Azure per ambienti ibridi. Rete virtuale di Microsoft Azure consente di creare reti logicamente in isolamento in Azure e di connetterle in modo sicuro al data center locale tramite Internet o mediante una connessione di rete privata.  I singoli computer client, inoltre, possono connettersi a una rete isolata di Azure usando una connessione VPN IPsec.  

Il modello di architettura Rete ibrida include le aree principali seguenti: 

- Connessione di reti locali ad Azure 
- Estensione delle reti virtuali di Azure in diverse aree geografiche 
- Estensione delle reti virtuali di Azure in più sottoscrizioni 
- Disponibilità di accesso remoto alla rete per gli sviluppatori 

## Panoramica del modello architettonico 

Il modello di architettura Rete ibrida è complesso a causa del numero possibile di scenari che possono essere creati. Questo modello architettonico sarà incentrato sui quattro scenari seguenti: 

- Rete ibrida da sito a sito con routing di rete virtuale con più hop in una singola sottoscrizione e area 
- Rete ibrida da sito a sito con routing di rete virtuale con più hop in diverse sottoscrizioni e aree 
- Rete ibrida ExpressRoute con connettività MPLS 
- Rete ibrida ExpressRoute con connettività IXP 

##  Risorse aggiuntive
[Rete ibrida (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

## Vedere anche
[Architettura di CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a) 

[Livello Web globale con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a) 

[Livello Dati con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Livello Ricerca di Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d) 

[Livello Elaborazione batch](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)


<!--HONumber=52-->