---
title: Configurare la gestione dei lead in Marketo | Azure Marketplace
description: Configurare la gestione dei lead per Marketo per i clienti di Azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: b142e0ab1aaa242157e207ceecf958be51bb1721
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133646"
---
# <a name="configure-lead-management-in-marketo"></a>Configurare la gestione dei lead in Marketo

Questo articolo descrive come configurare il sistema CRM di Marketo per elaborare i lead di vendita dall'offerta del Marketplace commerciale.

## <a name="set-up-your-marketo-crm-system"></a>Configurare il sistema CRM di Marketo

1. Accedere a Marketo.

1. Selezionare **Design Studio**.

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Selezionare **Nuovo modulo**.

    ![Nuovo modulo di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Compilare i campi obbligatori nella finestra di dialogo **nuovo modulo** , quindi selezionare **Crea**.

    ![Creazione nuovo modulo di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Nella pagina **Dettagli campo** selezionare **fine**.

    ![Completamento modulo di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Approva e Chiudi.

1. Nella scheda **MarketplaceLeadBackend** selezionare **incorpora codice**. 

    ![Codice di incorporamento Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. L'opzione Codice di incorporamento di Marketo visualizza un codice simile all'esempio seguente.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Copiare i valori per i seguenti campi mostrati nel formato del codice di incorporamento. Questi valori verranno usati per configurare l'offerta per la ricezione dei lead nel passaggio successivo. Usare l'esempio successivo come guida per ottenere gli ID necessari dall'esempio di codice di incorporamento Marketo.

    - ID server = **ys12**
    - ID Munchkin = **123-PQR-789**
    - ID modulo = **1179**

    Un altro modo per individuare i valori seguenti:

    - L'ID del server si trova nell'URL dell'istanza di Marketo, ad esempio `serverID.marketo.com`.
    - Ottenere l'ID Munchkin della sottoscrizione accedendo al menu di **Amministrazione** > **Munchkin** nel campo **ID account Munchkin** o dalla prima parte del sottodominio host dell'API REST di Marketo: `https://{Munchkin ID}.mktorest.com`.
    - ID modulo è l'ID del modulo di codice di incorporamento creato nel passaggio 7 per indirizzare i lead dal Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configurare l'offerta per inviare lead a Marketo

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura. 

1. Passare alla pagina di **installazione dell'offerta** per l'offerta.

1. Selezionare **Connetti** nella sezione **gestione dei lead** . 

    ![Pulsante Connetti sezione Gestione lead](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Nella finestra popup **Dettagli connessione** selezionare **Marketo** per la **destinazione principale**.

    ![Scegliere una destinazione principale](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Specificare l'ID del **Server**, l' **ID dell'account Munchkin**e l' **ID del modulo**.

    > [!NOTE]
    > È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta. 

1. In **Contact email**immettere gli indirizzi di posta elettronica per gli utenti della società che devono ricevere notifiche tramite posta elettronica quando viene ricevuto un nuovo lead. È possibile fornire più indirizzi di posta elettronica separandoli con un punto e virgola.

1. Selezionare **OK**.

   Per assicurarsi di avere effettuato la connessione a una destinazione principale, selezionare il pulsante **convalida** . In caso di esito positivo, sarà presente un lead di test nella destinazione principale.

   ![Finestra popup Dettagli connessione](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
