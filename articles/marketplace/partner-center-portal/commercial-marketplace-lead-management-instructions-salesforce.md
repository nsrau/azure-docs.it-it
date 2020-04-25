---
title: Configurare la gestione dei lead per Salesforce | Azure Marketplace
description: Configurare la gestione dei lead in Salesforce per i clienti di Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: e0fbb09370e198772b4fc485b3c0fe8a56da4226
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133642"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurare la gestione dei clienti potenziali per Salesforce

Questo articolo descrive come configurare il sistema Salesforce per elaborare i lead di vendita dall'offerta del Marketplace commerciale.

> [!NOTE]
> Azure Marketplace non supporta gli elenchi prepopolati, ad esempio un elenco di valori per il campo **Country** . Assicurarsi che non siano stati impostati elenchi prima di continuare. In alternativa, è possibile configurare un [endpoint HTTPS](./commercial-marketplace-lead-management-instructions-https.md) o una [tabella di Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) per ricevere i lead.

## <a name="set-up-your-salesforce-system"></a>Configurare il sistema Salesforce

1. Eseguire l'accesso a Salesforce.
1. Se si usa l'esperienza di illuminazione di Salesforce:
    1. Selezionare il **programma di installazione** nel Home page di Salesforce.

       ![Configurazione di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Nella pagina **installazione** passare a **strumenti** > piattaforma**Impostazioni** > funzionalità**Marketing** > **Web-to-lead**.

        ![Salesforce Web a lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

1. Se si usa l'esperienza classica di Salesforce:

    1. Selezionare il **programma di installazione** nel Home page di Salesforce.

       ![Configurazione classica di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Nella pagina del **programma di installazione** selezionare **compilazione** > **Personalizza** > **Lead** > **da Web a lead**.

        ![Web-to-lead di Salesforce classico](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Le altre istruzioni sono le stesse, indipendentemente dall'esperienza di Salesforce usata.

1. Nella pagina **installazione Web-lead** selezionare il pulsante **Crea modulo Web-to-lead** .
1. Nel **programma di installazione Web-to-lead**selezionare **Crea un modulo Web-to-lead**.

    ![Installazione da Web a lead di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. Quando si **Crea un modulo Web a lead**, assicurarsi che l' `Include reCAPTCHA in HTML` impostazione sia deselezionata e selezionare **genera**.

    ![Creazione di un riquadro form da Web a lead per Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Verrà visualizzato un testo HTML. Cercare il testo "OID" e copiare il **valore "OID"** dal testo HTML (solo il testo tra virgolette) e salvarlo. Questo valore verrà incollato nel campo **identificatore organizzazione** del portale di pubblicazione.

    ![Salesforce creare un modulo Web a lead che mostra il valore "OID" HTML](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Selezionare **fine**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configurare l'offerta per inviare lead a Salesforce

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura.

1. Passare alla pagina di **installazione dell'offerta** per l'offerta.
1. Selezionare **Connetti** nella sezione **gestione dei lead** .

    ![Pulsante Connetti sezione Gestione lead](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Nella finestra popup **Dettagli connessione** selezionare **Salesforce** per la **destinazione lead** e incollare il `oid` valore del modulo Web-to-lead creato nel campo **identificatore organizzazione** .

1. In **Contact email**immettere gli indirizzi di posta elettronica per gli utenti della società che devono ricevere notifiche tramite posta elettronica quando viene ricevuto un nuovo lead. È possibile specificare più messaggi di posta elettronica separandoli con un punto e virgola.

1. Fare clic su **OK**.

Per assicurarsi di avere effettuato la connessione a una destinazione principale, selezionare il pulsante **convalida** . In caso di esito positivo, sarà presente un lead di test nella destinazione principale.

>[!NOTE]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

![Finestra popup Dettagli connessione scegliere una casella di destinazione lead](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Finestra popup Dettagli connessione finestra di dialogo Convalida contatto e-mail](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
