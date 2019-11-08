---
title: Configurare la gestione dei lead in Marketo | Azure Marketplace
description: Configurare la gestione dei lead per Marketo per i clienti di Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 8d13e8c3aeabf6d3fdea80ffddbae47b80adc139
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812128"
---
# <a name="configure-lead-management-in-marketo"></a>Configurare la gestione dei lead in Marketo

Questo articolo descrive come configurare il sistema CRM di Marketo per elaborare i lead di vendita dall'offerta del Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Configurare il sistema CRM di Marketo

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

7. Nella scheda *MarketplaceLeadBacked* selezionare **incorpora codice**. 

    ![Codice di incorporamento](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. L'opzione Codice di incorporamento di Marketo visualizza un codice simile all'esempio seguente.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Copiare i valori per i campi riportati di seguito visualizzati nel modulo di codice di incorporamento. Questi valori vengono usati per configurare l'offerta per la ricezione dei lead nel passaggio successivo. Usare l'esempio seguente come guida per ottenere gli ID necessari dall'esempio di codice di incorporamento di Marketo.

    - ID server = **ys12**
    - ID Munchkin = **123-PQR-789**
    - ID modulo = **1179**

    **Un altro modo per individuare questi valori**

    - L'ID del server si trova nell'URL dell'istanza di Marketo, ad esempio, "`serverID.marketo.com`".
    - Per ottenere l'ID munching della sottoscrizione, passare al menu admin > Munchkin nel campo "ID account Munchkin" o dalla prima parte del sottodominio host dell'API REST di Marketo: `https://{Munchkin ID}.mktorest.com`.
    - ID modulo è l'ID del modulo di codice di incorporamento creato nel passaggio 7 per indirizzare i lead dal Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configurare l'offerta per inviare lead a Marketo

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura: 

1. Passare alla pagina di **configurazione dell'offerta** per l'offerta.
1. Selezionare **Connetti** nella sezione gestione dei lead. 

    ![Gestione dei lead-connessione](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Nella finestra popup Dettagli connessione selezionare **Marketo** per la destinazione principale.

    ![Scegliere una destinazione principale](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Specificare l'ID del **Server**, l' **ID dell'account munching**e l' **ID del modulo**.

    >[!Note]
    >È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta. 

