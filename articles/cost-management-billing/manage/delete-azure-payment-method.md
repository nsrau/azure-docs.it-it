---
title: Eliminare un metodo di pagamento delle fatture di Azure
description: Questo articolo descrive come eliminare un metodo di pagamento usato da una sottoscrizione di Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/15/2020
ms.author: banders
ms.openlocfilehash: b26958037cf7fd752c6cf07078b4a627fed64b2b
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "91003116"
---
# <a name="delete-an-azure-billing-payment-method-preview"></a>Eliminare un metodo di pagamento delle fatture di Azure (anteprima)

Questo documento fornisce istruzioni per l'eliminazione di un metodo di pagamento, ad esempio una carta di credito, da tipi di sottoscrizioni di Azure diversi. È possibile eliminare un metodo di pagamento per:

- Contratto del cliente Microsoft
- Programma Sottoscrizione Microsoft Online, detto anche metodo di pagamento in base al consumo

Qualunque sia il tipo di sottoscrizione di Azure attivo, è necessario annullarlo per poter eliminare il metodo di pagamento associato.

La rimozione di un metodo di pagamento per altri tipi di sottoscrizione di Azure, come il Contratto Microsoft Partner e il Contratto Enterprise, non è supportata.

## <a name="delete-an-mca-payment-method"></a>Eliminare un metodo di pagamento per un Contratto del cliente Microsoft

Solo l'utente che ha creato l'account del Contratto del cliente Microsoft può eliminare un metodo di pagamento.

Per eliminare un metodo di pagamento per un Contratto del cliente Microsoft, seguire questa procedura.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com/.
1. Passare a **Gestione dei costi e fatturazione**.
1. Se necessario, selezionare un ambito di fatturazione.
1. Nell'elenco di menu a sinistra selezionare **Profili di fatturazione** in **Fatturazione**.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Screenshot di esempio con l'opzione Profili di fatturazione nel portale di Azure" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. Nell'elenco dei profili di fatturazione selezionare quello in cui si usa il metodo di pagamento.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Screenshot di esempio con l'opzione Profili di fatturazione nel portale di Azure" :::
1. Nell'elenco di menu a sinistra selezionare **Metodi di pagamento** in **Impostazioni**.
1. Nella pagina dei metodi di pagamento per il profilo di fatturazione viene visualizzata una tabella di metodi di pagamento nella sezione **Carte di credito**. Trovare la carta di credito da eliminare, selezionare i puntini di sospensione ( **...** ) e quindi selezionare **Elimina**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Screenshot di esempio con l'opzione Profili di fatturazione nel portale di Azure" :::
1. Viene visualizzata la pagina Elimina un metodo di pagamento. Azure verifica se il metodo di pagamento è in uso.
    - Se il metodo di pagamento non è in uso, l'opzione **Elimina** è abilitata. Selezionarla per eliminare le informazioni sulla carta di credito.
    - Se il metodo di pagamento è in uso, deve essere sostituito o scollegato. Continuare a leggere le sezioni seguenti, che spiegano come **scollegare** il metodo di pagamento usato dalla sottoscrizione.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Scollegare il metodo di pagamento usato da un profilo di fatturazione del Contratto del cliente Microsoft

Se il metodo di pagamento è usato da un profilo di fatturazione del Contratto del cliente Microsoft, verrà visualizzato un messaggio simile all'esempio seguente.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Screenshot di esempio con l'opzione Profili di fatturazione nel portale di Azure" :::

Per scollegare un metodo di pagamento devono essere soddisfatte alcune condizioni. Se una o più condizioni non vengono soddisfatte, vengono visualizzate le istruzioni che spiegano come soddisfarle. Viene anche visualizzato un collegamento che porta alla posizione in cui è possibile risolvere la condizione.

Quando tutte le condizioni sono soddisfatte, è possibile scollegare il metodo di pagamento dal profilo di fatturazione.

> [!NOTE]
> Una volta scollegato il metodo di pagamento predefinito, il profilo di fatturazione viene impostato su uno stato _inattivo_. Qualsiasi elemento eliminato in questo processo non potrà essere recuperato. Dopo avere impostato come inattivo un profilo di fatturazione, è necessario effettuare l'iscrizione per una nuova sottoscrizione di Azure per poter creare nuove risorse.

#### <a name="to-detach-a-payment-method"></a>Per scollegare un metodo di pagamento

