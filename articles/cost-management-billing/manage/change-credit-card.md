---
title: Cambiare la carta di credito per Azure
description: Questo articolo descrive come cambiare la carta di credito usata per pagare una sottoscrizione di Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: banders
ms.openlocfilehash: 0f1b4751ae2448b519387d57e4dbfa1eecf8a576
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984284"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Aggiungere o aggiornare una carta di credito per Azure

Questo documento si applica ai clienti che hanno usato una carta di credito per iscriversi ad Azure Online.

Nel portale di Azure è possibile cambiare il metodo di pagamento predefinito impostando una nuova carta di credito e aggiornare i dati della carta di credito. Per apportare queste modifiche, è necessario essere un [amministratore account](billing-subscription-transfer.md#whoisaa).

Se si vuole eliminare una carta di credito, vedere [Eliminare un metodo di pagamento delle fatture di Azure](delete-azure-payment-method.md).

I metodi di pagamento supportati per Microsoft Azure sono carta di credito e assegno o bonifico. Per ottenere l'approvazione per il pagamento tramite assegno o bonifico, vedere [Pagare le sottoscrizioni di Azure tramite fattura](pay-by-invoice.md).

Con un Contratto del cliente Microsoft, i metodi di pagamento sono associati ai profili di fatturazione. Vedere [Verificare l'accesso a un Contratto del cliente Microsoft](#check-the-type-of-your-account). Se si ha un contratto del cliente Microsoft, passare a [Gestire le carte di credito per un contratto del cliente Microsoft](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Gestire le carte di credito per una sottoscrizione di Azure

Le sezioni seguenti sono valide per i clienti con un account di fatturazione del Programma di Microsoft Online Services. Vedere [Verificare il tipo di account in uso](#check-the-type-of-your-account). Se si ha un tipo di account di fatturazione del Programma di Microsoft Online Services, i metodi di pagamento sono associati alle singole sottoscrizioni di Azure. Se si verifica un errore dopo aver aggiunto la carta di credito, vedere [La carta di credito viene rifiutata al momento dell'iscrizione ad Azure](/troubleshoot-declined-card.md).

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Cambiare la carta di credito per una sottoscrizione aggiungendo una nuova carta di credito

È possibile cambiare la carta di credito predefinita della sottoscrizione di Azure con una nuova carta di credito o una carta di credito precedentemente salvata nel portale di Azure. Per cambiare carta di credito è necessario essere amministratore account. Se più sottoscrizioni hanno lo stesso metodo di pagamento attivo, cambiando il metodo di pagamento attivo in una di queste sottoscrizioni viene aggiornato anche il metodo di pagamento attivo nelle altre sottoscrizioni.

Per cambiare la carta di credito predefinita della sottoscrizione con una nuova carta, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.  
    ![Screenshot che mostra la ricerca](./media/change-credit-card/search.png)
1. Selezionare la sottoscrizione a cui si vuole aggiungere la carta di credito.
1. Selezionare **Metodi di pagamento**.  
    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata](./media/change-credit-card/payment-methods-blade-x.png)
1. Nell'angolo in alto a sinistra selezionare "+" per aggiungere una carta. A destra verrà visualizzato un modulo per la carta di credito.
1. Immettere i dettagli della carta di credito.  
    ![Screenshot che mostra l'aggiunta di una nuova carta](./media/change-credit-card/sub-add-new-x.png)
1. Per impostare la carta come metodo di pagamento attivo, selezionare la casella accanto a **Usa come metodo di pagamento attivo** sopra al modulo. Questa carta diventerà lo strumento di pagamento attivo per tutte le sottoscrizioni che usano la stessa carta della sottoscrizione selezionata.
1. Selezionare **Avanti**.

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Cambiare la carta di credito per una sottoscrizione in una carta di credito salvata in precedenza

Si può anche cambiare la carta di credito predefinita della sottoscrizione con una carta già salvata nell'account seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.  
    ![Screenshot che mostra la ricerca](./media/change-credit-card/search.png)
1. Selezionare la sottoscrizione a cui si vuole aggiungere la carta di credito.
1. Selezionare **Metodi di pagamento**.
    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata](./media/change-credit-card/payment-methods-blade-x.png)
1. Selezionare la casella accanto alla carta da impostare come metodo di pagamento attivo.
1. Selezionare **Imposta come attivo**.
    ![Screenshot che mostra la carta di credito selezionata e impostata coma attiva](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Modificare i dettagli della carta di credito

Se la carta di credito viene rinnovata e il numero rimane invariato, aggiornare i dati della carta di credito esistente, ad esempio la data di scadenza. Se il numero di carta di credito viene modificato in seguito alla perdita, al furto o alla scadenza della carta, seguire la procedura nella sezione [Aggiungere una carta di credito come metodo di pagamento](#addcard). Non è necessario aggiornare il codice CVV.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.
    ![Screenshot che mostra la ricerca](./media/change-credit-card/search.png)
1. Selezionare **Metodi di pagamento**.
    ![Screenshot che mostra l'opzione Gestisci i metodi di pagamento selezionata](./media/change-credit-card/payment-methods-blade-x.png)
1. Selezionare la carta di credito da modificare. A destra verrà visualizzato un modulo per la carta di credito.
    ![Screenshot che mostra la carta di credito selezionata](./media/change-credit-card/edit-card-x.png)
1. Aggiornare i dettagli della carta di credito.
1. Selezionare **Salva**.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Gestire le carte di credito per un contratto del cliente Microsoft

Le sezioni seguenti sono valide per i clienti che hanno un contratto del cliente Microsoft e hanno effettuato l'iscrizione ad Azure online con una carta di credito. [Come verificare se si ha un contratto del cliente Microsoft](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Cambiare la carta di credito predefinita

Se si ha un contratto del cliente Microsoft, la carta di credito è associata a un profilo di fatturazione. Il metodo di pagamento per un profilo di fatturazione può essere cambiato solo dalla persona che ha effettuato l'iscrizione ad Azure e ha creato l'account di fatturazione.

Se si vuole cambiare il metodo di pagamento predefinito del profilo di fatturazione in modo da pagare tramite assegno o bonifico, vedere [Pagare la sottoscrizione di Azure tramite fattura](pay-by-invoice.md).

Per cambiare la carta di credito, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Eseguire ricerche in **Gestione dei costi e fatturazione**.
1. Nel menu a sinistra selezionare **Profili di fatturazione**.
1. Selezionare un profilo di fatturazione.
1. Nel menu a sinistra selezionare **Metodi di pagamento**.  
   ![Screenshot che mostra i metodi di pagamento nel menu](./media/change-credit-card/payment-methods-tab-mca.png)
1. Nella sezione **Metodo di pagamento predefinito** selezionare **Sostituisci**.  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="Screenshot che mostra l'opzione Sostituisci" :::
1. Nella nuova area a destra selezionare una carta esistente dall'elenco a discesa o aggiungerne una nuova selezionando il collegamento blu **Aggiungi un nuovo metodo di pagamento**.

### <a name="edit-a-credit-card"></a>Modificare una carta di credito

È possibile modificare i dettagli della carta di credito, ad esempio aggiornando la data di scadenza, nel portale di Azure. 

Per modificare una carta di credito, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Eseguire ricerche in **Gestione dei costi e fatturazione**.
1. Nel menu a sinistra selezionare **Profili di fatturazione**.
1. Selezionare un profilo di fatturazione.
1. Nel menu a sinistra selezionare **Metodi di pagamento**.  
   ![Screenshot che mostra i metodi di pagamento nel menu](./media/change-credit-card/payment-methods-tab-mca.png)
1. Nella sezione **Carte di credito** trovare la carta di credito da modificare.
1. Selezionare i puntini di sospensione (`...`) alla fine della riga.  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="Screenshot che mostra l'opzione Sostituisci" :::
1. Per modificare i dettagli della carta di credito, scegliere **Modifica** dal menu di scelta rapida.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Azure non supporta le carte virtuali o prepagate. Se si ricevono messaggi di errore durante l'aggiunta o l'aggiornamento di una carta di credito valida, provare ad aprire il browser in modalità privata.

## <a name="frequently-asked-questions"></a>Domande frequenti

Le sezioni seguenti contengono le risposte alle domande frequenti sulla modifica delle informazioni sulla carta di credito.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Perché continua a essere visualizzato il messaggio "La sessione è scaduta. Per continuare, accedere di nuovo"?

Se si continua a ricevere questo messaggio anche dopo aver effettuato la disconnessione e un nuovo accesso, riprovare con una sessione anonima del browser.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Come si usa una carta diversa per ogni sottoscrizione?

Se le sottoscrizioni usano già la stessa carta, sfortunatamente non è possibile separarle in modo da usare carte diverse. Tuttavia, quando ci si iscrive per una nuova sottoscrizione, è possibile scegliere di usare un nuovo metodo di pagamento per la sottoscrizione.

### <a name="how-do-i-make-payments"></a>Come si effettuano i pagamenti?

Se il metodo di pagamento è configurato per l'uso di una carta di credito, il pagamento viene addebitato automaticamente sulla carta al termine di ogni periodo di fatturazione. Non è necessario intervenire manualmente.

Se si usa l'opzione di [pagamento con fattura](pay-by-invoice.md), inviare il pagamento al destinatario elencato in fondo alla fattura.

### <a name="how-do-i-change-the-tax-id"></a>Come è possibile modificare l'ID imposta?

Per aggiungere o aggiornare l'ID imposta, aggiornare il profilo nel [portale di Azure](https://portal.azure.com) e quindi selezionare **Registrazione fiscale**. L'ID imposta viene usato per calcolare l'esenzione fiscale ed riportato sulla fattura.

## <a name="check-the-type-of-your-account"></a>Verificare il tipo di account in uso

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni sulle [prenotazioni di Azure](../reservations/save-compute-costs-reservations.md) per stabilire se consentono di risparmiare.
- Se si vuole eliminare una carta di credito, vedere [Eliminare un metodo di pagamento delle fatture di Azure](delete-azure-payment-method.md).