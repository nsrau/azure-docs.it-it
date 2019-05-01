---
title: I passaggi di pubblicazione di App | Azure Marketplace
description: Passaggi per pubblicare un'applicazione in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pabutler
ms.openlocfilehash: 04278f50366ee91738fd36e64331572e14baf17c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935173"
---
# <a name="app-publishing-steps"></a>Procedura per la pubblicazione dell'app

Per avviare il processo di pubblicazione, è necessario fare clic su "Pubblica" sotto la scheda Editor.

![Pulsante di pubblicazione dell'app CCP](./media/d365-financials/image014.jpg)


Nella scheda Stato verranno visualizzati i passaggi di pubblicazione che indicheranno la fase del processo in cui si trova l'offerta. In qualsiasi momento del processo di pubblicazione è anche possibile accedere e fare clic sulla scheda All Offers (Tutte le offerte) per visualizzare lo stato più recente di tutte le offerte pubblicate. È possibile fare clic direttamente sullo stato dell'offerta e visualizzare i dettagli relativi alla fase del processo di pubblicazione in cui si trova.

Verranno ora esaminati i singoli passaggi di pubblicazione, illustrando cosa accade a ogni passaggio con una stima del tempo previsto per la sua esecuzione.

![Diagramma del processo di pubblicazione](./media/d365-financials/image017.png)


**Abilitare i prerequisiti**

Quando si fa clic su "Pubblica", viene eseguita una verifica automatica per garantire che siano stati compilati tutti i campi obbligatori dell'offerta. Se non sono stati compilati tutti i campi, viene visualizzato un avviso accanto al campo, che dovrà essere compilato in modo accurato prima di fare nuovamente clic su "Pubblica".

Dopo aver completato questo passaggio, viene visualizzata una finestra in cui si richiede di specificare un indirizzo di posta elettronica per l'invio delle notifiche di pubblicazione. Una volta inviato l'indirizzo di posta elettronica, questo passaggio è completo.


**Convalida dell'applicazione automatizzata**

In questo passaggio, il nostro servizio di certificazione automatizzata verifica le estensioni dell'applicazione fornite con un'offerta che il relativo contenuto allinea con i metadati dell'offerta. Assicurarsi sempre che nome dell'app, versione, editore e ID corrispondano con quelli specificati nel manifesto di estensione denominato `app.json`.


**Convalida del Test Drive**

Se si è scelto di impostare il Test Drive, questa è la fase in cui vengono convalidate le impostazioni di Test Drive.


**Convalida di gestione dei lead e registrazione**

In questa fase, se è stata configurata la funzionalità Lead Generation, si verificherà il funzionamento dell'integrazione CRM inviando un lead di test al CRM. Al completamento di questo passaggio, un record con dati fittizi popolerà il CRM o la tabella di Azure. Tutta la documentazione relativa alla funzionalità lead generation è disponibile qui.


**Creazione pacchetto di AppSource**

Gli artefatti di dettagli Vetrina vengono controllati e viene generato il pacchetto di anteprima di AppSource.


**Disconnessione del server di pubblicazione**

Durante questa fase, il pulsante **Go Live** diventa attivo. Sarà disponibile anche un collegamento per visualizzare l'anteprima dell'offerta (con hidekey). Quando si è soddisfatti dell'anteprima, fare clic sul pulsante Go Live.
Tenere presente che questa richiesta non esegue l'app in tempo reale in AppSource, ma attiva invece il processo di convalida interno.


**Convalida tecnica e di marketing dell'applicazione**

Questo passaggio è dove si svolgono la convalida di marketing e tecnica in parallelo. Fare riferimento al materiale sussidiario [Elenco di controllo per l'invio dell'App](https://aka.ms/CheckBeforeYouSubmit) e [Sviluppo di App per Dynamics 365 per Finanza e operazioni white paper](https://go.microsoft.com/fwlink/?linkid=841518) per i requisiti obbligatori e gli elementi consigliati. Durante il processo di convalida:
-  si lavorerà su eventuali problemi e domande in sospeso.  
- si fornirà una data di pubblicazione dell'app e verrà inviata una notifica a pubblicazione avvenuta. 
- si fornirà un primo feedback relativo alla convalida tecnica e di marketing entro 5-7 giorni lavorativi.

Questi passaggi richiedono, in genere, più di una settimana e non è necessario rimanere costantemente connessi al portale Cloud Partner.


**Pubblicare applicazioni con il servizio**

L'offerta attraversa la fase di elaborazione finale. L'app ha superato la convalida sia tecnica che di marketing, ma ora deve attraversare alcune attività di elaborazione per far sì che sia pronta per l'AppSource.


**Offerta pubblicata**

L'offerta è ora pubblicata nell'AppSource e i clienti potranno visualizzare e distribuire l'app nelle proprie sottoscrizioni di Microsoft Dynamics 365 Business Central. Si riceverà un messaggio e-mail da Microsoft che informa dell'avvenuta pubblicazione dell'app su AppSource. In qualsiasi momento è possibile fare clic sulla scheda All offers (Tutte le offerte) e visualizzare lo stato per tutte le offerte elencate nella colonna di destra. È possibile fare clic sullo stato per vedere lo stato del flusso di pubblicazione nei dettagli dell'offerta.


<a name="error-handling"></a>Gestione degli errori
--------------

Durante il processo di pubblicazione può essere rilevato un errore. In questo caso, si riceve una notifica tramite posta elettronica in cui si informa l'utente che si è verificato un errore e si forniscono le istruzioni sui passaggi successivi. È inoltre possibile vedere gli errori in qualsiasi momento durante il processo facendo clic sulla scheda Stato. Verrà visualizzato il punto del processo in cui si è verificato l'errore insieme a un messaggio che indica qual è il problema da risolvere.

Se si verificano errori durante il processo di pubblicazione, verrà richiesto di risolverli e quindi di fare clic su **Pubblica** per riavviare il processo. Quando si esegue nuovamente la pubblicazione dopo eventuali correzioni, è necessario ricominciare dai passaggi iniziali della fase di pubblicazione **Convalidare i prerequisiti**.

Se si verificano problemi nella risoluzione di un errore, è necessario aprire una richiesta di supporto per ottenere assistenza dai tecnici del supporto di Azure.


<a name="canceling-the-publishing-request"></a>Annullamento della richiesta di pubblicazione
--------------------------------

Può accadere che dopo avere avviato il processo di pubblicazione sia necessario annullare la richiesta. È possibile annullare una richiesta di pubblicazione solo dopo avere completato il passaggio di signoff dell'autore. Per annullare, fare clic su **Cancel Publish** (Annulla pubblicazione). Verrà ripristinato lo stato di pubblicazione del passaggio 1 e per eseguire nuovamente la pubblicazione sarà necessario fare clic su Pubblica e seguire i passaggi nello stato.

![Pulsante di annullamento della pubblicazione](./media/d365-financials/image013.png)
