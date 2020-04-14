---
title: Configurare la gestione dei lead in Marketo Azure Marketplace
description: Configurare la gestione dei lead per i clienti del marketplace di Marketo per Azure.Configure lead management for Marketo for Azure marketplace customers.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 04eae529efcf9509d8cd43a7629bb0b76b593cd0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252471"
---
# <a name="configure-lead-management-in-marketo"></a>Configurare la gestione dei lead in Marketo

In questo articolo viene descritto come configurare il sistema MARKETo CRM per elaborare i lead di vendita dall'offerta del marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Configurare il sistema MARKETo CRM

1. Accedere a Marketo.
2. Selezionare **Design Studio**.
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Selezionare **Nuovo modulo**.
    ![Nuovo modulo di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Compilare i campi obbligatori nel nuovo modulo e quindi selezionare **Crea**.
    ![Creazione nuovo modulo di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  Nei dettagli del campo selezionare **Fine**.
    ![Completamento modulo di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Approvare e chiudere.

7. Nella scheda *MarketplaceLeadBacked* selezionare **Incorpora codice**. 

    ![Codice di incorporamento](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. L'opzione Codice di incorporamento di Marketo visualizza un codice simile all'esempio seguente.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Copiare i valori per i campi seguenti visualizzati nel modulo Codice di incorporamento. Questi valori verranno utilizzati per configurare l'offerta per ricevere i lead nel passaggio successivo. Usare l'esempio seguente come guida per ottenere gli ID necessari dall'esempio di codice di incorporamento di Marketo.

    - ID server : **ys12**
    - ID Munchkin - **123-PQR-789**
    - ID del modulo **1179**

    **Un altro modo per capire questi valori**

    - L'ID server si trova nell'URL dell'istanza`serverID.marketo.com`Di Marketo, ad esempio " ".
    - Ottenere l'ID Munching dell'abbonamento accedendo al menu Admin>Munchkin nel campo "Munchkin Account ID" o `https://{Munchkin ID}.mktorest.com`dalla prima parte del sottodominio host dell'API REST di Marketo: .
    - ID modulo è l'ID del modulo Codice di incorporamento creato nel passaggio 7 per instradare i lead dal marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configura la tua offerta per inviare lead a Marketo

Quando si è pronti a configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, attenersi alla seguente procedura: 

1. Accedi alla pagina **Configurazione offerta** per la tua offerta.
1. Selezionare **Connetti** nella sezione Gestione lead. 

    ![Gestione dei lead - Connect](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Nella finestra popup Dettagli connessione selezionare **Marketo** per Destinazione lead.

    ![Scegliere una destinazione lead](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Specificare **l'ID server**, **l'ID account munching**e **l'ID modulo**.

    >[!Note]
    >È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta. 

5. **Email di contatto:** fornisci email alle persone della tua azienda che devono ricevere notifiche e-mail quando viene ricevuto un nuovo lead. È possibile fornire più messaggi di posta elettronica separandoli con un punto e virgola.
6. Selezionare **OK**.

Per assicurarsi di aver eseguito correttamente la connessione a una destinazione lead, fare clic sul pulsante di convalida. In caso di esito positivo, si avrà un lead di test nella destinazione del lead.

![Dettagli della connessione](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)