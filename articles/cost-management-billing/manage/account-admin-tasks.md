---
title: Attività di amministratore account nel portale di Azure
description: Descrive come eseguire operazioni di pagamento nel portale di Azure
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contentperfq2
ms.openlocfilehash: c9f344ec6eae40db2c76f3712df6f1f9d8cead37
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668871"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Attività di amministratore account nel portale di Azure

Questo articolo illustra come eseguire le attività seguenti nel portale di Azure:
- Gestire i metodi di pagamento della sottoscrizione
- Rimuovere il limite di spesa per la sottoscrizione
- Aggiungere crediti alla sottoscrizione di Azure in Open

Per eseguire una di queste attività, è necessario avere il ruolo di amministratore account.

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Passare ai metodi di pagamento della sottoscrizione

1. Accedere al portale di Azure come amministratore account.

1. Cercare **Gestione dei costi e fatturazione** .

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione ](./media/account-admin-tasks/search-bar.png)

1. Nell'elenco **Sottoscrizioni personali** selezionare la sottoscrizione a cui si vuole aggiungere la carta di credito.

   ![Screenshot che illustra la pagina Gestione dei costi e fatturazione in cui è possibile selezionare una sottoscrizione.](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > Se non vengono visualizzate alcune sottoscrizioni, è possibile che la directory della sottoscrizione sia stata modificata in un determinato momento. Per queste sottoscrizioni, è necessario passare alla directory originale (la directory in cui è stata effettuata l'iscrizione iniziale). Quindi, ripetere il passaggio 2.

1. Selezionare **Metodi di pagamento** .

    ![Screenshot che illustra la pagina Metodi di pagamento in cui è possibile aggiungere un metodo di pagamento.](./media/account-admin-tasks/subscription-payment-methods-blade.png)

Qui è possibile aggiungere una nuova carta di credito, modificare il metodo di pagamento attivo, modificare i dettagli della carta di credito ed eliminare carte di credito.

### <a name="change-active-payment-method"></a>Modificare il metodo di pagamento attivo

È possibile modificare il metodo di pagamento attivo aggiungendo una nuova carta di credito o scegliendone una già salvata. Per modificare il metodo di pagamento attivo impostando una nuova carta di credito:

1. Nell'angolo in alto a sinistra selezionare "+" per aggiungere una carta di credito.

    ![Screenshot che mostra il segno più](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Immettere i dettagli della carta di credito nel modulo a destra.

    ![Screenshot che mostra il modulo per l'aggiunta di una carta di credito.](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Per impostare la carta come metodo di pagamento attivo, selezionare la casella accanto a **Usa come metodo di pagamento attivo** sopra al modulo. Questa carta diventerà lo strumento di pagamento attivo per tutte le sottoscrizioni che usano la stessa carta della sottoscrizione selezionata.

    ![Screenshot che mostra la casella di controllo per impostare la carta come metodo di pagamento attivo.](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Selezionare **Avanti** .

Per modificare il metodo di pagamento attivo impostando una carta di credito già salvata:

1. Selezionare la casella accanto alla carta da impostare come metodo di pagamento attivo.

    ![Screenshot che mostra la casella selezionata accanto alla carta di credito](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Fare clic su **Imposta come attivo** sulla barra dei comandi.

    ![Screenshot che mostra il pulsante Imposta come attivo](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Modificare i dettagli della carta di credito

Per modificare i dettagli della carta di credito, ad esempio l'indirizzo o la data di scadenza, fare clic sulla carta di credito che si vuole modificare. A destra verrà visualizzato un modulo per la carta di credito.

![Screenshot che mostra la carta di credito selezionata](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Aggiornare i dettagli della carta di credito e fare clic su **Salva** .

### <a name="remove-a-credit-card-from-the-account"></a>Rimuovere una carta di credito dall'account

1. Selezionare la casella accanto alla carta che si vuole eliminare.

    ![Screenshot che mostra la casella selezionata accanto alla carta di credito](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Fare clic su **Elimina** sulla barra dei comandi.

    ![Screenshot che mostra il pulsante Elimina](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Se la carta di credito è il metodo di pagamento attivo per altre sottoscrizioni Microsoft, non è possibile rimuoverla dal proprio account Azure. Cambiare il metodo di pagamento attivo per tutte le sottoscrizioni collegate a questa carta di credito e riprovare.

### <a name="switch-to-invoice-payment"></a>Passare al pagamento con fattura

Se si è idonei al pagamento con fattura (tramite assegno o bonifico), è possibile passare all'apposita sottoscrizione nel portale di Azure.

1. Selezionare **Pagamento con fattura** sulla barra dei comandi.

    ![Screenshot che illustra la pagina Metodi di pagamento con l'opzione Pagamento con fattura selezionata.](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Immettere l'indirizzo per il metodo di pagamento con fattura.
1. Fare clic su **Avanti** .

Per ricevere l'approvazione di un pagamento con fattura, vedere l'articolo contenente [informazioni sui pagamenti con fattura](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Modificare l'indirizzo di fatturazione

Per modificare l'indirizzo associato al metodo di pagamento con fattura, fare clic su **Fattura** nell'elenco dei metodi di pagamento per la sottoscrizione. A destra verrà visualizzato il modulo dell'indirizzo.

## <a name="remove-spending-limit"></a>Rimuovere il limite di spesa

Il limite di spesa in Azure impedisce spese superiori all'importo del credito. È possibile rimuovere il limite di spesa in qualsiasi momento, purché alla sottoscrizione di Azure sia associato un metodo di pagamento valido. Per i tipi di sottoscrizione con credito su più mesi, ad esempio Visual Studio Enterprise e Visual Studio Professional, è anche possibile scegliere di riabilitare il limite di spesa all'inizio del periodo di fatturazione successivo.

Il limite di spesa non è disponibile per le sottoscrizioni con piani di impegno e piani che prevedono prezzi con pagamento in base al consumo.

1. Accedere al portale di Azure come amministratore account.
1. Cercare **Gestione dei costi e fatturazione** .

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione ](./media/account-admin-tasks/search-bar.png)

1. Nell'elenco **Sottoscrizioni personali** selezionare la sottoscrizione di Visual Studio Enterprise.

   ![Screenshot che illustra l'area Sottoscrizioni personali in cui è possibile selezionare la sottoscrizione di Visual Studio Enterprise.](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Se non vengono visualizzate alcune sottoscrizioni di Visual Studio, è possibile che la directory della sottoscrizione sia stata modificata in un determinato momento. Per queste sottoscrizioni, è necessario passare alla directory originale (la directory in cui è stata effettuata l'iscrizione iniziale). Quindi, ripetere il passaggio 2.

1. Nella visualizzazione panoramica della sottoscrizione fare clic sul banner arancione per rimuovere il limite di spesa.

    ![Screenshot che mostra il banner per la rimozione del limite di spesa](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Scegliere se si vuole rimuovere il limite di spesa per un periodo illimitato o solo per il periodo di fatturazione corrente.

   ![Screenshot che mostra il pannello per la rimozione del limite di spesa](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Fare clic su **Selezionare un metodo di pagamento** per scegliere un metodo di pagamento per la sottoscrizione. Questo diventerà il metodo di pagamento attivo per la sottoscrizione.

1. Fare clic su **Fine** .

## <a name="add-credits-to-azure-in-open-subscription"></a>Aggiungere crediti alla sottoscrizione di Azure in Open

Se si dispone di una sottoscrizione di Licenze Azure in Open, è possibile aggiungere crediti alla sottoscrizione nel portale di Azure riscattando un codice Product Key o acquistando crediti con una carta di credito.

1. Accedere al portale di Azure come amministratore account.
1. Cercare **Gestione dei costi e fatturazione** .

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione ](./media/account-admin-tasks/search-bar.png)

1. Nell'elenco **Sottoscrizioni personali** selezionare la sottoscrizione di Azure in Open.

    ![Screenshot che illustra l'area Sottoscrizioni personali in cui è possibile selezionare la sottoscrizione di Azure in Open.](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > Se non vengono visualizzate le proprie sottoscrizioni, è possibile che la relativa directory sia stata modificata in un determinato momento. È necessario passare alla directory originale della sottoscrizione (la directory in cui è stata effettuata l'iscrizione iniziale). Quindi, ripetere il passaggio 2.

1. Selezionare **Cronologia crediti** .

    ![Screenshot che mostra la cronologia dei crediti](./media/account-admin-tasks/aio-credit-history-blade.png)

1. Nell'angolo in alto a sinistra selezionare "+" per aggiungere altri crediti.

    ![Screenshot che mostra il pulsante per l'aggiunta di crediti](./media/account-admin-tasks/aio-credit-history-plus.png)

1. Selezionare un tipo di metodo di pagamento dall'elenco a discesa. È possibile aggiungere un codice Product Key o acquistare crediti con una carta di credito.

    ![Screenshot che mostra l'elenco a discesa dei metodi di pagamento nel pannello Aggiungi crediti](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. Se si sceglie il codice Product Key:
    - Immettere il codice Product Key.
    - Fare clic su **Convalida** .

1. Se si sceglie la carta di credito:
    - Fare clic su **Selezionare un metodo di pagamento** per aggiungere una carta di credito o selezionarne una esistente.
    - Specificare la quantità di crediti da aggiungere.

1. Fare clic su **Applica** .

## <a name="troubleshooting"></a>risoluzione dei problemi
Non sono supportate le carte virtuali o prepagate. Se si ricevono messaggi di errore durante l'aggiunta o l'aggiornamento di una carta di credito valida, provare ad aprire il browser in modalità privata.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'[analisi di addebiti imprevisti](../understand/analyze-unexpected-charges.md)
