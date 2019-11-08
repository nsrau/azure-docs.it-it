---
title: Pubblicare Power BI offerta di app | Azure Marketplace
description: Pubblicare un'offerta di app Power BI in Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pabutler
ms.openlocfilehash: 324be960bd2d22623763ca3e24b99be92ff04174
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826043"
---
# <a name="publish-a-power-bi-app-offer"></a>Pubblicare un'offerta di app Power BI

L'ultimo passaggio, dopo aver definito un'offerta nel portale Cloud Partner e aver creato le risorse tecniche associate, consiste nell'inviare l'offerta per la pubblicazione. Per avviare questo processo, nel riquadro sinistro della finestra **nuova offerta** selezionare **pubblica**. Per altre informazioni, vedere [Pubblicare offerte di Azure Marketplace e AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Procedura per la pubblicazione

Di seguito sono riportati i passaggi principali del processo di pubblicazione:

![Procedura di pubblicazione per Power BI offerta di app](./media/publishing-process-steps.png)

Questa tabella descrive ogni passaggio e fornisce il tempo di completamento stimato:

|   Passaggio di pubblicazione            |   Time     |   Descrizione                                                                  |
| --------------------         |------------| ----------------                                                               |
| Convalida dei prerequisiti       | 15 minuti     | Vengono convalidate le informazioni e le impostazioni dell'offerta.                            |
| Certificazione                | 1-7 giorni   | Il team di certificazione Power BI analizza l'offerta. Il team esegue l'app Power BI tramite un test di verifica manuale installando l'app tramite l'URL di installazione specificato. Le convalide primarie vengono eseguite come parte del processo di certificazione delle app (descritto più avanti in questo documento).         |
| Packaging                    | \< 1 ora  | Gli asset tecnici dell'offerta sono inclusi nel pacchetto per l'uso da parte dei clienti.                        |
| Registrazione generazione lead | \< 1 ora  | I sistemi di registrazione di clienti potenziali vengono configurati e distribuiti.                                      |
| Approvazione dell'editore            | \-         | È possibile completare una verifica finale e una conferma prima che l'offerta venga avviata. È ora disponibile anche un collegamento per visualizzare l'anteprima dell'offerta. Quando si è soddisfatti dell'aspetto dell'anteprima, selezionare **Go Live** nella scheda **stato** . Viene inviata una richiesta al team di onboarding per elencare l'app in AppSource.    |
| Live                         | \< 3 ore | L'offerta è ora elencata pubblicamente ("Live") in AppSource e i clienti possono visualizzare l'app e distribuirla nelle sottoscrizioni Power BI. Si riceverà anche un messaggio di posta elettronica di conferma. Nella colonna a destra della scheda **tutte le offerte** è possibile visualizzare lo stato di tutte le offerte. Nella scheda **stato** è possibile visualizzare lo stato dettagliato del flusso di pubblicazione per l'offerta. |
|   |   |

Attendere fino a otto giorni per il completamento del processo. Dopo aver eseguito questi passaggi di pubblicazione, l'offerta Power BI app verrà elencata nella sezione [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power bi Apps.


### <a name="app-certification-process"></a>Procedura di certificazione dell'app

Il team di onboarding di Microsoft usa questo processo per convalidare l'invio dell'offerta Power BI App:

1. Esaminare i documenti legali e i collegamenti della guida.
2. Verificare le informazioni di contatto per il supporto.
3. Usare l'URL del programma di installazione per verificare l'installazione corretta.
4. Analizzare l'app per individuare malware e altri contenuti dannosi.
5. Verificare che il contenuto visualizzato corrisponda alla descrizione dell'app.
6. Verificare che le operazioni relative alle app funzionino come previsto in Power BI. Il team apre report e dashboard con dati di esempio, si connette a origini dati personalizzate, aggiorna i dati e così via.

Se identifica eventuali problemi, il team di certificazione fornisce il feedback appropriato.  Per ulteriori informazioni sui requisiti delle app Power BI, vedere la [documentazione relativa all'app Power bi](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Passaggi successivi

È consigliabile monitorare regolarmente l'app nel [Marketplace di AppSource](https://appsource.microsoft.com).  È inoltre consigliabile usare la funzionalità [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) del [portale cloud partner](https://cloudpartner.azure.com/#insights) per ottenere informazioni dettagliate sui clienti del Marketplace e sull'utilizzo delle app. Infine, è possibile [aggiornare l'offerta](./cpp-update-existing-offer.md).
