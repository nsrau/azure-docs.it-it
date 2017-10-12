---
title: Convertire i dati XML con le trasformazioni - App per la logica di Azure | Microsoft Docs
description: Creare trasformazioni o mappe per convertire i dati XML da un formato a un altro nelle app per la logica con l'SDK di Enterprise Integration
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: fb6027769377b3527b11f7831dab3bb8d7061c84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="enterprise-integration-with-xml-transforms"></a>Enterprise Integration con trasformazioni XML
## <a name="overview"></a>Panoramica
Il connettore di trasformazione di Enterprise Integration converte i dati da un formato a un altro. Ad esempio, si è ricevuto un messaggio contenente la data corrente nel formato YearMonthDay. È possibile usare una trasformazione per riformattare la data nel formato MonthDayYear.

## <a name="what-does-a-transform-do"></a>Funzioni della trasformazione
Un'app per le API Transform, conosciuta anche come Map, consiste in uno schema XML di origine (input) e in uno schema XML di destinazione (output). È possibile usare diverse funzioni predefinite per contribuire a gestire o controllare i dati, incluse la modifica di stringhe, le operazioni Conditional Assignment, le espressioni aritmetiche, i formattatori di data/ora e persino i costrutti a ciclo continuo.

## <a name="how-to-create-a-transform"></a>Procedura: Creare una trasformazione
È possibile creare una mappa/trasformazione tramite l' [SDK di Enterprise Integration](https://aka.ms/vsmapsandschemas)di Visual Studio. Al termine della creazione e del testing della trasformazione, caricare la trasformazione nell'account di integrazione. 

## <a name="how-to-use-a-transform"></a>Procedura: Usare una trasformazione
Dopo aver caricato il file della trasformazione/mappa nell'account di integrazione, è possibile usarlo per creare un'app per la logica. L'app per la logica esegue le trasformazioni a ogni attivazione dell'app, con la relativa trasformazione del contenuto immesso.

**Di seguito è riportata la procedura per l'uso di una trasformazione**:

### <a name="prerequisites"></a>Prerequisiti

* Creare un account di integrazione e aggiungervi una mappa  

Una volta soddisfatti i requisiti, è ora di creare l'app per la logica:  

1. Creare un'app per la logica e [collegarla all'account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md "Informazioni su come collegare un account di integrazione a un'app per la logica") che contiene la mappa.
2. Aggiungere un trigger **Richiesta** all'app per la logica.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Aggiungere l'azione **Trasforma XML** selezionando prima **Aggiungi un'azione**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Immettere la parola *trasforma* nella casella di ricerca per filtrare tutte le azioni e ottenere quella che si vuole usare.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Selezionare l'azione **Trasforma XML**   
6. Aggiungere il **CONTENUTO** XML da trasformare. È possibile usare tutti i dati XML ricevuti nella richiesta HTTP come **CONTENUTO**. In questo esempio selezionare il corpo della richiesta HTTP che ha attivato l'app per la logica.
7. Selezionare il nome della **MAPPA** che si vuole usare per eseguire la trasformazione. La mappa deve essere già presente nell'account di integrazione. In un passaggio precedente è stato concesso l'accesso dell'app per la logica all'account di integrazione che contiene la mappa.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Salvare il lavoro   
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

A questo punto, la configurazione della mappa è completa. In un'applicazione reale è possibile archiviare i dati trasformati in un'applicazione LOB, ad esempio SalesForce. È possibile eseguire facilmente questa azione inviando l'output della trasformazione a Salesforce. 

È ora possibile testare la trasformazione eseguendo una richiesta all'endpoint HTTP.  

## <a name="features-and-use-cases"></a>Funzionalità e casi d'uso
* La trasformazione creata in una mappa può essere semplice, come ad esempio la copia di un nome e di un indirizzo da un documento a un altro. In alternativa, è possibile creare trasformazioni più complesse usando operazioni di mapping predefinite.  
* Sono già disponibili più operazioni o funzioni di mapping, incluse stringhe, funzioni data/ora e così via.  
* È possibile eseguire una copia diretta dei dati tra gli schemi. Nel mapper incluso nell'SDK, questa operazione è semplice quanto tracciare una linea che colleghi gli elementi nello schema di origine con le relative controparti nello schema di destinazione.  
* Durante la creazione di una mappa viene visualizzata la relativa rappresentazione grafica, che mostra tutte le relazioni e i collegamenti creati dall'utente.
* Usare la funzione Mappa di test per aggiungere un messaggio XML di esempio. Con un semplice clic, è possibile testare la mappa creata e visualizzare l'output generato.  
* Caricare le mappe esistenti  
* Include il supporto per il formato XML.

## <a name="learn-more"></a>Altre informazioni
* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")  
* [Altre informazioni sulle mappe](../logic-apps/logic-apps-enterprise-integration-maps.md "Informazioni sulle mappe di Enterprise Integration")  