1. Nell'area Elimina un metodo di pagamento selezionare il collegamento **Scollega il metodo di pagamento corrente**.
1. Se tutte le condizioni sono soddisfatte, selezionare **Scollega**. In caso contrario, continuare con il passaggio successivo.
1. Se l'opzione Scollega non è disponibile, viene visualizzato un elenco di condizioni. Eseguire le azioni elencate. Selezionare il collegamento visualizzato nell'area Scollega il metodo di pagamento predefinito. Ecco un esempio di un'azione correttiva che spiega le azioni che occorre eseguire.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Screenshot di esempio con l'opzione Profili di fatturazione nel portale di Azure" :::
1. Quando si seleziona il collegamento dell'azione correttiva, si viene reindirizzati alla pagina di Azure in cui occorre eseguire l'azione. Eseguire qualsiasi azione correttiva necessaria.
1. Se necessario, completare tutte le altre azioni correttive.
1. Tornare a **Gestione dei costi e fatturazione** > **Profili di fatturazione** > **Metodi di pagamento**. Selezionare **Scollega**. In fondo alla pagina Scollega il metodo di pagamento predefinito selezionare **Scollega**.

> [!NOTE]
> - Dopo aver annullato una sottoscrizione, la sua eliminazione può richiedere fino a 90 giorni. Se si vuole abbreviare l'attesa, aprire una richiesta di supporto di Azure e chiedere l'eliminazione immediata della sottoscrizione.
> - Prima di eliminare un metodo di pagamento occorre saldare tutti gli addebiti precedenti per un profilo di fatturazione. Se il periodo di fatturazione è attivo, prima di poter eliminare il metodo di pagamento è necessario attendere fino al termine del periodo di fatturazione. **Durante l'attesa del termine del periodo di fatturazione, verificare che tutte le altre condizioni di scollegamento siano soddisfatte**.

## <a name="delete-a-mosp-payment-method"></a>Eliminare un metodo di pagamento del Programma Sottoscrizione Microsoft Online

Per eliminare un metodo di pagamento è necessario essere un amministratore account.

Se il metodo di pagamento è usato da una sottoscrizione del Programma Sottoscrizione Microsoft Online, seguire questa procedura.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com/.
1. Passare a **Gestione dei costi e fatturazione**.
1. Se necessario, selezionare un ambito di fatturazione.
1. Nell'elenco di menu a sinistra selezionare **Metodi di pagamento** in **Fatturazione**.
1. Nell'area Metodi di pagamento selezionare la _riga_ corrispondente al metodo di pagamento in uso. Non selezionare il collegamento al metodo di pagamento. Potrebbe non esserci un'indicazione visiva dell'effettiva selezione del metodo di pagamento.
1. Selezionare **Elimina**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Screenshot di esempio con l'opzione Profili di fatturazione nel portale di Azure" :::
1. Nell'area Elimina un metodo di pagamento selezionare **Elimina** se tutte le condizioni sono soddisfatte. Se l'opzione Elimina non è disponibile, continuare con il passaggio successivo.
1. Viene visualizzato un elenco di condizioni. Eseguire le azioni elencate. Selezionare il collegamento visualizzato nell'area Elimina un metodo di pagamento.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Screenshot di esempio con l'opzione Profili di fatturazione nel portale di Azure" :::
1. Quando si seleziona il collegamento dell'azione correttiva, si viene reindirizzati alla pagina di Azure in cui occorre eseguire l'azione. Eseguire qualsiasi azione correttiva necessaria.
1. Se necessario, completare tutte le altre azioni correttive.
1. Tornare a **Gestione dei costi e fatturazione** > **Profili di fatturazione** > **Metodi di pagamento** ed eliminare il metodo di pagamento.

> [!NOTE]
> Dopo aver annullato una sottoscrizione, la sua eliminazione può richiedere fino a 90 giorni. Se si vuole abbreviare l'attesa, aprire una richiesta di supporto di Azure e chiedere l'eliminazione immediata della sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come annullare una sottoscrizione di Azure, vedere [Annullare la sottoscrizione di Azure](cancel-azure-subscription.md).
- Per altre informazioni su come aggiungere o aggiornare una carta di credito, vedere [Aggiungere, aggiornare o rimuovere una carta di credito per Azure](change-credit-card.md).