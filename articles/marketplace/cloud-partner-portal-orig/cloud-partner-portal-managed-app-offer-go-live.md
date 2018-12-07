---
title: Attivare l'offerta dell'applicazione Azure in Azure Marketplace | Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 11/15/2018
ms.author: pbutlerm
ms.openlocfilehash: 18a8e6ae8ab3bd4299c6a014f938e73a2a021492
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263589"
---
<a name="make-your-azure-application-offer-live-on-azure-marketplace"></a>Attivare l'offerta dell'applicazione Azure in Azure Marketplace 
===========================================================

Dopo aver completato tutti i dettagli dell'offerta, è possibile pubblicarla e attivarla in Azure Marketplace. Prima di attivare un'offerta nel sito Web, è necessario seguire alcuni passaggi, per garantire che sia il contenuto di marketing sia l'applicazione soddisfino i requisiti di qualità necessari per conseguire la certificazione Azure.

![Flusso di pubblicazione](./media/cloud-partner-portal-publish-managed-app/publish_flow.png)

Verrà ora analizzato questo processo in modo più dettagliato per comprendere meglio ciò che accade durante ogni passaggio e quali interventi attuare per garantire l'avanzamento dell'offerta.

<a name="publishing-process"></a>Processo di pubblicazione 
------------------

Per avviare il processo di pubblicazione, è necessario fare clic su \"Pubblica\" sotto la scheda **Editor**.

