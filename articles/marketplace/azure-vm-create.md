---
title: Creare un'offerta di macchina virtuale in Azure Marketplace.
description: Scopri come creare un'offerta di macchina virtuale in Microsoft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: bfd67688ce30b62002e26cea9f7be4df1cb6e622
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126448"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Come creare un'offerta di macchina virtuale in Azure Marketplace

Questo articolo descrive come creare un'offerta di macchina virtuale di Azure per [Azure Marketplace](https://azuremarketplace.microsoft.com/). Fa riferimento alle macchine virtuali basate su Windows e a quelle basate su Linux con un sistema operativo, un disco rigido virtuale e fino a 16 dischi dati.

Prima di iniziare, [creare un account Marketplace commerciale nel centro per i partner](partner-center-portal/create-account.md). Assicurarsi che l'account sia registrato al programma del marketplace commerciale.

## <a name="before-you-begin"></a>Prima di iniziare

Se non è ancora stato fatto, rivedere [pianificare un'offerta di macchina virtuale](marketplace-virtual-machines.md). Verranno illustrati i requisiti tecnici per la macchina virtuale e verranno elencate le informazioni e gli asset necessari per la creazione dell'offerta. 

## <a name="create-a-new-offer"></a>Creare una nuova offerta

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel riquadro a sinistra selezionare **Marketplace commerciale** > **Panoramica** .
3. Nella pagina **Panoramica** selezionare **Nuova offerta** > **Macchina virtuale di Azure** .

    ![Screenshot delle opzioni di menu del riquadro sinistro e del pulsante "Nuova offerta".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Le eventuali modifiche apportate dal Centro per i partner all'offerta in seguito alla sua pubblicazione vengono visualizzate in Azure Marketplace solo dopo che l'offerta viene ripubblicata. Assicurarsi di ripubblicare sempre un'offerta dopo averla modificata.

Immettere un ID in **ID offerta** . Si tratta di un identificatore univoco per ogni offerta nell'account.

- Questo ID è visibile ai clienti nell'indirizzo Web dell'offerta di Azure Marketplace, in Azure PowerShell e nell'interfaccia della riga di comando di Azure, se applicabile.
- Usare solo lettere minuscole e numeri. L'ID può includere trattini e caratteri di sottolineatura, ma non spazi, per un massimo di 50 caratteri. Ad esempio, se si immette **test-offer-1** , l'indirizzo Web dell'offerta sarà `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Non è possibile modificare l'ID offerta dopo aver selezionato **Crea** .

Immettere un alias in **Alias offerta** . L'alias dell'offerta corrisponde al nome usato per l'offerta nel Centro per i partner.

- Questo nome non viene usato in Azure Marketplace ed è diverso dal nome dell'offerta e da altri valori visualizzati dai clienti.

## <a name="enable-a-test-drive-optional"></a>Abilitare una test drive (facoltativo)

Un test drive è un ottimo modo per presentare l'offerta a potenziali clienti, offrendo loro l'accesso a un ambiente preconfigurato per un numero fisso di ore. L'offerta di un test drive comporta un aumento della velocità di conversione e genera lead altamente qualificati. Per ulteriori informazioni sulle unità di test, vedere [che cos'è un test drive?](./what-is-test-drive.md).

> [!TIP]
> Una test drive è diversa da una versione di valutazione gratuita. È possibile offrire una test drive, una versione di valutazione gratuita o entrambe. Entrambi forniscono ai clienti la soluzione per un periodo di tempo fisso. Tuttavia, un test drive include anche una presentazione pratica e autonoma delle funzionalità chiave del prodotto e dei vantaggi illustrati in uno scenario di implementazione reale.

**Per abilitare un test drive**
1.  In **test drive** selezionare la casella di controllo **Abilita un test drive** .
1.  Selezionare il tipo di test drive dall'elenco visualizzato.

## <a name="configure-lead-management"></a>Configurare la gestione dei lead

Quando si pubblica l'offerta nel marketplace commerciale tramite il Centro per i partner, connetterla al sistema Customer Relationship Management (CRM) per ricevere le informazioni di contatto del cliente non appena questo esprime interesse o usa il prodotto. La connessione a un CRM è necessaria se si vuole abilitare una test drive (vedere la sezione precedente). In caso contrario, la connessione a tale sistema è facoltativa.

1. in **Lead cliente** selezionare il collegamento **Connetti** .
1. Nella finestra di dialogo **Dettagli connessione** selezionare una destinazione principale nell'elenco.
1. Completare i campi visualizzati. Per i passaggi dettagliati, vedere gli articoli seguenti:

   - [Configurare l'offerta per inviare lead alla tabella di Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configurare l'offerta per inviare lead a dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (in precedenza Dynamics CRM online)
   - [Configurare l'offerta per inviare lead all'endpoint HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configurare l'offerta per inviare lead a Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configurare l'offerta per inviare lead a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Per convalidare la configurazione specificata, selezionare il collegamento **convalida** .
1. Per chiudere la finestra di dialogo, fare clic su **OK** .

## <a name="resell-through-csps"></a>Rivendere tramite CSP

Per espandere la copertura dell'offerta, è possibile renderla disponibile ai partner del programma [Cloud Solution Provider](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Tutti i piani Bring Your Own License (BYOL) fanno automaticamente parte del programma. È anche possibile scegliere di includere i piani non BYOL.

Selezionare **Crea** per generare l'offerta e continuare.

## <a name="next-steps"></a>Passaggi successivi

- [Configura proprietà offerta macchina virtuale](azure-vm-create-properties.md)
- [Procedure consigliate per le inserzioni di offerte](gtm-offer-listing-best-practices.md)