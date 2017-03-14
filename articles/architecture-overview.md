---
title: Architettura delle applicazioni in Microsoft Azure | Documentazione Microsoft
description: "Informazioni generali sull&quot;architettura che includono riferimenti ai modelli di progettazione più comuni."
services: 
documentationcenter: 
author: rboucher
manager: carmonm
editor: 
ms.assetid: 3a37bbc0-f624-46f3-bc4e-5a10560f9fbf
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: b463c363f131c3d75bbab229fe9f14495b5f3b95
ms.openlocfilehash: d2a13d5c80513a6fe80979bb97368b269a24a883
ms.lasthandoff: 03/01/2017


---
# <a name="application-architecture-on-microsoft-azure"></a>Architettura delle applicazioni in Microsoft Azure
Questo articolo elenca le risorse per la creazione di applicazioni che usano Microsoft Azure. Include strumenti che consentono di disegnare e descrivere visivamente i sistemi software.

## <a name="design-patterns-poster"></a>Poster di modelli di progettazione
Microsoft Patterns and Practices ha pubblicato il libro [Cloud Design Patterns](http://msdn.microsoft.com/library/dn568099.aspx) (Modelli di progettazione per il cloud), disponibile su MSDN e come file in formato PDF per il download. È inoltre disponibile un poster di grande formato in cui sono elencati visivamente tutti i modelli.

![Poster sui modelli cloud di Patterns and Practices](./media/architecture-overview/PnPPatternPosterThumb.jpg)


## <a name="drawing-symbol-and-icon-sets"></a>Set di simboli e icone disegno
[Guardare il video di training su simboli e Visio](http://aka.ms/CnESymbolsVideo) e quindi [scaricare il set di simboli per Cloud ed Enterprise](http://aka.ms/CnESymbols) per creare materiali tecnici che illustrano Azure, Windows Server, SQL Server e altro ancora. È possibile usare i simboli seguenti gratuitamente o a scopo di profitto.  
- diagrammi di architettura 
- materiale di formazione 
- presentazioni 
- fogli dati 
- infografica 
- white paper 
- documentazione di terze parti, se le informazioni sono destinate alla formazione sull'uso di prodotti Microsoft.

I simboli non sono destinati all'uso nelle interfacce utente, ma è possibile richiedere l'autorizzazione.  È consentito usare i simboli di Azure. In altre parole, possono essere usati per rappresentare gli stessi oggetti in modo analogo al portale di Azure.  Ad esempio, il simbolo per il bus di servizio di Azure deve essere usato solo per rappresentare e accedere agli oggetti del bus di servizio di Azure. I simboli di terze parti non sono di proprietà di Microsoft. Cercare nei relativi siti Web aziendali le regole sull'uso delle icone.

I simboli CnE sono disponibili in formato Visio, SVG e PNG. Nel set sono incluse istruzioni aggiuntive per usare facilmente i simboli in PowerPoint. Il set di simboli viene fornito ogni trimestre e viene aggiornato ogni volta che vengono rilasciati nuovi servizi.

Simboli aggiuntivi per Microsoft Office e tecnologie correlate sono disponibili nello [stencil di Microsoft Office Visio](http://www.microsoft.com/en-us/download/details.aspx?id=35772), anche se non sono ottimizzati per diagrammi di architettura come il set per Cloud ed Enterprise.   

Per domande specifiche, commenti e suggerimenti scrivere all'indirizzo [ CnESymbols@microsoft.com ](mailto:CnESymbols@microsoft.com). Il parere degli utenti, incluso il feedback positivo, è importante per Microsoft Per questo motivo, si continuerà a dedicare del tempo a questa attività.

![Set di simboli/icone di Cloud ed Enterprise](./media/architecture-overview/CnESymbols.png)

## <a name="microsoft-architecture-certification-course"></a>Corso di certificazione per Microsoft Architecture
Microsoft ha creato un nuovo corso di architettura ad agosto 2015 a supporto dell'esame di certificazione 70-534. È [disponibile gratuitamente sul sito EDX.ORG](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x).  Viene usato [3D Blueprint Visio Template](#3d-blueprint-visio-template).

![Corso di certificazione per Microsoft Architecture](./media/architecture-overview/EDXCourse.png)

## <a name="microsoft-solutions"></a>Soluzioni Microsoft
Microsoft pubblica un set di [architetture della soluzione](http://aka.ms/azblueprints) di alto livello che illustrano come creare tipi specifici di sistemi usando prodotti Microsoft.

In passato Microsoft ha pubblicato un set di progetti 3D isometrici che illustravano architetture di esempio. Questi progetti sono stati sostituiti dalle architetture della soluzione. Il collegamento ai progetti è stato reindirizzato per puntare alle soluzioni. Se fosse necessario accedere ai materiali dei progetti precedenti, inviare un messaggio di posta elettronica all'indirizzo [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) con la richiesta.   

I diagrammi dei progetti e delle architetture della soluzione usano entrambi parti del [set di simboli per Cloud ed Enterprise](http://aka.ms/CnESymbols).   

![Diagramma 3D dei progetti di Microsoft Architecture](./media/architecture-overview/BluePrintThumb.jpg)

## <a name="3d-blueprint-visio-template"></a>3D Blueprint Visio Template
Le versioni 3D degli ormai inattivi [progetti di Microsoft Architecture](http://aka.ms/azblueprints) sono stati creati inizialmente in uno strumento non Microsoft. Un modello Visio 2013 (e versioni successive) è stato fornito il 5 agosto 2015 come parte di un [Corso di certificazione per Microsoft Architecture](#microsoft-architecture-certification-course) distribuito in EDX.ORG.

Il modello è anche disponibile al di fuori del corso.

* [Visualizzare prima il video di training](http://aka.ms/3dBlueprintTemplateVideo) in modo da sapere quali operazione eseguire   
* Scaricare [Microsoft 3D Blueprint Visio Template](http://aka.ms/3DBlueprintTemplate)
* Scaricare il [set di simboli per Cloud ed Enterprise](https://www.microsoft.com/en-us/download/details.aspx?id=41937) da usare con il modello 3D. 

Inviare un messaggio di posta elettronica all'indirizzo [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) per domande specifiche per le quali non si trova risposta nel materiale didattico o per inviare commenti e suggerimenti. Il modello non è più in fase di sviluppo attivo, ma è ancora utile e pertinente perché può usare qualsiasi PNG o il [set di simboli per Cloud ed Enterprise](http://aka.ms/CnESymbols), che vengono aggiornati.  

![Microsoft 3D Blueprint Visio Template](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)

## <a name="architecture-infographics"></a>Infografiche relative all'architettura
Microsoft pubblica vari poster/infografiche relativi all'architettura, tra cui [Creazione di app reali per il cloud](https://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) e [Scalabilità delle applicazioni con Servizi cloud](https://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infografiche relative all'architettura di Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

