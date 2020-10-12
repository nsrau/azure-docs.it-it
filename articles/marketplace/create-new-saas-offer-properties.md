---
title: Come configurare le proprietà dell'offerta SaaS nel centro per i partner Microsoft
description: Informazioni su come configurare le proprietà per l'offerta di Microsoft Commercial Marketplace Software as a Service (SaaS) nel centro per i partner.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4dd592cb1b0037e2e1054bb24d987d4456f15e2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380977"
---
# <a name="how-to-configure-your-saas-offer-properties"></a>Come configurare le proprietà dell'offerta SaaS

Questo articolo descrive come configurare le proprietà per un'offerta SaaS (Software as a Service) in Microsoft Commercial Marketplace.

Nella scheda **Proprietà** è possibile definire le categorie e i settori applicabili all'offerta, alla versione dell'app e ai contratti legali. Assicurarsi di fornire dettagli completi e accurati sull'offerta in questa pagina, affinché l'offerta venga visualizzata in modo appropriato e presentata al set di clienti adeguato.

## <a name="select-a-category-for-your-offer"></a>Selezionare una categoria per l'offerta

In **categoria**selezionare almeno una delle due categorie per raggruppare l'offerta nelle aree di ricerca del Marketplace appropriate. In base alle categorie selezionate, vengono determinati i negozi online in cui elencare l'offerta, ovvero Azure Marketplace, Microsoft AppSource o entrambi.

## <a name="select-industries-optional"></a>Seleziona settori (facoltativo)

In **settori**, è possibile selezionare fino a due settori e fino a due sottosettori (detti anche Verticals) per ogni settore. Questi settori vengono usati per visualizzare l'offerta quando i clienti filtrano la ricerca in settori e sottosettori nel negozio online.

> [!NOTE]
> Se l'offerta non è specifica di un settore, lasciare vuota questa sezione.

1. In **Industries**selezionare il collegamento **+ Industries** .
1. Selezionare un settore dall'elenco **settore** .
1. Selezionare almeno uno e un massimo di due verticali nell'elenco di **sottosettori** . Usare il tasto CTRL per selezionare più sottosettori.
1. Per aggiungere un altro settore e verticale, selezionare **+ industrie** , quindi ripetere i passaggi da 1 a 3.

## <a name="specify-an-app-version-optional"></a>Specificare una versione dell'app (facoltativo)

 Nella casella **versione app** immettere un numero di versione. La versione dell'app viene usata in AppSource Marketplace per identificare il numero di versione dell'offerta.

## <a name="provide-terms-and-conditions"></a>Specificare i termini e le condizioni

In **Legal**specificare i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

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

Sono disponibili due tipi di modifiche: *universale* e *personalizzato*.

#### <a name="add-universal-amendment-terms"></a>Aggiungi termini di modifica universale

Nei **termini dell'emendamento universale per il contratto standard per il Marketplace commerciale di Microsoft** , immettere le condizioni per la modifica universale. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni vengono visualizzate dai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante il flusso di individuazione e acquisto.

#### <a name="add-one-or-more-custom-amendments"></a>Aggiungere una o più modifiche personalizzate

1. In conformità **alle modifiche personalizzate apportate al contratto standard per il Marketplace commerciale di Microsoft**, selezionare il collegamento **Aggiungi termini di modifica personalizzata (max 10)** .
1. Nella casella **condizioni di modifica personalizzate** immettere le condizioni per la modifica.
1. Nella casella **ID tenant** immettere un ID tenant. Solo i clienti associati agli ID tenant specificati per questi termini personalizzati li visualizzeranno nel flusso di acquisto dell'offerta nel portale di Azure.
   > [!TIP]
   > Un ID tenant identifica il cliente in Azure. È possibile chiedere al cliente questo ID e trovarlo passando a [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Proprietà**. Il valore di ID directory è l'ID tenant (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ). È anche possibile cercare l'ID tenant dell'organizzazione del cliente usandone l'URL del nome di dominio in [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com/).
1. Nella casella **Descrizione** immettere facoltativamente una descrizione descrittiva per l'ID tenant. Questa descrizione consente di identificare il cliente a cui si è interessati con la modifica.
1. Per aggiungere un altro ID tenant, selezionare il collegamento **Aggiungi ID tenant del cliente** e ripetere i passaggi 3 e 4. È possibile aggiungere fino a 20 ID tenant.
1. Per aggiungere un altro termine di modifica, ripetere i passaggi da 1 a 5. È possibile specificare fino a dieci condizioni di modifica personalizzate per offerta. 
2. Prima di continuare, selezionare **Salva bozza**.

### <a name="use-your-own-terms-and-conditions"></a>Usare termini e condizioni personalizzati

È possibile scegliere di specificare i termini e le condizioni, anziché il contratto standard. I clienti devono accettare questi termini prima di poter provare l'offerta.

1. In **Legal**, assicurarsi che la casella di controllo **USA contratto standard per il Marketplace commerciale di Microsoft** sia deselezionata.
1. Nella casella **termini e condizioni** immettere fino a 10.000 caratteri di testo.

   > [!NOTE]
   > Se è necessaria una descrizione più lunga, immettere un singolo indirizzo Web che punti alla posizione in cui si trovano i termini e le condizioni. che verrà visualizzato dai clienti come collegamento attivo.

1. Selezionare **Salva bozza** prima di continuare con la scheda successiva, **elenco offerte**.

## <a name="next-steps"></a>Passaggi successivi

- [Come configurare i dettagli dell'elenco di offerte SaaS](create-new-saas-offer-listing.md)
