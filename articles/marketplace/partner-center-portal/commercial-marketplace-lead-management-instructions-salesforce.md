---
title: Configurare la gestione dei lead per Salesforce Azure Marketplace
description: Configurare la gestione dei lead nei clienti salesforce per Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263104"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurare la gestione dei clienti potenziali per Salesforce

In questo articolo viene descritto come configurare il sistema Salesforce per elaborare i lead di vendita dall'offerta del marketplace commerciale.

> [!Note]
> Marketplace non supporta elenchi precompilati, ad esempio un elenco di valori per il campo **Paese.** Assicurarsi che non siano presenti elenchi impostati prima di continuare. In alternativa, è possibile configurare un [endpoint HTTPS](./commercial-marketplace-lead-management-instructions-https.md) o una tabella di [Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) per ricevere i lead.

## <a name="set-up-your-salesforce-system"></a>Configurare il sistema Salesforce

1. Eseguire l'accesso a Salesforce.
2. Se si utilizza l'esperienza di illuminazione di Salesforce.
    1. Selezionare **Configurazione** dalla home page di Salesforce.
    ![Configurazione di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Dalla pagina Configurazione, spostarsi nel riquadro di spostamento sinistro fino a Impostazioni funzionalità >**Strumenti piattaforma->Marketing->Web-to-Lead**.

        ![Salesforce Web a lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Se si utilizza l'esperienza Salesforce Classic:
    1. Selezionare **Configurazione** dalla home page di Salesforce.
    ![Configurazione classica di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Dalla pagina Configurazione, spostarsi nel riquadro di spostamento sinistro fino a **Crea ->Personalizza->i lead >Web-to-Lead**.

        ![Salesforce classico web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Il resto delle istruzioni è lo stesso indipendentemente dall'esperienza Salesforce in uso.

4. Nella **pagina Impostazione Web-Lead**selezionare il pulsante **Crea modulo Web-to-Lead.**
5. Nell'impostazione di **Web a lead** selezionare **Crea modulo Web a lead**.
    ![Salesforce - Configurazione da Web a lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Nella pagina **Crea un modulo Web-to-Lead**, assicurarsi che `the Include reCAPTCHA in HTML` l'impostazione sia deselezionata e selezionare **Genera**. 
    ![Salesforce - Creare un modulo Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Ti verrà presentato un po 'di testo HTML. Cercare il testo "oid" e copiare il **valore oid** dal testo HTML (solo il testo tra virgolette) e salvarlo. Questo valore verrà incollato nel campo **Identificatore organizzazione** del portale di pubblicazione.
    ![Salesforce - Creare un modulo Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Selezionato **Finito**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configurare l'offerta per l'invio di lead a Salesforce

Quando si è pronti a configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, attenersi alla seguente procedura:

1. Accedi alla pagina **Configurazione offerta** per la tua offerta.
1. Selezionare **Connetti** nella sezione Gestione lead.
    ![Gestione dei lead - Connect](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Nella finestra popup Dettagli connessione selezionare **Salesforce** per **Destinazione** `oid` lead e incollare il modulo da Web a lead creato seguendo i passaggi precedenti nel campo **Identificatore organizzazione.**

1. **Email di contatto:** fornisci email alle persone della tua azienda che devono ricevere notifiche e-mail quando viene ricevuto un nuovo lead. È possibile fornire più messaggi di posta elettronica separandoli con un punto e virgola.

1. Selezionare **Ok**.

Per assicurarsi di aver eseguito correttamente la connessione a una destinazione lead, fare clic sul pulsante di convalida. In caso di esito positivo, si avrà un lead di test nella destinazione del lead.

>[!Note]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

![Dettagli connessione - Scegli una destinazione lead](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Dettagli connessione - Scegli una destinazione lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
