---
title: Panoramica di Enterprise Integration | Documentazione Microsoft
description: "Usare le funzionalità di Enterprise Integration per abilitare processi aziendali e scenari di integrazione tramite le app per la logica"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: d0508a40cd4ccea1d7476b366b8e1d17a0b78847


---
# <a name="overview-of-the-enterprise-integration-pack"></a>Panoramica di Enterprise Integration Pack
## <a name="what-is-the-enterprise-integration-pack"></a>Informazioni su Enterprise Integration Pack
Enterprise Integration Pack è la soluzione Microsoft basata su cloud per comunicazioni business-to-business (B2B) semplificate. Il pacchetto usa protocolli standard del settore tra cui [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) ed [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md) per scambiare messaggi tra partner commerciali. Facoltativamente è possibile proteggere i messaggi tramite firme digitali e crittografia. 

Il pacchetto consente alle organizzazioni che usano diversi protocolli e formati di scambiarsi messaggi elettronicamente trasformando i diversi formati in un formato che i sistemi di entrambe le organizzazioni possono interpretare e usare. 

Se si ha familiarità con BizTalk Server o con i Servizi BizTalk di Microsoft Azure, risulterà più facile usare le funzionalità di Enterprise Integration grazie all'analogia della maggior parte dei concetti. La principale differenza consiste nel fatto che Enterprise Integration usa gli account di integrazione per semplificare l'archiviazione e la gestione degli elementi nelle comunicazioni B2B. 

A livello di architettura, Enterprise Integration Pack è basato su **account di integrazione** in cui vengono archiviati tutti gli elementi che possono essere usati per progettare, distribuire e gestire app B2B. Un account di integrazione è essenzialmente un contenitore basato su cloud in cui archiviare elementi quali schemi, partner, certificati, mappe e contratti. Questi elementi possono quindi essere usati nelle app per la logica per compilare flussi di lavoro B2B. Prima di poter usare gli elementi in un'app per la logica, è necessario collegare l'account di integrazione all'app per la logica. Dopo il collegamento, l'app per la logica avrà accesso agli elementi dell'account di integrazione.  

## <a name="why-should-you-use-enterprise-integration"></a>Perché usare Enterprise Integration
* Con Enterprise Integration è possibile archiviare tutti gli elementi in un'unica posizione, ovvero nell'account di integrazione. 
* È possibile sfruttare il motore delle app per la logica e tutti i relativi connettori per creare flussi di lavoro B2B ed eseguire l'integrazione con applicazioni SaaS di terze parti, applicazioni locali e applicazioni personalizzate
* In aggiunta, è possibile usare le Funzioni di Azure

## <a name="how-to-get-started-with-enterprise-integration"></a>Introduzione all'uso di Enterprise Integration
È possibile compilare e gestire le applicazioni B2B in Enterprise Integration Pack tramite la finestra di progettazione dell'app per la logica del **portale di Azure**.  

Le app per la logica possono essere gestite usando [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Argomenti relativi alle app per la logica in Powershell") . 

Di seguito è disponibile una panoramica della procedura da eseguire prima di creare app nel portale di Azure: ![immagine riepilogativa](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Alcuni scenari comuni
Enterprise Integration supporta gli standard del settore seguenti:   

* EDI: Electronic Data Interchange  
* EAI: Enterprise Application Integration  

## <a name="heres-what-you-need-to-get-started"></a>Elementi necessari per iniziare
* Una sottoscrizione ad Azure con account di integrazione
* Visual Studio 2015 per creare mappe e schemi
* [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0 (Strumenti di Enterprise Integration per le app per la logica di Microsoft Azure per Visual Studio 2015 2.0)](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Prova
[Provare subito](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) per distribuire un'app per la logica personalizzata di invio e ricezione AS2 di esempio completamente operativa usando le funzionalità B2B delle app per la logica.

## <a name="learn-more-about"></a>Altre informazioni su:
* [Contratti](../logic-apps/logic-apps-enterprise-integration-agreements.md "Informazioni sui contratti di Enterprise Integration")
* [Scenari Business to Business (B2B)](../logic-apps/logic-apps-enterprise-integration-b2b.md "Informazioni su come creare app per la logica con funzionalità B2B")  
* [Certificati](logic-apps-enterprise-integration-certificates.md "Informazioni sui certificati di Enterprise Integration")
* [Codifica/decodifica dei file flat](logic-apps-enterprise-integration-flatfile.md "Informazioni su come codificare e decodificare il contenuto dei file flat")  
* [Account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md "Informazioni sugli account di integrazione")
* [Mappe](../logic-apps/logic-apps-enterprise-integration-maps.md "Informazioni sulle mappe di Enterprise Integration")
* [Partner](logic-apps-enterprise-integration-partners.md "Informazioni sui partner di Enterprise Integration")
* [Schemi](logic-apps-enterprise-integration-schemas.md "Informazioni sugli schemi di Enterprise Integration")
* [Convalida dei messaggi XML](logic-apps-enterprise-integration-xml.md "Informazioni su come convalidare i messaggi XML con le app per la logica")
* [Trasformazione XML](logic-apps-enterprise-integration-transform.md "Informazioni sulle mappe di Enterprise Integration")
* [Connettori di integrazione aziendale](../connectors/apis-list.md "Informazioni sui connettori di Enterprise Integration Pack")
* [Metadati dell'account di integrazione](../logic-apps/logic-apps-enterprise-integration-metadata.md "Informazioni sui metadati dell'account di integrazione")
* [Monitoraggio dei messaggi B2B](logic-apps-monitor-b2b-message.md "Altre informazioni sul monitoraggio dei messaggi B2B")
* [Rilevamento dei messaggi B2B nel portale OMS](logic-apps-track-b2b-messages-omsportal.md "Altre informazioni sul rilevamento dei messaggi B2B nel portale OMS")




<!--HONumber=Jan17_HO3-->


