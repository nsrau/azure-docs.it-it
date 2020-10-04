---
title: Come creare un'offerta SaaS in Microsoft Commercial Marketplace
description: Scopri come creare una nuova offerta SaaS (Software as a Service) per l'inserzione o la vendita in Microsoft AppSource, Azure Marketplace o tramite il programma Cloud Solution Provider (CSP) usando il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 2679d24a9e8c821dae73114f32adbeb3d22a1458
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708901"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Come creare un'offerta SaaS nel Marketplace commerciale

In qualità di editore del Marketplace commerciale, è possibile creare un'offerta SaaS (Software as a Service), in modo che i potenziali clienti possano acquistare la soluzione tecnica basata su SaaS. Questo articolo illustra il processo di creazione di un'offerta SaaS per Microsoft Commercial Marketplace.

## <a name="before-you-begin"></a>Prima di iniziare

Se non è già stato fatto, leggere [pianificare un'offerta SaaS per il Marketplace commerciale](plan-saas-offer.md). Verranno illustrati i requisiti tecnici per l'app SaaS e le informazioni e gli asset necessari durante la creazione dell'offerta. A meno che non si preveda di pubblicare un semplice elenco (opzione**Contattaci** elenco) nel Marketplace commerciale, l'applicazione SaaS deve soddisfare i requisiti tecnici relativi all'autenticazione.

## <a name="create-a-new-saas-offer"></a>Creare una nuova offerta SaaS

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
1. Nel menu di spostamento a sinistra, selezionare **Commercial Marketplace**  >  **Overview**.
1. Nella scheda **Panoramica** selezionare **+ nuovo offerta**  >  **software come servizio**.

   :::image type="content" source="media/new-offer-saas.png" alt-text="Vengono illustrati il menu di spostamento a sinistra e il nuovo elenco di offerte.":::

1. Nella finestra di dialogo **nuova offerta** immettere un **ID offerta**. Questo ID è visibile nell'URL dell'elenco di Marketplace commerciale e nei modelli di Azure Resource Manager, se applicabile. Se ad esempio si immette **test-offer-1** in questa casella, l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
   + Ogni offerta nell'account deve avere un ID offerta univoco.
   + Usare solo lettere minuscole e numeri. È possibile includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri.
   + Non è possibile modificare l'ID offerta dopo aver selezionato **Crea**.

1. Immettere un alias in **Alias offerta**. Si tratta del nome usato per l'offerta nel Centro per i partner.

   + Questo nome non è visibile nel Marketplace commerciale ed è diverso dal nome dell'offerta e da altri valori visualizzati ai clienti.
   + Non è possibile modificare l'alias dell'offerta dopo aver selezionato **Crea**.
1. Per generare l'offerta e continuare, selezionare **Crea**.

## <a name="configure-your-saas-offer-setup-details"></a>Configurare i dettagli dell'installazione dell'offerta SaaS

Nella scheda **installazione offerta** , in **Dettagli installazione**, scegliere se vendere l'offerta tramite Microsoft o gestire le transazioni in modo indipendente. Le offerte vendute tramite Microsoft sono indicate come _offerte transazionali_, il che significa che Microsoft semplifica lo scambio di denaro per una licenza software per conto dell'editore. Per ulteriori informazioni su queste opzioni, vedere l' [elenco delle opzioni](plan-saas-offer.md#listing-options) e [determinare l'opzione di pubblicazione](determine-your-listing-type.md).

1. Per eseguire la vendita tramite Microsoft e semplificare le transazioni, selezionare **Sì**. Continuare a [abilitare un test drive](#enable-a-test-drive-optional).

1. Per elencare l'offerta tramite il Marketplace commerciale ed elaborare le transazioni in modo indipendente, selezionare **No**, quindi eseguire una delle operazioni seguenti:
   + Per fornire una sottoscrizione gratuita per l'offerta, selezionare **Get it Now (gratuito)**. Nella casella **URL offerta** , quindi, immettere l'URL (a partire da *http* o *https*), in cui i clienti possono ottenere una versione di valutazione tramite [l'autenticazione con un clic usando Azure Active Directory (Azure ad)](marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials). Ad esempio: `https://contoso.com/saas-app`.
   + Per fornire una versione di valutazione gratuita di 30 giorni, selezionare versione di valutazione **gratuita**, quindi nella casella **URL di valutazione** visualizzata immettere l'URL (a partire da *http* o *https*), in cui i clienti possono accedere alla versione di valutazione gratuita tramite [l'autenticazione con un clic usando Azure Active Directory (Azure ad)](marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials). Ad esempio: `https://contoso.com/trial/saas-app`.
   + Per consentire ai clienti potenziali di contattare l'utente per acquistare l'offerta, selezionare **contattami**.

### <a name="enable-a-test-drive-optional"></a>Abilitare una test drive (facoltativo)

Un test drive è un ottimo modo per presentare l'offerta a potenziali clienti, offrendo loro l'accesso a un ambiente preconfigurato per un numero fisso di ore. L'offerta di un test drive comporta un aumento della velocità di conversione e genera lead altamente qualificati. Per ulteriori informazioni sulle unità di test, vedere [che cos'è un test drive?](partner-center-portal/test-drive.md).

> [!TIP]
> Una test drive è diversa da una versione di valutazione gratuita. È possibile offrire una test drive, una versione di valutazione gratuita o entrambe. Entrambi forniscono ai clienti la soluzione per un periodo di tempo fisso. Tuttavia, un test drive include anche una presentazione pratica e autonoma delle funzionalità chiave del prodotto e dei vantaggi illustrati in uno scenario di implementazione reale.

**Per abilitare un test drive**
1.  In **test drive**selezionare la casella di controllo **Abilita un test drive** .
1.  Selezionare il tipo di test drive dall'elenco visualizzato.

### <a name="configure-lead-management"></a>Configurare la gestione dei lead

Connetti il tuo sistema CRM (Customer Relationship Management) all'offerta del Marketplace commerciale per ricevere le informazioni di contatto del cliente quando un cliente esprime interesse o distribuisce il prodotto. È possibile modificare questa connessione in qualsiasi momento durante o dopo la creazione dell'offerta.

> [!NOTE]
> È necessario configurare la gestione dei lead se si sta vendendo l'offerta tramite Microsoft o se è stata selezionata l'opzione **Contatta** l'utente. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](partner-center-portal/commercial-marketplace-get-customer-leads.md).

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Per configurare i dettagli della connessione nel centro per i partner

1.  In **Lead cliente**selezionare il collegamento **Connetti** .
1. Nella finestra di dialogo **Dettagli connessione** selezionare una destinazione principale nell'elenco.
1. Completare i campi visualizzati. Per i passaggi dettagliati, vedere gli articoli seguenti:

   - [Configurare l'offerta per inviare lead alla tabella di Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configurare l'offerta per inviare lead a dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (in precedenza Dynamics CRM online)
   - [Configurare l'offerta per inviare lead all'endpoint HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configurare l'offerta per inviare lead a Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configurare l'offerta per inviare lead a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Per convalidare la configurazione specificata, selezionare il collegamento **convalida** .
1. Per chiudere la finestra di dialogo, fare clic su **OK**.

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare le proprietà dell'offerta SaaS](create-new-saas-offer-properties.md)
