---
title: Attività di amministratore account nel portale di Azure
description: Descrive come eseguire operazioni di pagamento nel portale di Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: banders
ms.openlocfilehash: 510765778166f007501cfcb21e242c539200ce2f
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73722143"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Attività di amministratore account nel portale di Azure

Questo articolo illustra come eseguire le attività seguenti nel portale di Azure:
- Gestire i metodi di pagamento della sottoscrizione
- Rimuovere il limite di spesa per la sottoscrizione
- Aggiungere crediti alla sottoscrizione di Azure in Open

Per eseguire una di queste attività, è necessario avere il ruolo di amministratore account. 

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Passare ai metodi di pagamento della sottoscrizione

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.
    
    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione ](./media/billing-payment-features-azure-portal/search-bar.png)

1. Nell'elenco **Sottoscrizioni personali** selezionare la sottoscrizione a cui si vuole aggiungere la carta di credito.
    ![Screenshot che mostra la griglia delle sottoscrizioni personali in visualizzazione panoramica](./media/billing-payment-features-azure-portal/cost-management-billing-overview-x.png)

1. Selezionare **Metodi di pagamento**.

    ![Screenshot che mostra il pannello dei metodi di pagamento selezionato.](./media/billing-payment-features-azure-portal/subscription-payment-methods-blade.png)

Qui è possibile aggiungere una nuova carta di credito, modificare il metodo di pagamento attivo, modificare i dettagli della carta di credito ed eliminare carte di credito. 

### <a name="change-active-payment-method"></a>Modificare il metodo di pagamento attivo

È possibile modificare il metodo di pagamento attivo aggiungendo una nuova carta di credito o scegliendone una già salvata. Per modificare il metodo di pagamento attivo impostando una nuova carta di credito:

1. Nell'angolo in alto a sinistra selezionare "+" per aggiungere una carta di credito.
    
    ![Screenshot che mostra il segno più](./media/billing-payment-features-azure-portal/subscription-payment-methods-plus.png)

