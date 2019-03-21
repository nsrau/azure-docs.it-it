---
title: Pubblicare un'offerta di app Power BI - Azure Marketplace | Microsoft Docs
description: Pubblicare un'offerta di App di Power BI tramite il marketplace Microsoft AppSource.
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
ms.openlocfilehash: 0e82041dab1932ed7c129b598740b2441aa59315
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822753"
---
# <a name="publish-a-power-bi-app-offer"></a>Pubblicare un'offerta di App di Power BI

L'ultimo passaggio, dopo aver definito un'offerta nel portale Cloud Partner e aver creato gli asset tecnici associati, consiste nell'inviare l'offerta per la pubblicazione. Per avviare questo processo, nel riquadro sinistro della finestra di **offrono nuove** finestra, seleziona **Publish**. Per altre informazioni, vedere [Pubblicare offerte di Azure Marketplace e AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Procedura per la pubblicazione

Questi sono i passaggi principali nel processo di pubblicazione:

![Passaggi di processo di pubblicazione per App di Power BI offrono](./media/publishing-process-steps.png)

Questa tabella descrive i diversi passaggi e fornisce il tempo di completamento stimato:

|   Passaggio di pubblicazione            |   Tempo     |   DESCRIZIONE                                                                  |
| --------------------         |------------| ----------------                                                               |
| Convalida dei prerequisiti       | 15 minuti     | Vengono convalidate le informazioni e le impostazioni dell'offerta.                            |
| Certificazione                | 1-7 giorni   | Il Team di certificazione di Power BI analizza l'offerta. Il team esegue l'app Power BI tramite un test di verifica manuali installando l'app tramite l'URL di installazione fornito. Come parte del processo di certificazione app (descritto più avanti in questo documento) vengono eseguite convalide primarie.         |
| Packaging                    | \< 1 ora  | Asset tecnici dell'offerta sono disponibili per l'uso dei clienti.                        |
| Registrazione di generazione di lead | \< 1 ora  | I sistemi di registrazione di clienti potenziali vengono configurati e distribuiti.                                      |
| Approvazione dell'editore            | \-         | Completare una verifica finale e la conferma prima che la pubblicazione dell'offerta. È possibile ora anche un collegamento per visualizzare l'anteprima dell'offerta. Dopo aver ottenuto come appare la versione di anteprima, selezionare **Go Live** nel **stato** scheda. Verrà inviata una richiesta al team di onboarding per elencare l'app in AppSource.    |
| Live                         | \< 3 ore | L'offerta è ora inclusa pubblicamente ("live") in AppSource e i clienti possono visualizzare l'app e distribuirla nelle proprie sottoscrizioni di Power BI. Inoltre, si riceverà un messaggio di posta elettronica di conferma. Nella colonna a destra nella **tutte le offerte** scheda, è possibile visualizzare lo stato di tutte le offerte. Nel **stato** scheda, è possibile visualizzare lo stato del flusso di pubblicazione dettagliato per l'offerta. |
|   |   |

Consentire fino a otto giorni per il completamento del processo. Dopo che si eseguono questi passaggi di pubblicazione, l'offerta App Power BI conterrà il [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) sezione delle app di Power BI.


### <a name="app-certification-process"></a>Procedura di certificazione dell'app

Il team di onboarding di Microsoft Usa questo processo per convalidare l'invio di offerta di App di Power BI:

1. Esaminare i collegamenti della Guida e i documenti legali.
2. Convalidare le informazioni di contatto di supporto.
3. Utilizzare l'URL del programma di installazione per verificare la corretta installazione.
4. Analizzare l'app per malware e contenuti dannosi.
5. Verificare che il contenuto visualizzato corrisponda alla descrizione dell'app.
6. Verificare che le operazioni correlate alle app funzionino come previsto in Power BI. Il team l'apertura di dashboard e report con dati di esempio, si connette a origini dati personalizzate, consente di aggiornare i dati e così via.

Se identifica eventuali problemi, il team di certificazione fornisce il feedback appropriato.  Per altre informazioni sui requisiti delle app Power BI, vedere la [documentazione dell'app Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Passaggi successivi

È consigliabile monitorare regolarmente l'app nel [marketplace AppSource](https://appsource.microsoft.com).  È anche consigliabile usare la [informazioni dettagliate del venditore](../../cloud-partner-portal-orig/si-getting-started.md) funzionalità delle [portale Cloud Partner](https://cloudpartner.azure.com/#insights) per ottenere informazioni dettagliate sui clienti di marketplace e utilizzo delle app. Infine, è possibile [aggiornare l'offerta](./cpp-update-existing-offer.md).
