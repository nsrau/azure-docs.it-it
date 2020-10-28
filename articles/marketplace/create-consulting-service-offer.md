---
title: Come creare un'offerta di servizio di consulenza in Microsoft Commercial Marketplace
description: Scopri come creare una nuova offerta di servizio di consulenza per Microsoft AppSource o Azure Marketplace usando il programma Commercial Marketplace nel centro per i partner Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754453"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Come creare un'offerta di servizio di consulenza nel Marketplace commerciale

Questo articolo illustra come creare un'offerta di servizio di consulenza per Microsoft Commercial Marketplace tramite il centro per i partner. 

## <a name="before-you-begin"></a>Prima di iniziare

Per pubblicare un'offerta di servizio di consulenza, è necessario soddisfare determinati requisiti di idoneità per dimostrare le competenze nel campo. Se non è già stato fatto, leggere [pianificare un'offerta del servizio di consulenza per il Marketplace commerciale](./plan-consulting-service-offer.md). Vengono descritti i prerequisiti per un servizio di consulenza e gli asset necessari durante la creazione dell'offerta con il centro per i partner.

## <a name="create-a-new-consulting-service-offer"></a>Creare una nuova offerta di servizi di consulenza

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2.  Nel menu di spostamento a sinistra, selezionare **Commercial Marketplace**  >  **Overview** .
3.  Nella scheda Panoramica selezionare **+ nuovo offerta**  >  **servizio di consulenza** .

    ![Immagine del menu di spostamento di sinistra.](./media/new-offer-consulting-service.png)

4. Nella finestra di dialogo **nuova offerta** immettere un **ID offerta** . Questo ID è visibile nell'URL dell'inserzione sul Marketplace commerciale. Se ad esempio si immette test-offer-1 in questa casella, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Ogni offerta nell'account deve avere un ID offerta univoco.
    * Usare solo lettere minuscole e numeri. L'ID offerta può includere trattini e caratteri di sottolineatura, ma senza spazi e con un limite di 50 caratteri.
    * Non è possibile modificare l'ID offerta dopo aver selezionato **Crea** .

5. Immettere un alias in **Alias offerta** . Si tratta del nome usato per l'offerta nel Centro per i partner. Non è visibile negli archivi online ed è diverso dal nome dell'offerta indicato ai clienti.
6. Per generare l'offerta e continuare, selezionare **Crea** .

## <a name="configure-lead-management"></a>Configurare la gestione dei lead

Connetti il tuo sistema CRM (Customer Relationship Management) all'offerta del Marketplace commerciale per ricevere le informazioni di contatto del cliente quando un cliente esprime interesse per il servizio di consulenza. È possibile modificare questa connessione in qualsiasi momento durante o dopo la creazione dell'offerta. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Per configurare la gestione dei lead nel centro per i partner:

1.  Nel centro per i partner passare alla scheda **installazione offerta** .
2.  In **Lead cliente** selezionare il collegamento **Connetti** .
3.  Nella finestra di dialogo **Dettagli connessione** selezionare una destinazione principale nell'elenco.
4.  Completare i campi visualizzati. Per i passaggi dettagliati, vedere gli articoli seguenti:

    * [Configurare l'offerta per inviare lead alla tabella di Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Configurare l'offerta per inviare lead a dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (in precedenza Dynamics CRM online)
    * [Configurare l'offerta per inviare lead all'endpoint HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Configurare l'offerta per inviare lead a Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Configurare l'offerta per inviare lead a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Per convalidare la configurazione specificata, selezionare il **collegamento convalida** .
6.  Una volta configurati i dettagli della connessione, selezionare **Connetti** .
7.  Selezionare **Salva bozza** .

Dopo aver inviato l'offerta per la pubblicazione nel centro per i partner, la connessione verrà convalidata e verrà inviato un responsabile del test. Quando si visualizza l'anteprima dell'offerta prima che venga avviata, testare la connessione del lead provando ad acquistare l'offerta nell'ambiente di anteprima.

> [!TIP]
> Assicurarsi che la connessione alla destinazione dei clienti potenziali rimanga aggiornata, in modo da non perdere alcun cliente potenziale.

## <a name="next-steps"></a>Passaggi successivi

* [Come configurare le proprietà dell'offerta del servizio di consulenza](./create-consulting-service-offer-properties.md)