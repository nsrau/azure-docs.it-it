---
title: Configurare le proprietà dell'offerta di macchina virtuale in Azure Marketplace
description: Informazioni su come configurare le proprietà delle offerte di macchine virtuali in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629530"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>Come configurare le proprietà di un'offerta di macchina virtuale

Nella pagina delle **Proprietà** (selezionare dal menu di spostamento a sinistra nel centro per i partner) definire le categorie usate per raggruppare l'offerta della macchina virtuale (VM) in Azure Marketplace, la versione dell'applicazione e i contratti legali che supportano l'offerta.

## <a name="select-a-category"></a>Selezionare una categoria

Selezionare le categorie e le sottocategorie per inserire l'offerta nelle aree di ricerca di Azure Marketplace appropriate. Assicurarsi di descrivere più avanti nella descrizione dell'offerta il modo in cui l'offerta supporta queste categorie.

- Selezionare una categoria primaria.
- Per aggiungere una seconda categoria facoltativa (secondaria), selezionare il collegamento **+ categorie** .
- Selezionare fino a due sottocategorie per la categoria primaria e/o secondaria. Se per l'offerta non è applicabile alcuna sottocategoria, selezionare **non applicabile**. Usare Ctrl + clic per selezionare una seconda sottocategoria.

Vedere l'elenco completo di categorie e sottocategorie nell'elenco delle [procedure](gtm-offer-listing-best-practices.md)consigliate. Le offerte della macchina virtuale vengono sempre visualizzate nella categoria **calcolo** in Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Specificare i termini e le condizioni

In **Legal** specificare i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- [Usare il contratto standard con le modifiche facoltative](#use-the-standard-contract)
- [Usare termini e condizioni personalizzati](#use-your-own-terms-and-conditions)

Per informazioni sul contratto standard e sulle modifiche facoltative, vedere [contratto standard per Microsoft Commercial Marketplace](standard-contract.md). È possibile scaricare il file PDF con [contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) . Assicurarsi che il blocco popup sia disattivato.

### <a name="use-the-standard-contract"></a>Usare il contratto standard

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard che è possibile usare per le offerte nel Marketplace commerciale. Quando si offre il software nel contratto standard, i clienti devono leggere e accettare una sola volta e non è necessario creare termini e condizioni personalizzati.

1. Selezionare la casella **di controllo Usa contratto standard per il Marketplace commerciale di Microsoft** .

   ![Viene illustrata la casella di controllo Usa il contratto standard per il Marketplace commerciale di Microsoft.](partner-center-portal/media/use-standard-contract.png)

1. Nella finestra di dialogo di **conferma** selezionare **Accetto**. A seconda delle dimensioni dello schermo, potrebbe essere necessario scorrere verso l'alto per visualizzarlo.
1. Prima di continuare, selezionare **Salva bozza**.

   > [!NOTE]
   > Dopo la pubblicazione di un'offerta con il contratto standard per il Marketplace commerciale, non è possibile usare i termini e le condizioni personalizzati. È possibile offrire la propria soluzione nel contratto standard con modifiche facoltative o in base a termini e condizioni.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Aggiungere modifiche al contratto standard (facoltativo)

Sono disponibili due tipi di modifiche: *universale* e *personalizzato*.

##### <a name="add-universal-amendment-terms"></a>Aggiungi termini di modifica universale

Nei **termini dell'emendamento universale per il contratto standard per il Marketplace commerciale di Microsoft** , immettere le condizioni per la modifica universale. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni vengono visualizzate dai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante il flusso di individuazione e acquisto.

##### <a name="add-one-or-more-custom-amendments"></a>Aggiungere una o più modifiche personalizzate

1. In conformità **alle modifiche personalizzate apportate al contratto standard per il Marketplace commerciale di Microsoft** , selezionare il collegamento **Aggiungi termini di modifica personalizzata (max 10)** .
2. Immettere le **condizioni di modifica personalizzate** nella casella.
3. Immettere l' **ID tenant** nella casella. Solo i clienti associati agli ID tenant specificati per questi termini personalizzati li visualizzeranno nel flusso di acquisto dell'offerta nel portale di Azure.

   > [!TIP]
   > Un ID tenant identifica il cliente in Azure. È possibile chiedere al cliente questo ID e trovarlo passando a [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Proprietà**. Il valore di ID directory è l'ID tenant (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ). È anche possibile cercare l'ID tenant dell'organizzazione del cliente usandone l'URL del nome di dominio in [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com/).

4. Facoltativamente, immettere una **Descrizione** descrittiva per l'ID tenant. Questa descrizione consente di identificare il cliente a cui si è interessati con la modifica.
5. Per aggiungere un altro ID tenant, selezionare il collegamento **Aggiungi ID tenant del cliente (max 10)** e ripetere i passaggi 3 e 4. È possibile aggiungere fino a 20 ID tenant.
6. Per aggiungere un altro termine di modifica, ripetere i passaggi da 1 a 5. È possibile specificare fino a dieci condizioni di modifica personalizzate per offerta.
7. Prima di continuare, selezionare **Salva bozza**.

### <a name="use-your-own-terms-and-conditions"></a>Usare termini e condizioni personalizzati

È possibile scegliere di specificare i termini e le condizioni, anziché il contratto standard. I clienti devono accettare questi termini prima di poter provare l'offerta.

1. In **Legal** , deselezionare la casella **di controllo Use the Standard Contract for Microsoft Commercial Marketplace** .
1. Nella casella **termini e condizioni** immettere fino a 10.000 caratteri di testo.

   > [!NOTE]
   > Se è necessaria una descrizione più lunga, immettere un singolo indirizzo Web che punti alla posizione in cui si trovano i termini e le condizioni. che verrà visualizzato dai clienti come collegamento attivo.

1. Selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu di navigazione a sinistra, **elenco offerte**.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'inserzione dell'offerta di VM](azure-vm-create-listing.md)
