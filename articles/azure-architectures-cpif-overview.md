<properties 
   pageTitle="Framework di integrazione piattaforma cloud | Microsoft Azure" 
   description="Il Framework di integrazione piattaforma cloud (CPIF, Cloud Platform Integration Framework) fornisce materiale sussidiario sull'integrazione dei carichi di lavoro per il caricamento di applicazioni in una soluzione cloud Microsoft ed è costituito da modelli architettonici per Microsoft Azure" 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>


# Framework di integrazione piattaforma cloud (Modelli di architettura di Azure)

Il Framework di integrazione piattaforma cloud (CPIF, Cloud Platform Integration Framework) fornisce materiale sussidiario sull'integrazione dei carichi di lavoro per il caricamento di applicazioni in una soluzione cloud Microsoft.

Il CPIF descrive il modo in cui le organizzazioni, i clienti e i partner devono progettare e distribuire i carichi di lavoro destinati al cloud usando la piattaforma cloud ibrida e le capacità di gestione di Azure, System Center e Windows Server. I domini del CPIF sono stati suddivisi nelle funzioni seguenti:

![Parte relativa ai tag nella risorsa e nei pannelli relativi ai gruppi di risorse](./media/azure-architecture-cpif-overview/overview.png)

##  Architettura di CPIF

Gli ambienti cloud pubblici e privati forniscono elementi comuni per supportare l'esecuzione dei carichi di lavoro. Anche se queste architetture sono relativamente ben comprese negli ambienti locali fisici e virtualizzati tradizionali, i costrutti inclusi in Microsoft Azure richiedono pianificazione aggiuntiva per razionalizzare l'infrastruttura e le capacità delle piattaforme disponibili in ambienti cloud pubblici. Per supportare lo sviluppo di un'applicazione o un servizio ospitato in Azure, è necessaria una serie di modelli che illustrano i diversi componenti necessari per creare una determinata soluzione per il carico di lavoro.

Questi modelli architettonici rientrano nelle categorie seguenti:

- **Infrastruttura**: Microsoft Azure è una soluzione di tipo infrastruttura e piattaforma come servizio, costituita da diversi servizi e capacità sottostanti. Questi servizi possono essere genericamente suddivisi in calcolo, archiviazione e servizi di rete, ma sono disponibili alcune capacità che potrebbero non rientrare in queste definizioni. I modelli di tipo Infrastruttura forniscono informazioni dettagliate su un'area funzionale specifica di Microsoft Azure, necessaria per fornire un determinato servizio a una o più soluzioni ospitate in una particolare sottoscrizione di Azure. 
- **Fondamenta**: quando si crea un'applicazione o un servizio a più livelli in Azure, è necessario usare una combinazione di più elementi per offrire un ambiente di hosting idoneo. I modelli di tipo Fondamenta costituiscono uno o più servizi di Microsoft Azure per supportare un determinato livello di funzionalità in un'applicazione. Ciò potrebbe richiedere l'uso di uno o più componenti descritti nei modelli di tipo Infrastruttura illustrati in precedenza. Ad esempio, il livello di presentazione di un'applicazione a più livelli richiede capacità di calcolo, rete e archiviazione in Azure per potere funzionare correttamente. I modelli di tipo Fondamenta devono essere combinati con altri modelli come parte di una determinata soluzione.
- **Soluzione**: i modelli di tipo Soluzione sono costituiti da modelli di tipo Infrastruttura e/o Fondamenta per rappresentare un'applicazione o un servizio finale in fase di sviluppo. È previsto che le soluzioni complesse non vengano sviluppate in modo indipendente rispetto ad altri modelli, ma che usino invece i componenti e le interfacce definiti in ogni categoria di modelli illustrata in precedenza.    

## Concetti relativi ai modelli architettonici di Azure

Per supportare lo sviluppo di architetture della soluzione in Azure, viene fornita una serie di guide relative ai modelli per scenari comuni. Anche se queste guide relative agli scenari verranno costruite nel corso del tempo, i modelli previsti includono i seguenti:

- Livello Web globale con carico bilanciato 
- Livello Dati con carico bilanciato
- Livello Elaborazione batch
- Rete ibrida
- Ricerca di Azure 

##  Risorse aggiuntive
[Architettura di CPIF (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

## Vedere anche
[Livello Web globale con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Livello Dati con carico bilanciato](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Livello Elaborazione batch](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[Rete di Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Ricerca di Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=Oct15_HO4-->