---
title: Elenco delle attività di amministrazione e sviluppo in Servizi BizTalk | Documentazione Microsoft
description: Pianificazione e processo di aiuto per la distribuzione di servizi BizTalk di Azure.
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 6b8e0fea73dc24f7e680482be1f06fba6d702804
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916241"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Elenco dell’attività di Amministrazione e Sviluppo in Servizi BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Introduzione
Quando si lavora con i servizi BizTalk di Microsoft Azure, esistono diversi componenti in locale e basati su cloud da considerare. Per iniziare, considerare il seguente flusso del processo:  

| Passaggio | Chi è il responsabile | Attività | Collegamenti correlati |
| --- | --- | --- | --- |
| 1. |Amministratore |Creare la sottoscrizione di Microsoft Azure usando un account Microsoft o un account aziendale |[Portale di Azure](https://portal.azure.com) |
| 2. |Amministratore |Creare o eseguire il provisioning di un servizio BizTalk |[Creare un servizio BizTalk](/previous-versions/azure/reference/dn232347(v=azure.100)) |
| 3. |Amministratore |Registrazione della distribuzione dei servizi BizTalk dell’azienda |[Registrazione e aggiornamento di una distribuzione del servizio BizTalk nel portale dei servizi BizTalk](/previous-versions/azure/hh689837(v=azure.100)) |
| 4. |Amministratore |Si applica se l'applicazione usa BizTalk Adapter Service per connettersi a un sistema line-of-business locale o usa una destinazione coda o argomento.  Creare lo spazio dei nomi del bus di servizio di Azure Assegnare lo spazio dei nomi, il nome dell'autorità emittente del bus di servizio e i valori della chiave dell'autorità emittente del bus di servizio agli sviluppatori. |[Procedura: Creare o modificare un Namespace di servizio del Bus di servizio](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) e [valori Ottieni nome dell'autorità emittente e chiave dell'autorità emittente](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |Installare l’SDK e creare il progetto Servizio BizTalk in Visual Studio. |[Installare Azure BizTalk Services SDK](/previous-versions/azure/hh689760(v=azure.100)) e [Creare endpoint di messaggistica avanzate in Azure](/previous-versions/azure/hh689766(v=azure.100)) |
| 6. |Developer |Distribuire il progetto dei Servizi BizTalk nel Servizio BizTalk ospitato in Azure. |[Distribuzione e aggiornamento del progetto di servizi BizTalk](/previous-versions/azure/hh689881(v=azure.100)) |
| 7. |Amministratore |Si applica se usa EDI:  È possibile aggiungere partner e creare contratti nel portale Servizi BizTalk di Microsoft Azure Quando si crea un contratto, è possibile aggiungere il bridge e/o le trasformazioni create dallo sviluppatore per le impostazioni del contratto. |[Configurazione di EDI, AS2 ed EDIFACT nel portale servizi BizTalk](/previous-versions/azure/hh689853(v=azure.100)) |
| 8. |Amministratore |Tramite [REST](/previous-versions/azure/reference/dn232347(v=azure.100)) monitorare l'integrità del servizio BizTalk, comprese le metriche delle prestazioni. |[Servizi BizTalk: Schede dashboard, monitoraggio e scalabilità](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Amministratore |Tramite il portale di servizi BizTalk di Microsoft Azure, è possibile gestire gli elementi usati dai Servizi BizTalk e tenere traccia dei messaggi man mano che vengono elaborati dai file di bridge. |[Uso del portale di Servizi BizTalk](/previous-versions/azure/dn874043(v=azure.100)) |
| 10. |Amministratore |Creare un piano di backup per eseguire il backup del Servizio BizTalk. |[Continuità aziendale e ripristino di emergenza nei servizi BizTalk](/previous-versions/azure/dn509557(v=azure.100)) |

## <a name="next-steps"></a>Fasi successive
[Esercitazioni ed esempi](/previous-versions/azure/hh689895(v=azure.100))

[Creare il progetto in Visual Studio](/previous-versions/azure/hh689811(v=azure.100))

[Installare Azure BizTalk Services SDK](/previous-versions/azure/hh689760(v=azure.100))

## <a name="concepts"></a>Concetti
[Creare il progetto in Visual Studio](/previous-versions/azure/hh689811(v=azure.100))  
[EDI, AS2 ed EDIFACT di messaggistica (Business to Business)](/previous-versions/azure/hh689898(v=azure.100))  

## <a name="other-resources"></a>Altre risorse
[Aggiungi origine, destinazione e Bridge di endpoint di messaggistica](/previous-versions/azure/hh689877(v=azure.100))  
[Informazioni su e creare trasformazioni e mappe messaggi](/previous-versions/azure/hh689905(v=azure.100))  
[Uso del servizio Adapter BizTalk (BAS)](/previous-versions/azure/hh689889(v=azure.100))  
[Servizi BizTalk di Azure](https://go.microsoft.com/fwlink/p/?LinkID=303664)

