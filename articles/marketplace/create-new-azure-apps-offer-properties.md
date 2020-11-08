---
title: Come configurare le proprietà dell'offerta applicazione Azure
description: Informazioni su come configurare le proprietà per l'offerta di applicazione Azure nel centro per i partner.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1414f2ce53d1764e788749fc2d225c56f624bcdc
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370249"
---
# <a name="how-to-configure-your-azure-application-offer-properties"></a>Come configurare le proprietà dell'offerta applicazione Azure

Questo articolo descrive come configurare le proprietà per un'offerta applicazione Azure nel Marketplace commerciale.

Nella pagina **Proprietà** è possibile definire le categorie applicabili all'offerta e ai contratti legali. Assicurarsi di fornire dettagli completi e precisi sull'offerta in questa pagina, in modo che vengano visualizzati in modo appropriato e offerti al set di clienti corretto.

## <a name="select-a-category-for-your-offer"></a>Selezionare una categoria per l'offerta

In **categorie** selezionare il collegamento **categorie** , quindi scegliere almeno una e due categorie per raggruppare l'offerta nelle aree di ricerca del Marketplace commerciale appropriate. Selezionare fino a due sottocategorie per ogni categoria primaria e secondaria. Se per l'offerta non è applicabile alcuna sottocategoria, selezionare **non applicabile**.

## <a name="provide-terms-and-conditions"></a>Specificare i termini e le condizioni

In **Legal** specificare i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- [Usare il contratto standard con le modifiche facoltative](#use-the-standard-contract)
- [Usare termini e condizioni personalizzati](#use-your-own-terms-and-conditions)

Per informazioni sul contratto standard e sulle modifiche facoltative, vedere [contratto standard per Microsoft Commercial Marketplace](standard-contract.md). È possibile scaricare il file PDF con [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) . Assicurarsi che il blocco popup sia disattivato.

### <a name="use-the-standard-contract"></a>Usare il contratto standard

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard che è possibile usare per le offerte nel Marketplace commerciale. Quando si offre il software nel contratto standard, i clienti devono leggere e accettare una sola volta e non è necessario creare termini e condizioni personalizzati.

1. Selezionare la casella **di controllo Usa il contratto standard per il Marketplace commerciale di Microsoft** .

   ![Viene illustrata la casella di controllo Usa il contratto standard per il Marketplace commerciale di Microsoft.](partner-center-portal/media/use-standard-contract.png)

1. Nella finestra di dialogo di **conferma** selezionare **Accetto**. Potrebbe essere necessario scorrere verso l'alto per visualizzarlo.
1. Prima di continuare, selezionare **Salva bozza**.

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il Marketplace commerciale, non è possibile usare i termini e le condizioni personalizzati. È possibile offrire la propria soluzione nel contratto standard con modifiche facoltative o in base a termini e condizioni.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Aggiungere modifiche al contratto standard (facoltativo)

Sono disponibili due tipi di modifiche: _universale_ e _personalizzato_.

#### <a name="add-universal-amendment-terms"></a>Aggiungi termini di modifica universale

Nei **termini dell'emendamento universale per il contratto standard per il Marketplace commerciale di Microsoft** , immettere le condizioni per la modifica universale. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni vengono visualizzate dai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante il flusso di individuazione e acquisto.

#### <a name="add-one-or-more-custom-amendments"></a>Aggiungere una o più modifiche personalizzate

1. In conformità **alle modifiche personalizzate apportate al contratto standard per il Marketplace commerciale di Microsoft** , selezionare il collegamento **Aggiungi termini di modifica personalizzata (max 10)** .
1. Nella casella **condizioni di modifica personalizzate** immettere le condizioni per la modifica.
1. Nella casella **ID tenant** immettere un ID tenant. Solo i clienti associati agli ID tenant specificati per questi termini personalizzati li visualizzeranno nel flusso di acquisto dell'offerta nel portale di Azure.

   > [!TIP]
   > Un ID tenant identifica il cliente in Azure. È possibile chiedere al cliente questo ID e trovarlo passando a [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Proprietà**. Il valore di ID directory è l'ID tenant (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ). È anche possibile cercare l'ID tenant dell'organizzazione del cliente usandone l'URL del nome di dominio in [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com/).

1. Nella casella **Descrizione** immettere facoltativamente una descrizione descrittiva per l'ID tenant. Questa descrizione consente di identificare il cliente a cui si è interessati con la modifica.
1. Per aggiungere un altro ID tenant, selezionare il collegamento **Aggiungi ID tenant del cliente** e ripetere i passaggi 3 e 4. È possibile aggiungere fino a 20 ID tenant.
1. Per aggiungere un altro termine di modifica, ripetere i passaggi da 1 a 5. È possibile specificare fino a dieci condizioni di modifica personalizzate per offerta.
1. Prima di continuare, selezionare **Salva bozza**.

### <a name="use-your-own-terms-and-conditions"></a>Usare termini e condizioni personalizzati

È possibile scegliere di specificare i termini e le condizioni, anziché il contratto standard. I clienti devono accettare questi termini prima di poter provare l'offerta.

1. In **Legal** , assicurarsi che la casella di controllo **USA contratto standard per il Marketplace commerciale di Microsoft** sia deselezionata.
1. Nella casella **termini e condizioni** immettere fino a 10.000 caratteri di testo.

   > [!NOTE]
   > Se è necessaria una descrizione più lunga, immettere un singolo indirizzo Web che punti alla posizione in cui si trovano i termini e le condizioni. che verrà visualizzato dai clienti come collegamento attivo.

1. Selezionare **Salva bozza** prima di continuare con la scheda successiva: elenco offerte.

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare i dettagli dell'elenco di offerte di applicazione Azure](create-new-azure-apps-offer-listing.md)
