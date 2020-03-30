---
title: Configurare la gestione dei lead in Marketo Azure Marketplace
description: Configurare la gestione dei lead per i clienti del marketplace di Marketo per Azure.Configure lead management for Marketo for Azure marketplace customers.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 35d57d117f6308863965ffd789c0e28bedd0f301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281511"
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

