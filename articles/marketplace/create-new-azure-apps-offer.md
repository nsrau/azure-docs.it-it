---
title: Come creare un'offerta di applicazione Azure nel Marketplace commerciale
description: Scopri come creare una nuova offerta di applicazione Azure per l'inserzione o la vendita in Azure Marketplace o tramite il programma Cloud Solution Provider (CSP) usando il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370225"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Come creare un'offerta di applicazione Azure nel Marketplace commerciale

In qualità di editore del Marketplace commerciale, è possibile creare un'offerta di applicazione Azure in modo che i potenziali clienti possano acquistare la soluzione. Questo articolo illustra il processo di creazione di un'offerta di applicazione Azure per Microsoft Commercial Marketplace.

Se non è già stato fatto, leggere [pianificare un'offerta di applicazione Azure per il Marketplace commerciale](plan-azure-application-offer.md). Fornirà le risorse e consentirà di raccogliere le informazioni e gli asset necessari durante la creazione dell'offerta.

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).

1. Nel menu di spostamento a sinistra, selezionare **Commercial Marketplace**  >  **Overview**.

1. Nella pagina Panoramica selezionare **+ Nuova offerta** > **Applicazione Azure**.

    ![Immagine del menu di spostamento a sinistra.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. Nella finestra di dialogo **nuova offerta** immettere un **ID offerta**. Si tratta di un identificatore univoco per ogni offerta nell'account. Questo ID è visibile nell'URL dell'elenco di Marketplace commerciale e nei modelli di Azure Resource Manager, se applicabile. Se ad esempio si immette test-offer-1 in questa casella, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

     * Ogni offerta nell'account deve avere un ID offerta univoco.
     * Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri.
     * Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

1. Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

     * Questo nome è visibile solo nel centro per i partner ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
     * L'alias dell'offerta non può essere modificato dopo aver selezionato **Crea**.

1. Per generare l'offerta e continuare, selezionare  **Crea**.

## <a name="configure-your-azure-application-offer-setup-details"></a>Configurare i dettagli di installazione dell'offerta di applicazione Azure

Nella scheda **installazione offerta** , in **Dettagli installazione** , scegliere se configurare un test drive. Si connetterà anche il sistema CRM (Customer Relationship Management) con l'offerta del Marketplace commerciale.

### <a name="enable-a-test-drive-optional"></a>Abilitare una test drive (facoltativo)

Un test drive è un ottimo modo per presentare l'offerta a potenziali clienti, offrendo loro l'accesso a un ambiente preconfigurato per un numero fisso di ore. L'offerta di un test drive comporta un aumento della velocità di conversione e genera lead altamente qualificati. Per ulteriori informazioni sulle unità di test, vedere [test drive](plan-azure-application-offer.md#test-drive).

#### <a name="to-enable-a-test-drive"></a>Per abilitare un test drive

- In **test drive** selezionare la casella di controllo **Abilita un test drive** .

### <a name="customer-lead-management"></a>Gestione clienti

Connetti il tuo sistema CRM (Customer Relationship Management) all'offerta del Marketplace commerciale per ricevere le informazioni di contatto del cliente quando un cliente esprime interesse o distribuisce il prodotto.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Per configurare i dettagli della connessione nel centro per i partner

1. In **Lead cliente** selezionare il collegamento **Connetti** .
1. Nella finestra di dialogo **Dettagli connessione** selezionare una destinazione principale nell'elenco.
1. Completare i campi visualizzati. Per i passaggi dettagliati, vedere gli articoli seguenti:

   - [Configurare l'offerta per inviare lead alla tabella di Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configurare l'offerta per inviare lead a dynamics 365 Customer Engagement](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (in precedenza Dynamics CRM online)
   - [Configurare l'offerta per inviare lead all'endpoint HTTPS](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configurare l'offerta per inviare lead a Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configurare l'offerta per inviare lead a Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Per convalidare la configurazione specificata, selezionare il collegamento **convalida** , se applicabile.
1. Per chiudere la finestra di dialogo, selezionare **Connetti**.
1. Selezionare **Salva bozza** prima di continuare con la scheda successiva: proprietà.

> [!NOTE]
> Assicurarsi che la connessione alla destinazione lead rimanga aggiornata in modo da non perdere i lead. Assicurarsi di aggiornare queste connessioni ogni volta che viene modificato un elemento.

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare le proprietà dell'offerta applicazione Azure](create-new-azure-apps-offer-properties.md)
