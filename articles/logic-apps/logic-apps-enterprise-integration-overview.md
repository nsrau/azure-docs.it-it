---
title: Enterprise Integration per B2B - App per la logica di Azure | Microsoft Docs
description: Compilare flussi di lavoro B2B e supportare scenari di integrazione aziendale per le app per la logica con Enterprise Integration Pack
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
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 03cd3f4edd7bb7895efa02475411d813ef44b8b3
ms.openlocfilehash: 538ba17e2b90ca1b30aee79c1f1835f3c4cdfb3c


---
# <a name="overview-b2b-scenarios-and-communication-with-the-enterprise-integration-pack"></a>Panoramica: scenari B2B e comunicazione con Enterprise Integration Pack

Per i flussi di lavoro Business to Business (B2B) e una fluida comunicazione con le app per la logica di Azure, è possibile abilitare gli scenari di integrazione aziendale grazie alla soluzione basata sul cloud di Microsoft, Enterprise Integration Pack. Le organizzazioni saranno in grado di scambiarsi messaggi elettronicamente anche se usano formati e protocolli diversi. Il pacchetto trasforma i diversi formati in un formato che i sistemi delle organizzazioni possano interpretare ed elaborare. Le organizzazioni possono scambiarsi messaggi grazie ai protocolli standard del settore tra cui [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) ed [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). È inoltre possibile proteggere i messaggi con firme digitali e crittografia.

Se si ha familiarità con BizTalk Server o con i Servizi BizTalk di Microsoft Azure, risulterà facile usare le funzionalità di Enterprise Integration grazie alla somiglianza della maggior parte dei concetti. La principale differenza consiste nel fatto che Enterprise Integration usa gli account di integrazione per semplificare l'archiviazione e la gestione degli elementi nelle comunicazioni B2B. 

Dal punto di vista dell'architettura, Enterprise Integration Pack si basa sugli "account di integrazione". Questi account sono contenitori basati sul cloud che archiviano elementi quali schemi, partner, certificati, mappe e contratti. È possibile usare questi elementi per progettare, distribuire e gestire le app B2B, nonché per creare flussi di lavoro B2B per le app per la logica. Prima di poter usare questi elementi però, è necessario collegare l'account di integrazione all'app per la logica. Successivamente, l'app per la logica può accedere agli elementi dell'account di integrazione.

## <a name="why-should-you-use-enterprise-integration"></a>Perché usare Enterprise Integration

* Grazie all'integrazione aziendale, è possibile archiviare tutti gli elementi in un'unica posizione, ovvero nell'account di integrazione.
* È possibile creare flussi di lavoro B2B ed eseguire l'integrazione con app SaaS (Software as a Service) di terze parti, app locali e app personalizzate usando il motore delle app per la logica di Azure e tutti i relativi connettori.
* Grazie alle funzioni di Azure, è possibile creare un codice personalizzato per le app per la logica.

## <a name="how-to-get-started-with-enterprise-integration"></a>Introduzione all'uso di Enterprise Integration

È possibile compilare e gestire le app B2B in Enterprise Integration Pack tramite la finestra di progettazione dell'app per la logica del **Portale di Azure**. Le app per la logica possono essere gestite anche usando [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Argomenti relativi alle app per la logica in PowerShell").

Di seguito vengono illustrati i passaggi più importanti della procedura da eseguire prima di creare le app nel Portale di Azure:

![immagine di panoramica](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Alcuni scenari comuni

Enterprise Integration supporta gli standard del settore seguenti:

* EDI: Electronic Data Interchange
* EAI: Enterprise Application Integration

## <a name="heres-what-you-need-to-get-started"></a>Elementi necessari per iniziare

* Una sottoscrizione ad Azure con account di integrazione
* Visual Studio 2015 per creare mappe e schemi
* [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0 (Strumenti di Enterprise Integration per le app per la logica di Microsoft Azure per Visual Studio 2015 2.0)](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Da provare subito

[Distribuire un'app per la logica di invio e ricezione AS2 di esempio completamente operativa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) che usi le funzionalità B2B per le app per la logica di Azure.

## <a name="learn-more"></a>Altre informazioni
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




<!--HONumber=Feb17_HO1-->


