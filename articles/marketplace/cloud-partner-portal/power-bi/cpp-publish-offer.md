---
title: Pubblicare un'offerta di app Power BI - Azure Marketplace | Microsoft Docs
description: Pubblicare un'offerta di app Power BI in Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665712"
---
# <a name="publish-power-bi-app-offer"></a>Pubblicare un'offerta di app Power BI

L'ultimo passaggio, dopo aver definito l'offerta nel portale e aver creato le risorse tecniche associate, consiste nell'inviare l'offerta per la pubblicazione.  Per iniziare, fare clic sul pulsante **Publish** (Pubblica) nel menu verticale nella finestra **New Offer** (Nuova offerta).  Per altre informazioni, vedere [Pubblicare offerte di Azure Marketplace e AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Procedura per la pubblicazione

Il diagramma seguente illustra i passaggi principali della procedura di pubblicazione da seguire per rendere disponibile un'offerta nel marketplace.

![Procedura per la pubblicazione di app Power BI](./media/publishing-process-steps.png)

La tabella seguente descrive questi passaggi e indica una stima del tempo massimo necessario per il completamento:

|   Passaggio di pubblicazione            |   Tempo     |   Descrizione                                                                  |
| --------------------         |------------| ----------------                                                               |
| Convalida dei prerequisiti       | 15 min     | Vengono convalidate le informazioni e le impostazioni dell'offerta.                            |
| Certificazione                | 1-7 giorni   | Il team di certificazione di Power BI analizza l'offerta. Il team sottopone l'app Power BI a un test di verifica manuale installando l'app con l'URL di installazione specificato. Come parte del processo di certificazione dell'app viene eseguita una serie di verifiche di base. Vedere di seguito.         |
| Creazione del pacchetto                    | \< 1 ora  | Gli asset tecnici dell'offerta vengono inclusi in un pacchetto da distribuire ai clienti.                        |
| Registrazione per la generazione di clienti potenziali | \< 1 ora  | I sistemi di registrazione di clienti potenziali vengono configurati e distribuiti.                                      |
| Approvazione dell'editore            | \-         | Revisione e conferma finali dell'editore prima che l'offerta diventi disponibile in Azure Marketplace. Sarà ora disponibile anche un collegamento per visualizzare l'anteprima dell'offerta. Quando si è soddisfatti dell'anteprima, fare clic sul pulsante **Go Live** nella scheda **Status** (Stato). Questa azione invia una richiesta al team di onboarding per elencare l'app in AppSource.    |
| Live                         | \< 3 ore | L'offerta è ora inclusa nell'elenco pubblico ("live") di AppSource e i clienti potranno visualizzare e distribuire l'app nelle relative sottoscrizioni di Power BI. Si riceverà anche un messaggio di posta elettronica di conferma. In qualsiasi momento è possibile fare clic sulla scheda **All offers** (Tutte le offerte) e vedere lo stato per tutte le offerte elencate nella colonna di destra. È possibile fare clic sulla scheda **Status** (Stato) per vedere lo stato del flusso di pubblicazione nei dettagli dell'offerta. |
|   |   |

Il completamento di questa procedura può richiedere al massimo otto giorni. Dopo l'esecuzione di questa procedura di pubblicazione, l'offerta di app Power BI verrà pubblicata nella sezione relativa alle app Power BI in [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20).


### <a name="app-certification-process"></a>Procedura di certificazione dell'app

Il team di onboarding di Microsoft adotta la procedura seguente per convalidare l'invio dell'offerta di Power BI:

1. Esamina i documenti legali e i collegamenti della Guida.
2. Convalida le informazioni di contatto del supporto.
3. Verifica che l'installazione venga eseguita correttamente usando l'URL del programma di installazione. 
4. Analizza l'app per verificare che non sia presente malware e altro contenuto dannoso. 
5. Verifica che il contenuto visualizzato corrisponda alla descrizione dell'app.
6. Controlla che le operazioni correlate all'app funzionino come previsto in Power BI: apertura di report e dashboard con dati di esempio, connessione a origini dati personalizzate, aggiornamento e così via.

Se identifica eventuali problemi, il team di certificazione fornisce il feedback appropriato.  Per altre informazioni sui requisiti di Power BI, vedere la [documentazione relativa alle app Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Passaggi successivi

È consigliabile monitorare regolarmente l'app in [AppSource Marketplace](https://appsource.microsoft.com).  È inoltre consigliabile usare la funzionalità [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) (Informazioni dettagliate per il venditore) del [portale Cloud Partner](https://cloudpartner.azure.com/#insights) per visualizzare informazioni dettagliate sui clienti e sull'uso del marketplace.  È anche possibile eseguire determinati [aggiornamenti per l'offerta](./cpp-update-existing-offer.md).