![Sequenza 1 di attivazione dell'offerta: pubblicazione](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish.png)

Nella scheda **Stato** verranno visualizzati i passaggi di pubblicazione e il passaggio in cui si trova l'offerta.

![Sequenza 2 di attivazione dell'offerta: workflow](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_workflow.png)

In qualsiasi momento del processo di pubblicazione è anche possibile accedere e fare clic sulla scheda **Tutte le offerte** per visualizzare lo stato più recente di tutte le offerte pubblicate. È possibile fare clic direttamente sullo stato dell'offerta e visualizzare i dettagli relativi alla fase del processo di pubblicazione in cui si trova.

> [!WARNING]
> Se l'attribuzione dell'utilizzo dei clienti non è abilitata, verrà visualizzato un messaggio di errore simile al seguente durante il processo di pubblicazione: "Attribuzione dell'utilizzo dei clienti partner di Azure mancante in uno o più modelli di Azure Resource Manager. Per risolvere il problema, aggiungere un GUID di rilevamento al modello di Azure Resource Manager nei pacchetti mainTemplate.json per il piano seguente: servicenow. Per informazioni dettagliate, vedere: http://aka.ms/customerusageattribution". 

Verranno ora esaminati i singoli passaggi di pubblicazione e verrà illustrato che cosa accade a ogni passaggio con una stima del tempo previsto per la sua esecuzione.

### <a name="validate-prerequisites-1-day"></a>Convalida dei prerequisiti (\<1 giorno) 

Quando si fa clic su \"Publish\" (Pubblica), viene eseguita una verifica automatica per garantire che siano stati compilati tutti i campi obbligatori dell'offerta. Se non sono stati compilati tutti i campi, viene visualizzato un avviso accanto al campo, che dovrà essere compilato in modo accurato prima di fare nuovamente clic su \'Publish\' (Pubblica).

Dopo aver completato questo passaggio, viene visualizzato un popup in cui si chiede di specificare un indirizzo di posta elettronica. Si tratta dell'indirizzo al quale verranno inviate le notifiche sullo stato di pubblicazione per la parte rimanente del processo. Una volta inviato l'indirizzo di posta elettronica, questo passaggio è completo.

![Sequenza 1 di attivazione dell'offerta: pubblicazione 1](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_publish1.png)

### <a name="certification-5-days"></a>Certificazione (\<5 giorni) 

Questo passaggio prevede l'esecuzione di più test per verificare che il pacchetto dell'applicazione Azure soddisfi i requisiti richiesti per ottenere la certificazione Azure.

Poiché questo passaggio può richiedere diversi giorni, è possibile disconnettersi dal portale Microsoft Cloud Partner. Verrà inviata una notifica tramite posta elettronica se sono presenti errori. Se tutte le fasi vengono eseguite correttamente, il processo passa automaticamente alla fase del Provisioning.

### <a name="packaging-and-lead-generation-registration-1-hour"></a>Creazione di pacchetti e registrazione alla generazione lead (\<1 ora) 

Durante questa fase i contenuti tecnici e di marketing vengono combinati in quella che sarà la pagina del prodotto sul sito Web.

### <a name="offer-available-in-preview"></a>Offerta disponibile in anteprima 

L'utente riceve una e-mail di notifica relativa al completamento dei passaggi richiesti per accedere all'offerta in anteprima. In questo passaggio l'offerta viene visualizzata in anteprima per verificare che tutto sia corretto. Controllare se la macchina virtuale è distribuita correttamente nell'ambiente di gestione temporanea.

Solo le sottoscrizioni consentite possono eseguire questa verifica.\*

### <a name="publisher-sign-out"></a>Disconnessione dell'editore 

Dopo aver verificato che tutto funziona correttamente, si può procedere con la pubblicazione. Fare clic su Go Live nella scheda dello stato per dare inizio ai passaggi necessari per attivare l'offerta nell'ambiente di produzione e nel sito Web. In genere potrebbero trascorrere diverse ore dal momento in cui si fa clic su Go Live a quando l'offerta è disponibile sul sito Web. L'utente riceverà una notifica e-mail quando l'offerta sarà ufficialmente disponibile sul sito Web.

![Sequenza 6 di attivazione dell'offerta: attivazione](./media/cloud-partner-portal-publish-managed-app/newOffer_managedApp_goLive.png)

### <a name="microsoft-review"></a>Revisione Microsoft 

Quando l'offerta è pronta ed è stato selezionato \"Go Live\", in Visual Studio Online verrà creato un sito di lavoro, in cui Microsoft revisionerà il codice del contenuto del pacchetto. La revisione del codice si baserà sui criteri/pratiche migliori da usare per creare modelli, suggerimenti e consigli per ottimizzare la creazione di risorse. Se il feedback prevede che l'editore apporti modifiche ai file, il processo di pubblicazione dovrà essere riattivato. La pubblicazione corrente verrà annullata e sarà necessario ripetere la pubblicazione dopo aver risolto i commenti del feedback.

### <a name="live"></a>Live

L'offerta è ora attiva in Azure Marketplace e nel portale di Azure. I clienti potranno visualizzare e distribuire l'applicazione gestita di Azure nelle sottoscrizioni di Azure. È possibile fare clic sulla scheda All offers (Tutte le offerte) e visualizzare lo stato di tutte le offerte elencate nella colonna di destra. È possibile fare clic sullo stato per vedere lo stato del flusso di pubblicazione nei dettagli dell'offerta.

### <a name="error-handling"></a>Gestione degli errori 

In caso di errore, verrà inviata una notifica tramite posta elettronica in cui si informa l'utente che si è verificato un errore, specificando le istruzioni sui passaggi successivi. È inoltre possibile vedere gli errori in qualsiasi momento durante il processo facendo clic sulla scheda Stato. Verrà visualizzato il punto del processo in cui si è verificato l'errore insieme a un messaggio che indica qual è il problema da risolvere.

Se si verificano errori durante il processo di pubblicazione, viene richiesto di risolverli prima di fare clic su \'Publish\' (Pubblica) per riavviare il processo. Quando si esegue nuovamente la pubblicazione dopo eventuali correzioni, è necessario ricominciare dai passaggi iniziali della fase di pubblicazione Convalidare i prerequisiti.

Se si verificano problemi nella risoluzione di un errore, è necessario aprire una richiesta di supporto per ottenere assistenza dai tecnici del supporto di Azure.

### <a name="canceling-the-publishing-request"></a>Annullamento della richiesta di pubblicazione

Può accadere che dopo avere avviato il processo di pubblicazione sia necessario annullare la richiesta. È possibile annullare una richiesta di pubblicazione solo dopo avere completato il passaggio di signoff dell'autore. Per annullare, fare clic su \'Cancel Publish\' (Annulla pubblicazione). Verrà ripristinato lo stato di pubblicazione del passaggio 1 e per eseguire nuovamente la pubblicazione sarà necessario fare clic su Pubblica e seguire i passaggi nello stato.