1. Immettere i dettagli della carta di credito nel modulo a destra.

    ![Screenshot che mostra il modulo per l'aggiunta di una carta di credito.](./media/billing-payment-features-azure-portal/subscription-add-payment-method-x.png)

1. Per impostare la carta come metodo di pagamento attivo, selezionare la casella accanto a **Usa come metodo di pagamento attivo** sopra al modulo. Questa carta diventerà lo strumento di pagamento attivo per tutte le sottoscrizioni che usano la stessa carta della sottoscrizione selezionata.

    ![Screenshot che mostra la casella di controllo per impostare la carta come metodo di pagamento attivo.](./media/billing-payment-features-azure-portal/subscription-make-active-payment-method-x.png)

1. Selezionare **Avanti**.

Per modificare il metodo di pagamento attivo impostando una carta di credito già salvata:

1. Selezionare la casella accanto alla carta da impostare come metodo di pagamento attivo.

    ![Screenshot che mostra la casella selezionata accanto alla carta di credito](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-x.png)

1. Fare clic su **Imposta come attivo** sulla barra dei comandi.

    ![Screenshot che mostra il pulsante Imposta come attivo](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Modificare i dettagli della carta di credito

Per modificare i dettagli della carta di credito, ad esempio l'indirizzo o la data di scadenza, fare clic sulla carta di credito che si vuole modificare. A destra verrà visualizzato un modulo per la carta di credito.

![Screenshot che mostra la carta di credito selezionata](./media/billing-payment-features-azure-portal/subscription-edit-payment-method-x.png)

Aggiornare i dettagli della carta di credito e fare clic su **Salva**.

### <a name="remove-a-credit-card-from-the-account"></a>Rimuovere una carta di credito dall'account

1. Selezionare la casella accanto alla carta che si vuole eliminare.

    ![Screenshot che mostra la casella selezionata accanto alla carta di credito](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-x.png)

1. Fare clic su **Elimina** sulla barra dei comandi.

    ![Screenshot che mostra il pulsante Elimina](./media/billing-payment-features-azure-portal/subscription-checked-payment-method-delete.png)

Se la carta di credito è il metodo di pagamento attivo per altre sottoscrizioni Microsoft, non è possibile rimuoverla dal proprio account Azure. Cambiare il metodo di pagamento attivo per tutte le sottoscrizioni collegate a questa carta di credito e riprovare.

### <a name="switch-to-invoice-payment"></a>Passare al pagamento con fattura

Se si è idonei al pagamento con fattura (tramite assegno o bonifico), è possibile passare all'apposita sottoscrizione nel portale di Azure.

1. Selezionare **Pagamento con fattura** sulla barra dei comandi.

    ![Screenshot che mostra il pannello dei metodi di pagamento selezionato.](./media/billing-payment-features-azure-portal/subscription-payment-methods-pay-by-invoice.png)

1. Immettere l'indirizzo per il metodo di pagamento con fattura.
1. Fare clic su **Avanti**.

Per ricevere l'approvazione di un pagamento con fattura, vedere l'articolo contenente [informazioni sui pagamenti con fattura](billing-how-to-pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Modificare l'indirizzo di fatturazione

Per modificare l'indirizzo associato al metodo di pagamento con fattura, fare clic su **Fattura** nell'elenco dei metodi di pagamento per la sottoscrizione. A destra verrà visualizzato il modulo dell'indirizzo. 

## <a name="remove-spending-limit"></a>Rimuovere il limite di spesa

Il limite di spesa in Azure impedisce spese superiori all'importo del credito. È possibile rimuovere il limite di spesa in qualsiasi momento, purché alla sottoscrizione di Azure sia associato un metodo di pagamento valido. Per i tipi di sottoscrizione con credito su più mesi, ad esempio Visual Studio Enterprise e Visual Studio Professional, è anche possibile scegliere di riabilitare il limite di spesa all'inizio del periodo di fatturazione successivo.

Il limite di spesa non è disponibile per le sottoscrizioni con piani di impegno e piani che prevedono prezzi con pagamento in base al consumo. Vedere l'[elenco completo dei tipi di sottoscrizioni di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione ](./media/billing-payment-features-azure-portal/search-bar.png)

1. Nell'elenco **Sottoscrizioni personali** selezionare la sottoscrizione di Visual Studio Enterprise.
    
    ![Screenshot che mostra la griglia delle sottoscrizioni personali in visualizzazione panoramica](./media/billing-payment-features-azure-portal/cost-management-overview-msdn-x.png)

1. Nella visualizzazione panoramica della sottoscrizione fare clic sul banner arancione per rimuovere il limite di spesa.
    
    ![Screenshot che mostra il banner per la rimozione del limite di spesa](./media/billing-payment-features-azure-portal/msdn-remove-spending-limit-banner-x.png)

1. Scegliere se si vuole rimuovere il limite di spesa per un periodo illimitato o solo per il periodo di fatturazione corrente.

   ![Screenshot che mostra il pannello per la rimozione del limite di spesa](./media/billing-payment-features-azure-portal/remove-spending-limit-blade-x.png)

1. Fare clic su **Selezionare un metodo di pagamento** per scegliere un metodo di pagamento per la sottoscrizione. Questo diventerà il metodo di pagamento attivo per la sottoscrizione.

1. Fare clic su **Fine**.

## <a name="add-credits-to-azure-in-open-subscription"></a>Aggiungere crediti alla sottoscrizione di Azure in Open

Se si dispone di una sottoscrizione di Licenze Azure in Open, è possibile aggiungere crediti alla sottoscrizione nel portale di Azure riscattando un codice Product Key o acquistando crediti con una carta di credito.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione ](./media/billing-payment-features-azure-portal/search-bar.png)

1. Nell'elenco **Sottoscrizioni personali** selezionare la sottoscrizione di Azure in Open.
   
    ![Screenshot che mostra la griglia delle sottoscrizioni personali in visualizzazione panoramica](./media/billing-payment-features-azure-portal/cost-management-overview-aio-x.png)

1. Selezionare **Cronologia crediti**.
    
    ![Screenshot che mostra la cronologia dei crediti](./media/billing-payment-features-azure-portal/aio-credit-history-blade.png)

1. Nell'angolo in alto a sinistra selezionare "+" per aggiungere altri crediti.

    ![Screenshot che mostra il pulsante per l'aggiunta di crediti](./media/billing-payment-features-azure-portal/aio-credit-history-plus.png)

1. Selezionare un tipo di metodo di pagamento dall'elenco a discesa. È possibile aggiungere un codice Product Key o acquistare crediti con una carta di credito.
    
    ![Screenshot che mostra l'elenco a discesa dei metodi di pagamento nel pannello Aggiungi crediti](./media/billing-payment-features-azure-portal/add-credits-select-payment-method.png)

1. Se si sceglie il codice Product Key:
    - Immettere il codice Product Key.
    - Fare clic su **Convalida**.

1. Se si sceglie la carta di credito:
    - Fare clic su **Selezionare un metodo di pagamento** per aggiungere una carta di credito o selezionarne una esistente.
    - Specificare la quantità di crediti da aggiungere.

1. Fare clic su **Applica**.

## <a name="troubleshooting"></a>risoluzione dei problemi
Non sono supportate le carte virtuali o prepagate. Se si ricevono messaggi di errore durante l'aggiunta o l'aggiornamento di una carta di credito valida, provare ad aprire il browser in modalità privata.

## <a name="next-steps"></a>Passaggi successivi
- Vedere altre informazioni su come [analizzare i costi ed evitare addebiti imprevisti nel portale di Azure](billing-getting-started.md)
