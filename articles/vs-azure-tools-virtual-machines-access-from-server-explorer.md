---
title: Accesso a Macchine virtuali di Azure da Esplora server | Documentazione Microsoft
description: Panoramica su come visualizzare, creare e gestire macchine virtuali di Azure (VM) in Esplora Server in Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/31/2017
ms.author: ghogen
ms.openlocfilehash: 81a0e2923ddbb6960066f01d6365e8c9278defac
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42142429"
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Accesso alle macchine virtuali di Azure da Esplora server

Se ci sono macchine virtuali ospitate da Azure, è possibile accedervi in Esplora server. Per visualizzare i servizi mobili, è necessario prima di tutto accedere alla sottoscrizione Azure. Per accedere, aprire il menu di scelta rapida per il nodo Azure e scegliere **Connetti a Microsoft Azure**.

1. In Cloud Explorer scegliere una macchina virtuale e quindi premere F4 per visualizzarne la finestra delle proprietà.

    La tabella seguente illustra le proprietà disponibili, le quali sono tuttavia di sola lettura. Per modificarle, usare il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

   | Proprietà | DESCRIZIONE |
   | --- | --- |
   | Nome DNS |URL con l'indirizzo Internet della macchina virtuale. |
   | Environment |Per una macchina virtuale, il valore di questa proprietà è sempre Produzione. |
   | NOME |Nome della macchina virtuale. |
   | Dimensione |Dimensioni della macchina virtuale corrispondenti alla quantità di memoria e spazio su disco disponibili. Per altre informazioni vedere [Dimensioni delle macchine virtuali](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | Status |I valori includono Avvio in corso, Avviato, Arresto, Arrestato e Recupero dello stato. Se viene visualizzato Recupero dello stato, lo stato corrente è sconosciuto. I valori di questa proprietà differiscono da quelli usati nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | SubscriptionID |ID sottoscrizione dell'account Azure. È possibile vedere queste informazioni nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) visualizzando le proprietà di una sottoscrizione. |
2. Scegliere un nodo di endpoint e quindi visualizzare la finestra **Proprietà** .
3. La tabella seguente descrive le proprietà disponibili degli endpoint, che però sono di sola lettura. Per aggiungere o modificare gli endpoint di una macchina virtuale, usare il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 

   | Proprietà | DESCRIZIONE |
   | --- | --- |
   | NOME |Identificatore dell'endpoint. |
   | Private Port |Porta per l'accesso di rete interno all'applicazione. |
   | Protocollo |Protocollo usato dal livello di trasporto per l'endpoint TCP o UDP. |
   | Public Port |Porta usata per l'accesso pubblico all'applicazione. |
