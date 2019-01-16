---
title: Pubblicare la propria offerta di macchina virtuale su Azure Marketplace
description: Pubblicare la propria offerta di macchina virtuale su Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ad22f1944d3fe9a088b66da4cf4df7136db497f7
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075339"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Pubblicare la propria offerta di macchina virtuale su Azure Marketplace
=========================================================

Dopo aver inserito tutti i dettagli dell'offerta, è il momento di pubblicarla e renderla attiva su Azure Marketplace. Un'offerta deve attraversare alcune fasi prima di essere pubblicata sul sito Web, per garantire che sia il contenuto di marketing sia l'immagine della macchina virtuale soddisfino i requisiti di qualità necessari per conseguire la certificazione Azure.

![Sequenza 0 di pubblicazione dell'offerta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

Questo processo verrà analizzato in modo dettagliato per comprendere al meglio ciò che avviene durante ogni passaggio. In questo processo, è necessario intervenire per garantire che l'offerta continui ad avanzare.

<a name="publishing-process"></a>Processo di pubblicazione
------------------

Fare clic su "Pubblica" nella scheda Editor per avviare il processo di pubblicazione.

![Sequenza 1 di pubblicazione dell'offerta - Pubblica](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

Nella scheda Stato verranno visualizzati i passaggi necessari per la pubblicazione e la fase del processo in cui si trova l'offerta.

![Sequenza 2 di pubblicazione dell'offerta - Stato](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

In qualsiasi momento del processo di pubblicazione è anche possibile accedere e fare clic sulla scheda All Offers (Tutte le offerte) per visualizzare lo stato più recente di tutte le offerte pubblicate. È possibile fare clic direttamente sullo stato dell'offerta e visualizzare i dettagli relativi alla fase del processo di pubblicazione in cui si trova.

![Sequenza 3 di pubblicazione dell'offerta - Stato di tutte le offerte](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

Verranno ora esaminati i singoli passaggi di pubblicazione, illustrando cosa accade a ogni passaggio con una stima del tempo previsto per la sua esecuzione.

**Convalidare i prerequisiti (\<1 giorno)**

Quando si fa clic su "Pubblica", viene eseguita una verifica automatica per garantire che siano stati compilati tutti i campi obbligatori dell'offerta. Se non sono stati compilati tutti i campi, viene visualizzato un avviso accanto al campo, che dovrà essere compilato in modo accurato prima di fare nuovamente clic su "Pubblica".

Dopo aver completato questo passaggio, viene visualizzato un popup in cui si richiede di specificare un indirizzo di posta elettronica. Si tratta dell'indirizzo al quale verranno inviate le notifiche sullo stato di pubblicazione per la parte rimanente del processo. Una volta inviato l'indirizzo di posta elettronica, questo passaggio è completo.

![Sequenza 4 di pubblicazione dell'offerta - Pubblicare l'offerta](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**Certificazione (\<5 giorni)**

Questo passaggio prevede l'esecuzione di più test per verificare che l'immagine della macchina virtuale soddisfi i requisiti richiesti per ottenere la certificazione per Azure. Tutte le linee guida necessarie per assicurarsi di soddisfare i requisiti di certificazione sono disponibili [qui](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md).

Poiché questo passaggio può richiedere diversi giorni, è possibile uscire dal portale Cloud Partner. L'utente riceverà una notifica tramite posta elettronica se sono presenti errori che è necessario risolvere. Se tutte le fasi vengono eseguite correttamente, il processo passa automaticamente alla fase del Provisioning.

**Provisioning (\<1 giorno)**

Durante questa fase, il cui completamento può richiedere fino a un giorno, si replicano le immagini per tutti i centri dati globali di Azure.

**Creazione di pacchetti e registrazione alla lead generation (\<1 ora)**

Durante questa fase i contenuti tecnici e di marketing vengono combinati in quella che sarà la pagina del prodotto sul sito Web.

Inoltre, se è stata configurata la funzionalità lead generation, si verificherà il funzionamento dell'integrazione CRM inviando un lead di test al CRM. Al completamento di questo passaggio, un record con dati fittizi popolerà il CRM o la tabella di Azure. Tutta la documentazione relativa alla funzionalità lead generation è disponibile [qui](./cloud-partner-portal-get-customer-leads.md).

**Offerta disponibile in anteprima**

L'utente riceve una e-mail di notifica relativa al completamento dei passaggi richiesti per accedere all'offerta in anteprima. Durante questo passaggio, è consigliabile visualizzare l'anteprima dell'offerta per assicurarsi che tutto funzioni come dovrebbe e che la macchina virtuale distribuisca correttamente i servizi nell'ambiente di gestione temporanea.

*Solo le sottoscrizioni consentite possono eseguire questa verifica.*

**Signoff dell'autore**

Dopo aver verificato che tutto funziona correttamente, si può procedere con la pubblicazione. Fare clic su **Go Live** nella scheda **Stato** per dare inizio ai passaggi necessari per attivare l'offerta nell'ambiente di produzione e nel sito Web. In genere potrebbero trascorrere diverse ore dal momento in cui si fa clic su Go Live a quando l'offerta è disponibile sul sito Web. L'utente riceverà una notifica e-mail quando l'offerta sarà ufficialmente disponibile sul sito Web.

![Sequenza 5 di pubblicazione dell'offerta - Offerta pubblicata](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**Offerta pubblicata**

L'offerta è ora disponibile nel portale di Azure e in Azure Marketplace e i clienti saranno in grado di visualizzare e distribuire la macchina virtuale nelle proprie sottoscrizioni di Azure. In qualsiasi momento è possibile fare clic sulla scheda All offers (Tutte le offerte) e visualizzare lo stato per tutte le offerte elencate nella colonna di destra. È possibile fare clic sullo stato per vedere lo stato del flusso di pubblicazione nei dettagli dell'offerta.

<a name="error-handling"></a>Gestione degli errori
--------------

Durante il processo di pubblicazione può essere rilevato un errore. In questo caso, si riceve una notifica tramite posta elettronica in cui si informa l'utente che si è verificato un errore e si forniscono le istruzioni sui passaggi successivi. È inoltre possibile vedere gli errori in qualsiasi momento durante il processo facendo clic sulla scheda Stato. Verrà visualizzato il punto del processo in cui si è verificato l'errore insieme a un messaggio che indica qual è il problema da risolvere.

![Sequenza 6 di pubblicazione dell'offerta - Messaggio di errore](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

Se si verificano errori durante il processo di pubblicazione, verrà richiesto di risolverli e quindi di fare clic su 'Pubblica' per riavviare il processo. Quando si esegue nuovamente la pubblicazione dopo eventuali correzioni, è necessario ricominciare dai passaggi iniziali della fase di pubblicazione Convalidare i prerequisiti.

Se si verificano problemi nella risoluzione di un errore, è necessario aprire una richiesta di supporto per ottenere assistenza dai tecnici del supporto di Azure.

![Sequenza 7 di pubblicazione dell'offerta - Supporto tecnico](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>Annullamento della richiesta di pubblicazione
--------------------------------

Può accadere che dopo avere avviato il processo di pubblicazione sia necessario annullare la richiesta. È possibile annullare una richiesta di pubblicazione solo dopo avere completato il passaggio di signoff dell'editore. Per annullare, fare clic su "Annulla pubblicazione". Verrà ripristinato lo stato di pubblicazione del passaggio 1 e per eseguire nuovamente la pubblicazione sarà necessario fare clic su Pubblica e seguire i passaggi nello stato.

![Sequenza 8 di pubblicazione dell'offerta - Stato](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

