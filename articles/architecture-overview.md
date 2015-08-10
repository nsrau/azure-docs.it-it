<properties 
	pageTitle="Architettura delle applicazioni in Microsoft Azure" 
	description="Informazioni generali sull'architettura che includono riferimenti ai modelli di progettazione più comuni." 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="robb"/>

#Architettura delle applicazioni in Microsoft Azure
Risorse per la creazione di applicazioni che usano Microsoft Azure.

##Modelli di progettazione architetturale di Azure
Microsoft pubblica serie di modelli di progettazione architetturale che consentono di creare progettazioni personalizzate. I modelli rappresentano linee guida sintetiche di progettazione architetturale che congiuntamente forniscono indicazioni per sfruttare al meglio la piattaforma Microsoft Azure per far fronte a esigenze aziendali specifiche dell'organizzazione.


[Panoramica](../azure-architectures-cpif-overview/) - [Reti ibride](../azure-architectures-cpif-infrastructure-hybrid-networking/) - [Elaborazione batch esterna](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/) - [Livello Dati multisito](../azure-architectures-cpif-foundation-multi-site-data-tier/) - [Livello Web globale con carico bilanciato](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/) - [Livello Ricerca di Azure](../azure-architectures-cpif-foundation-azure-search-tier/)
 
Ogni modello contiene
 
- Una descrizione del servizio
- Un elenco di servizi di Azure necessari per usare il modello
- Diagrammi di architettura
- Dipendenze dell'architettura
- Limitazioni o considerazioni sulla progettazione che possono influire sul modello
- Interfacce ed endpoint
- Anti-modelli
- Considerazioni fondamentali di alto livello sull'architettura, tra cui disponibilità e resilienza, contratti di servizio compositi per i servizi usati, scalabilità e prestazioni, costi e considerazioni operative.

##Progetti di Microsoft Architecture

Microsoft sta pubblicando un set di progetti di architettura di alto livello che illustrano come creare tipi specifici di sistemi che includono prodotti Microsoft e servizi di Microsoft Azure. Ogni progetto include un file 2D basato su Visio che è possibile scaricare e modificare, un file 3D più vivace in formato PDF per introdurre il progetto e un video che illustra la versione 3D. Vedere [Progetti di Microsoft Architecture](http://msdn.microsoft.com/dn630664).

I diagrammi di progetto 2D usano il set di simboli di Cloud ed Enterprise indicato più avanti.

I PDF di progetto 3D vengono creati in uno strumento non Microsoft, ma è in fase di sviluppo un modello di Visio. Un [video di formazione sulla versione BETA del modello è disponibile qui](http://aka.ms/3dBlueprintTemplate). Per ottenere la versione Beta del modello di progetto 3D Visio, inviare un messaggio di posta elettronica all'indirizzo [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

![Diagramma 3D dei progetti di Microsoft Architecture](./media/architecture-overview/BluePrintThumb.jpg)

##Set di simboli/icone di Cloud ed Enterprise

[Scaricare il set di simboli/icone di Cloud ed Enterprise](http://aka.ms/CnESymbols) per creare materiali tecnici che illustrano Azure, Windows Server, SQL Server e altri prodotti Microsoft. È possibile usarli in diagrammi di architettura, materiali per la formazione, presentazioni, fogli dati, infografiche, white paper e addirittura manuali di terze parti, se i manuali sono destinati alla formazione sull'uso di prodotti Microsoft. Per i simboli vengono usati i formati Visio e PNG. Sono incluse istruzioni per l'uso dei file PNG in PowerPoint.

Il set di simboli viene fornito ogni trimestre e viene aggiornato ogni volta che vengono rilasciati nuovi servizi. Per ottenere un'anteprima della versione più recente, che potrebbe includere servizi di Azure aggiuntivi, inviare un messaggio di posta elettronica all'indirizzo [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).

Il parere degli utenti è importante per Microsoft. Per questo motivo, il download contiene istruzioni per l'invio di commenti e suggerimenti. Se si sono già usati i simboli, compilare il breve [sondaggio](http://aka.ms/azuresymbolssurveyv2) di 5 domande o inviare un messaggio di posta elettronica agli indirizzi precedenti per fornire commenti sulla loro utilità e sul loro uso.

Simboli aggiuntivi sono disponibili nello [stencil di Microsoft Office Visio](http://www.microsoft.com/it-it/download/details.aspx?id=35772), anche se non sono ottimizzati per diagrammi di architettura come il set di Cloud ed Enterprise.

![Set di simboli/icone di Cloud ed Enterprise](./media/architecture-overview/CnESymbols.png)

##Modelli di progettazione
Microsoft Patterns and Practices ha pubblicato il libro [Cloud Design Patterns](http://msdn.microsoft.com/library/dn568099.aspx), disponibile su MSDN e come file in formato PDF per il download. È inoltre disponibile un poster di grande formato in cui sono elencati tutti i modelli.

![Poster sui modelli cloud di Patterns and Practices](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Infografiche relative all'architettura
Microsoft pubblica vari poster/infografiche relativi all'architettura, tra cui [Creazione di app reali per il cloud](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) e [Scalabilità delle applicazioni con Servizi cloud](http://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infografiche relative all'architettura di Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=July15_HO5-->