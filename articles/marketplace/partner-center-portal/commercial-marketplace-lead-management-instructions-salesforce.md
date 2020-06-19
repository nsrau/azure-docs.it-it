---
title: Gestione dei lead in Salesforce - Marketplace commerciale Microsoft
description: Informazioni su come usare Salesforce per configurare i clienti potenziali per Microsoft AppSource e Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 7fbe27845164eb7711d3531173fb440c5a3570f4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849137"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurare la gestione dei clienti potenziali per Salesforce

Questo articolo descrive come configurare il sistema Salesforce per elaborare i lead di vendita dalle offerte in Microsoft AppSource e Azure Marketplace.

> [!NOTE]
> Azure Marketplace non supporta elenchi prepopolati, ad esempio un elenco di valori per il campo **Paese**. Assicurarsi che non siano stati configurati elenchi prima di continuare. In alternativa, è possibile configurare un [endpoint HTTPS](./commercial-marketplace-lead-management-instructions-https.md) o una [tabella di Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) per ricevere i lead.

## <a name="set-up-your-salesforce-system"></a>Configurare il sistema Salesforce

1. Eseguire l'accesso a Salesforce.
1. Passare alle impostazioni di **Web a lead**. 
    
    Se si usa Salesforce Lighting Experience:
    1. Selezionare **Configura** nella home page di Salesforce.

       ![Configurazione di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Nella pagina di **configurazione** passare a **Strumenti della piattaforma** > **Impostazioni funzionalità** > **Marketing** > **Web a lead**.

        ![Web a lead di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Se si usa l'edizione classica di Salesforce:

    1. Selezionare **Configura** nella home page di Salesforce.

       ![Configurazione dell'edizione classica di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Nella pagina di **compilazione** selezionare **Build** > **Personalizza** > **Lead** > **Web a lead**.

        ![Web a lead di Salesforce (edizione classica)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   I passaggi rimanenti sono gli stessi per entrambe le edizioni di Salesforce.

1. Nella pagina di **impostazione di Web a lead** selezionare il pulsante **Create Web-to-Lead Form** (Crea modulo Web a lead).
1. Nella finestra di **impostazione di Web a lead** selezionare **Create a Web-to-Lead Form** (Crea modulo Web a lead).

    ![Impostazione di un modulo Web a lead in Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. Nella finestra **Create a Web-to-Lead Form** (Crea modulo Web a lead) assicurarsi che l'impostazione `Include reCAPTCHA in HTML` sia deselezionata e selezionare **Genera**.

    ![Riquadro della finestra Crea modulo Web a lead di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Verrà visualizzato un testo HTML. Cercare il testo "oid" e copiare il **valore "oid"** dal testo HTML (solo il testo tra virgolette) e salvarlo. Il valore verrà incollato nel campo **Identificatore organizzazione** del portale di pubblicazione.

    ![Finestra Crea modulo Web a lead di Salesforce con il valore HTML "oid" visualizzato](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Selezionare **Completato**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configurare l'offerta per inviare lead a Salesforce

Quando si è pronti a configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura.

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).

1. Selezionare l'offerta e passare alla scheda **Configurazione dell'offerta**.

1. Nella sezione **Customer leads** (Lead clienti) selezionare **Connetti**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Customer leads"::: (Lead clienti)

1. Nella finestra popup **Dettagli connessione** selezionare **Salesforce** per **Lead destination** (Destinazione lead) e incollare il valore `oid` dal modulo Web a lead creato nel campo **Identificatore organizzazione**.

    ![Casella e-mail Convalida contatto della finestra popup Dettagli connessione](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. In **Indirizzo posta elettronica contatto** immettere gli indirizzi delle persone dell'organizzazione che dovranno ricevere le notifiche tramite posta elettronica quando si riceve un nuovo lead. È possibile specificare più indirizzi di posta elettronica separandoli con un punto e virgola.

1. Selezionare **OK**.

Per assicurarsi di avere eseguito la connessione a una destinazione del lead, selezionare **Convalida**. In caso di esito positivo, sarà presente un lead di test nella destinazione del lead.

>[!NOTE]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i clienti potenziali per l'offerta.
