<properties 
   pageTitle="Livello Elaborazione batch esterna (Modelli di architettura di Azure)" 
   description="Il modello Livello Elaborazione batch esterna è incluso nell'area Infrastruttura, descritta in modo dettagliato nella documentazione sull'architettura CPIF." 
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

# Livello Elaborazione batch esterna (Modelli di architettura di Azure)

Il [Framework di integrazione piattaforma cloud (CPIF, Cloud Platform Integration Framework)](azure-architectures-cpif-overview.md) fornisce materiale sussidiario sull'integrazione dei carichi di lavoro per il caricamento di applicazioni in una soluzione cloud Microsoft.

Il CPIF descrive il modo in cui le organizzazioni, i clienti e i partner devono progettare e distribuire i carichi di lavoro destinati al cloud usando la piattaforma cloud ibrida e le capacità di gestione di Azure, System Center e Windows Server.

Il modello **Livello Elaborazione batch esterna** è incluso nell'area **Infrastruttura**, descritta in modo dettagliato nella documentazione sull'architettura CPIF.

##  Livello Elaborazione batch esterna

Il modello di progettazione Livello Elaborazione batch esterna fornisce informazioni dettagliate sulle funzionalità e sui servizi di Azure necessari per offrire l'elaborazione dei dati back-end con tolleranza di errore e scalabilità. Questi servizi vengono realizzati come ruoli di lavoro nei servizi cloud in Azure, che possono essere attualmente distribuiti in qualsiasi data center di Azure.

I carichi di lavoro dell'elaborazione batch sono univoci, poiché in genere forniscono un'interfaccia utente minima, se presente. Un esempio di questo tipo di carico di lavoro locale è costituito da un servizio di Windows in esecuzione su Windows Server. Se si considera questo tipo di carico di lavoro in un ambiente cloud, la distribuzione di un intero server per l'esecuzione di un carico di lavoro sarebbe eccessiva, poiché sono solo necessarie risorse di calcolo, risorse di archiviazione e connettività di rete. Il ruolo di lavoro rappresenta l'implementazione di questo carico di lavoro in Azure.

Per definizione, un processo di elaborazione batch eseguito in Azure è un carico di lavoro che si connette a una risorsa, fornisce logica di business (calcolo) e restituisce output. Le risorse di input e di output vengono definite dall'utente e possono essere file flat, BLOB nell'Archiviazione BLOB di Azure, un database NoSQL o database relazionali.

La logica di business viene implementata in un ruolo di lavoro di Azure, in genere mediante la definizione della logica di business necessaria in una libreria .NET. Anche se la distribuzione di un ruolo di lavoro in Azure è un'operazione semplice, la distribuzione di un ruolo di lavoro con tolleranza di errore e scalabilità richiede una struttura che tiene in considerazione il modo in cui il servizio viene eseguito e gestito in Azure. Questo modello illustra in modo dettagliato la struttura che rispetta questi requisiti e descrive il modo in cui è possibile eseguirne l'implementazione.

## Panoramica del modello architettonico 

Questo documento illustra un modello per l'elaborazione batch esterna che usa le istanze del ruolo di lavoro incluse in un servizio cloud in Azure. I componenti critici per questa struttura sono illustrati di seguito. Questo diagramma illustra le istanze minime necessarie per ottenere la tolleranza di errore. È possibile distribuire istanze aggiuntive per aumentare le prestazioni del servizio. È anche possibile abilitare la scalabilità automatica per semplificare il ridimensionamento delle istanze in base al tempo o ad altre metriche del server.

##  Risorse aggiuntive
[Livello Elaborazione batch (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## Vedere anche
[Architettura di CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Livello Web globale con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Livello Dati con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Rete ibrida](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Livello Ricerca di Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=July15_HO3-->