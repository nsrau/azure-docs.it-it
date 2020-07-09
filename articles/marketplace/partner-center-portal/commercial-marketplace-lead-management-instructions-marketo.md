---
title: Gestione dei lead in Marketo - Marketplace commerciale Microsoft
description: Informazioni su come usare un sistema CRM Marketo per gestire lead da Microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: be1f21f927b01d66e19dc5e97b38e5c35e6664cb
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120263"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Usare Marketo per gestire i lead del marketplace commerciale

Questo articolo descrive come configurare il sistema CRM Marketo per elaborare i lead di vendita dalle offerte in Microsoft AppSource e Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Configurare il sistema CRM Marketo

1. Accedere a Marketo.

1. Selezionare **Design Studio**.

    ![Design Studio di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Selezionare **Nuovo modulo**.

    ![Nuovo modulo di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Compilare i campi obbligatori nella finestra di dialogo **Nuovo modulo** e quindi selezionare **Crea**.

    ![Creazione nuovo modulo di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Dalla pagina **Dettagli del campo** selezionare **Fine**.

    ![Completamento modulo di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Approvare e chiudere.

1. Nella scheda **MarketplaceLeadBackend** selezionare **Codice di incorporamento**. 

    ![Immagine del codice di incorporamento di Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. L'opzione Codice di incorporamento di Marketo visualizza un codice simile all'esempio seguente.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Copiare i valori per i campi seguenti riportati nel formato del codice di incorporamento. Questi valori verranno usati per configurare l'offerta per la ricezione dei lead nel passaggio successivo. Usare l'esempio seguente come guida per ottenere gli ID necessari dall'esempio di codice di incorporamento di Marketo.

    - ID server = **ys12**
    - ID Munchkin = **123-PQR-789**
    - ID modulo = **1179**

    Un altro modo per individuare questi valori è il seguente:

    - L'ID del server si trova nell'URL dell'istanza di Marketo, ad esempio `serverID.marketo.com`.
    - L'ID Munchkin della sottoscrizione si trova accedendo al menu **Admin** > **Munchkin** nel campo **ID account Munchkin** o nella prima parte del sottodominio host dell'API REST di Marketo: `https://{Munchkin ID}.mktorest.com`.
    - L'ID del modulo è l'ID del modulo del codice di incorporamento creato nel passaggio 7 per indirizzare i lead dal marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configurare l'offerta per inviare lead a Marketo

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura. 

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).

1. Selezionare l'offerta e passare alla scheda **Configurazione dell'offerta**.

1. Nella sezione **Customer leads** (Lead clienti) selezionare **Connect** (Connetti).

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="Customer leads":::

1. Nella finestra popup **Dettagli connessione** selezionare **Marketo** per **Destinazione del lead**.

    ![Scegliere la destinazione del lead](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Specificare **ID server**, **ID account Munchkin** e **ID modulo**.

    > [!NOTE]
    > È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta. 

1. In **Indirizzo posta elettronica contatto** immettere gli indirizzi degli utenti della società che devono ricevere notifiche tramite posta elettronica quando viene ricevuto un nuovo lead. È possibile specificare più indirizzi di posta elettronica separandoli con un punto e virgola.

1. Selezionare **OK**.

   Per assicurarsi di avere eseguito la connessione a una destinazione del lead, selezionare **Convalida**. In caso di esito positivo, sarà presente un lead di test nella destinazione del lead.

   ![Finestra di popup con i dettagli della connessione](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
