---
title: Origini dati supportate - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Origini dati supportate
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376364"
---
# <a name="data-sources"></a>Origini dati 
QnA Maker è in grado di estrarre automaticamente coppie di domanda/risposta da comuni formati di contenuto semistrutturati, quali domande frequenti e manuali di prodotti. I contenuti possono anche essere aggiunti alla Knowledge Base da file strutturati.

## <a name="plain-faq-pages"></a>Pagine semplici di domande frequenti
Si tratta del tipo più comune di pagina di domande frequenti, in cui le risposte seguono immediatamente le domande nella stessa pagina. 

![Pagina semplice di domande frequenti](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>Pagine di domande frequenti con collegamenti a sezioni 
In questo tipo di pagina di domande frequenti, le domande sono aggregate e collegate a risposte che si trovano in diverse sezioni della stessa pagina.

 ![Pagina di domande frequenti con collegamenti a sezioni](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>Pagine di domande frequenti con collegamenti ad altre pagine 
Questo tipo di pagina di domande frequenti è simile a una pagina di domande frequenti con collegamenti a sezioni, tranne per il fatto che i collegamenti reindirizzano a un'altra pagina. QnA Maker esegue una ricerca per indicizzazione di tutte le pagine collegate per estrarre le risposte corrispondenti.

 ![Pagina delle domande frequenti con collegamento diretto](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>Manuali di prodotti

Un manuale è in genere materiale di riferimento fornito insieme a un prodotto. Consente all'utente di configurare, usare e gestire un prodotto e di risolverne i problemi. Quando QnA Maker elabora un manuale, estrae i titoli e sottotitoli come domande e il contenuto successivo come risposte. Vedere un esempio [qui](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

> [!NOTE]
> L'estrazione funziona al meglio con i manuali che hanno un sommario e/o una pagina di indice e una struttura chiara con titoli gerarchici.


## <a name="structured-data-format-through-file-upload"></a>Formato di dati strutturati tramite il caricamento di file

È anche possibile caricare in QnA Maker file strutturati come quelli in formato TSV e XLSX con colonne formattate durante la creazione della Knowledge Base. È possibile caricare file dalla scheda **Impostazioni** di una Knowledge Base

| Domanda  | Risposta  | Metadata                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | `Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 |      `Key:Value`           |
Qualsiasi colonna aggiuntiva nel file di origine viene ignorata.

## <a name="structured-data-format-through-import"></a>Formato di dati strutturati tramite l'importazione
L'importazione di una Knowledge Base sostituisce il contenuto della Knowledge Base esistente. L'importazione richiede un file TSV strutturato che contiene informazioni sull'origine dei dati. Queste informazioni consentono a QnA Maker di raggruppare le coppie domanda/risposta e di attribuirle a una specifica origine dati.

| Domanda  | Risposta  | Sorgente| Metadata                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|`Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 | Editoriale|    `Key:Value`       |

## <a name="editorial"></a>Editoriale
Se non è presente contenuto pre-esistente per popolare la Knowledge Base, è possibile aggiungerlo a livello editoriale nella Knowledge Base di QnA Maker. Informazioni su come aggiornare la Knowledge Base sono disponibili [qui](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare un servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Vedere anche  

[Panoramica di QnA Maker](../Overview/overview.md)