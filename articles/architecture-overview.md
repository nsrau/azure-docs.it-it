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
	ms.date="10/16/2015" 
	ms.author="robb"/>

#Architettura delle applicazioni in Microsoft Azure
Risorse per la creazione di applicazioni che usano Microsoft Azure. Include strumenti che consentono di creare diagrammi per descrivere visivamente i sistemi software.



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

![Modelli di progettazione di Azure Architecture](./media/architecture-overview/AzureArchPatterns.jpg)


##Poster di modelli di progettazione
Microsoft Patterns and Practices ha pubblicato il libro [Cloud Design Patterns](http://msdn.microsoft.com/library/dn568099.aspx), disponibile su MSDN e come file in formato PDF per il download. È inoltre disponibile un poster di grande formato in cui sono elencati tutti i modelli.

![Poster sui modelli cloud di Patterns and Practices](./media/architecture-overview/PnPPatternPosterThumb.jpg)



##Corso di certificazione per Microsoft Architecture

Microsoft ha appena rilasciato un nuovo corso sull'architettura a supporto dell'esame di certificazione Microsoft 70-534. È [disponibile gratuitamente sul sito EDX.ORG](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x). Viene usato il nuovo [3D Blueprint Visio Template](#3d-blueprint-visio-template).

![Corso di certificazione per Microsoft Architecture](./media/architecture-overview/EDXCourse.png)


##Progetti di Microsoft Architecture

Microsoft pubblica un set di [progetti di architettura](http://aka.ms/azblueprints) di alto livello che illustrano come creare tipi specifici di sistemi usando prodotti Microsoft.

Ogni progetto include:

- File flat 2D basato su **Visio** 2003 che è possibile scaricare e modificare 
- File **PDF in prospettiva 3D** colorato per presentare il progetto a destinatari con minori competenze tecniche
- **Video** che illustra la versione 3D. 

I progetti usano il [set di simboli Cloud ed Enterprise](#symbol-and-icon-sets).

![Diagramma 3D dei progetti di Microsoft Architecture](./media/architecture-overview/BluePrintThumb.jpg)



##3D Blueprint Visio Template

Le versioni 3D dei [Progetti di Microsoft Architecture](http://aka.ms/azblueprints) sono stati creati inizialmente in uno strumento non Microsoft. Il modello Visio 2013 (e versioni successive) è arrivato il 5 agosto 2015 come parte di un [Corso di certificazione per Microsoft Architecture distribuito su EDX.ORG](#microsoft-architecture-certification-course).

Il modello è anche disponibile al di fuori del corso.

- [Visualizzare prima il video di training](http://aka.ms/3dBlueprintTemplateVideo) in modo da sapere quali operazione eseguire   
- Scaricare [Microsoft 3D Blueprint Visio Template](http://aka.ms/3DBlueprintTemplate)
- Scaricare i [simboli di Cloud ed Enterprise](#drawing-symbol-and-icon-sets) da usare con il modello 3D. 

Inviare un messaggio di posta elettronica all'indirizzo [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) per domande specifiche per le quali non si trova risposta nel materiale didattico o per inviare commenti e suggerimenti. L'usabilità è uno degli obiettivi principali per il modello, per cui è utile sapere quali sono i vantaggi e gli svantaggi.

![Microsoft 3D Blueprint Visio Template](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)



##Set di simboli e icone disegno 

[Visualizzare il video di training dei simboli e Visio](http://aka.ms/CnESymbolsVideo) e quindi [scaricare il set di simboli Cloud ed Enterprise](http://aka.ms/CnESymbols) per creare materiali tecnici che illustrano Azure, Windows Server, SQL Server e altri prodotti Microsoft. È possibile usarli in diagrammi di architettura, materiale didattico, presentazioni, fogli dati, infografica, white paper e addirittura manuali di terze parti, se i manuali sono destinati alla formazione sull'uso di prodotti Microsoft. Tuttavia non possono essere usati nelle interfacce utente.

I simboli CnE sono disponibili nei formati Visio e PNG. Nel set sono incluse istruzioni aggiuntive per l'uso dei file PNG in PowerPoint.

Il set di simboli viene fornito ogni trimestre e viene aggiornato ogni volta che vengono rilasciati nuovi servizi.

Simboli aggiuntivi per Microsoft Office e tecnologie annesse sono disponibili nello [stencil di Microsoft Office Visio](http://www.microsoft.com/it-IT/download/details.aspx?id=35772), anche se non sono ottimizzati per diagrammi di architettura come il set di Cloud ed Enterprise.

**Feedback:** se sono stati usati simboli CnE, compilare il breve [sondaggio](http://aka.ms/azuresymbolssurveyv2) di 5 domande o inviare un messaggio di posta elettronica all'indirizzo [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) per domande e problemi specifici. Il parere degli utenti, incluso il feedback positivo, è importante per Microsoft Per questo motivo, si continuerà a dedicare del tempo a questa attività.

![Set di simboli/icone di Cloud ed Enterprise](./media/architecture-overview/CnESymbols.png)


##Infografiche relative all'architettura

Microsoft pubblica vari poster/infografiche relativi all'architettura, tra cui [Creazione di app reali per il cloud](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) e [Scalabilità delle applicazioni con Servizi cloud](http://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infografiche relative all'architettura di Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=Nov15_HO3-->