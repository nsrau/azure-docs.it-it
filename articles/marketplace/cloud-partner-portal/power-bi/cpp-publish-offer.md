---
title: Pubblicare l'offerta dell'app Power BI - Azure Marketplace
description: Pubblicare un'offerta power BI App nel marketplace Di Microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d52270e623a713d7188ac5ff907ebb82ada13283
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745613"
---
# <a name="publish-a-power-bi-app-offer"></a>Pubblicare un'offerta dell'app Power BI

>[!Important]
>A partire dal 30 marzo 2020, inizieremo a spostare la gestione delle offerte dell'app Power BI nel Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Panoramica](https://aka.ms/AzureCreatePBIServiceApp) sulla creazione di app Power BI per gestire le offerte migrate.

L'ultimo passaggio, dopo aver definito un'offerta nel portale Cloud Partner e aver creato le risorse tecniche associate, consiste nell'inviare l'offerta per la pubblicazione. Per avviare questo processo, nel riquadro sinistro della finestra **Nuova offerta** selezionare **Pubblica**. Per altre informazioni, vedere [Pubblicare offerte di Azure Marketplace e AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Procedura per la pubblicazione

Questi sono i passaggi principali del processo di pubblicazione:

![Passaggi del processo di pubblicazione per l'offerta dell'app Power BI](./media/publishing-process-steps.png)

Questa tabella descrive ogni passaggio e fornisce il tempo di completamento stimato:This table describes each step and provides its estimated completion time:

|   Passaggio di pubblicazione            |   Tempo     |   Descrizione                                                                  |
| --------------------         |------------| ----------------                                                               |
| Convalida dei prerequisiti       | 15 minuti     | Vengono convalidate le informazioni e le impostazioni dell'offerta.                            |
| Certificazione                | 1-7 giorni   | Il team di certificazione di Power BI analizza l'offerta. Il team esegue l'app Power BI tramite un test di verifica manuale installando l'app tramite l'URL di installazione fornito. Le convalide principali vengono eseguite come parte del processo di certificazione dell'app (descritto più avanti in questo documento).         |
| Packaging                    | \< 1 ora  | Le risorse tecniche dell'offerta sono confezionate per l'uso da parte del cliente.                        |
| Registrazione della generazione di lead | \< 1 ora  | I sistemi di registrazione di clienti potenziali vengono configurati e distribuiti.                                      |
| Approvazione dell'editore            | \-         | Puoi completare una revisione finale e una conferma prima che l'offerta venga messa in diretta. Ora avrai anche un link per visualizzare in anteprima la tua offerta. Quando sei soddisfatto dell'aspetto dell'anteprima, seleziona Passa allo **stato** nella scheda **Stato.** In questo modo viene inviata una richiesta al team di onboarding per elencare l'app in AppSource.This sends a request to the onboarding team to list your app on AppSource.    |
| Live                         | \< 3 ore | L'offerta è ora elencata pubblicamente ("live") in AppSource e i clienti possono visualizzare l'app e distribuirla nelle sottoscrizioni di Power BI. Riceverai anche un'email di conferma. Nella colonna di destra della scheda **Tutte le offerte,** puoi vedere lo stato di tutte le tue offerte. Nella scheda **Stato** è possibile visualizzare lo stato dettagliato del flusso di pubblicazione per l'offerta. |
|   |   |

Attendere fino a otto giorni per completare il processo. Dopo aver eseguito questi passaggi di pubblicazione, l'offerta dell'app Power BI verrà elencata nella sezione [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) delle app Power BI.


### <a name="app-certification-process"></a>Procedura di certificazione dell'app

Il team di onboarding Microsoft usa questo processo per convalidare l'invio dell'offerta di Power BI:The Microsoft onboarding team uses this process to validate your Power BI App offer submission:

1. Esaminare i documenti legali e i collegamenti della Guida.
2. Convalidare le informazioni di contatto del supporto tecnico.
3. Utilizzare l'URL del programma di installazione per verificare la corretta installazione.
4. Eseguire la scansione dell'applicazione per malware e altri contenuti dannosi.
5. Verifica che il contenuto visualizzato corrisponda alla descrizione dell'app.
6. Verificare che le operazioni relative alle app funzionino come previsto in Power BI. Il team apre report e dashboard con dati di esempio, si connette a origini dati personalizzate, aggiorna i dati e così via.

Se identifica eventuali problemi, il team di certificazione fornisce il feedback appropriato.  Per altre informazioni sui requisiti dell'app Power BI, vedere la [documentazione dell'app Power BI.](https://go.microsoft.com/fwlink/?linkid=2028636)


## <a name="next-steps"></a>Passaggi successivi

È consigliabile monitorare regolarmente l'app nel [marketplace AppSource.](https://appsource.microsoft.com)  Dovresti anche usare la funzionalità [Approfondimenti sul venditore](../../cloud-partner-portal-orig/si-getting-started.md) del [portale Cloud Partner](https://cloudpartner.azure.com/#insights) per ottenere informazioni dettagliate sui clienti del marketplace e sull'utilizzo delle app. Infine, è possibile [aggiornare l'offerta](./cpp-update-existing-offer.md).
