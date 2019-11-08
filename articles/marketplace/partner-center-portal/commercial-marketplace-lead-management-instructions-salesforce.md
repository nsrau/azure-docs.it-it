---
title: Configurare la gestione dei lead per Salesforce | Azure Marketplace
description: Configurare la gestione dei lead in Salesforce per i clienti di Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: bdd3bb50fc69083c73eb01a84bf0fb0db82a8a65
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812148"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurare la gestione dei clienti potenziali per Salesforce

Questo articolo descrive come configurare il sistema Salesforce per elaborare i lead di vendita dall'offerta del Marketplace.

## <a name="set-up-your-salesforce-system"></a>Configurare il sistema Salesforce

1. Eseguire l'accesso a Salesforce.
2. Se si usa l'esperienza di illuminazione di Salesforce.
    1. Selezionare **installazione** dalla Home page di Salesforce.
    ![Configurazione di Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Dalla pagina di installazione, spostarsi nel menu di spostamento a sinistra di **strumenti della piattaforma-> impostazioni funzionalità-> marketing-> Web-to-lead**.
    ![Salesforce Web a lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Se si usa l'esperienza classica di Salesforce:
    1. Selezionare **installazione** dalla Home page di Salesforce.
    installazione classica di ![Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Dalla pagina di installazione, spostarsi nel menu di spostamento a sinistra per **creare > > lead > da Web a lead**.
    ![](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png) da Web a lead di Salesforce classico

Le altre istruzioni sono identiche indipendentemente dall'esperienza di Salesforce in uso.

4. Nella **pagina installazione Web-lead**selezionare il pulsante **Crea modulo Web-to-lead** .
5. Nell'impostazione di **Web a lead** selezionare **Crea modulo Web a lead**.
    Installazione di ![Salesforce-Web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Nella pagina **creare un modulo Web a lead**verificare che `the Include reCAPTCHA in HTML` impostazione sia deselezionata e selezionare **genera**. 
    ![Salesforce-creare un modulo Web a lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Verrà visualizzato un testo HTML. Cercare il testo "OID" e copiare il **valore OID** dal testo HTML (solo il testo tra virgolette) e salvarlo. Questo valore verrà incollato nel campo **identificatore organizzazione** del portale di pubblicazione.
    ![Salesforce-creare un modulo Web a lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Selezione **completata**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configurare l'offerta per inviare lead a Salesforce

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura:

1. Passare alla pagina di **configurazione dell'offerta** per l'offerta.
1. Selezionare **Connetti** nella sezione gestione dei lead.
    gestione dei lead di ![-connessione](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Nella finestra popup Dettagli connessione selezionare **Salesforce** per la **destinazione lead** e incollare il `oid` dal modulo Web a lead creato seguendo i passaggi precedenti nel campo **identificatore organizzazione** .

1. Selezionare **Salva**. 

    >[!Note]
    >È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.

    ![Dettagli connessione-scegliere una destinazione principale](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Dettagli connessione-scegliere una destinazione principale](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)