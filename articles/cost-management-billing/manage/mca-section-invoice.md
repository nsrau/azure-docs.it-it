---
title: Organizzare la fattura in base alle esigenze-Azure
description: Informazioni su come organizzare i costi per la fattura.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991061"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Organizzare i costi personalizzando l'account di fatturazione

Il tuo account di fatturazione per il contratto clienti Microsoft ti offre la flessibilità necessaria per organizzare i costi in base alle tue esigenze, a seconda che si tratti di un ambiente di reparto, progetto o sviluppo. 

Questo articolo descrive come è possibile usare la portale di Azure per organizzare i costi. Si applica a un account di fatturazione per un contratto per i clienti Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Strutturare l'account con profili di fatturazione e sezioni di fattura

Nell'account di fatturazione per un contratto cliente Microsoft si utilizzano i profili di fatturazione e le sezioni della fattura per organizzare i costi.

![Screenshot che mostra la gerarchia di fatturazione del contratto clienti Microsoft](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Profilo di fatturazione

Un profilo di fatturazione rappresenta una fattura e le informazioni di fatturazione correlate, ad esempio i metodi di pagamento e l'indirizzo di fatturazione. Viene generata una fattura mensile all'inizio del mese per ogni profilo di fatturazione incluso nell'account. La fattura contiene gli addebiti per l'utilizzo di Azure e altri acquisti del mese precedente.

Quando si esegue l'iscrizione ad Azure, viene creato automaticamente un profilo di fatturazione insieme al proprio account di fatturazione. È possibile creare profili di fatturazione aggiuntivi per organizzare i costi in più fatture mensili. 

> [!IMPORTANT]
>
> La creazione di profili di fatturazione aggiuntivi può compromettere il costo complessivo. Per ulteriori informazioni, vedere [aspetti da considerare quando si aggiungono nuovi profili di fatturazione](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Sezione della fattura

Una sezione relativa alla fattura rappresenta un raggruppamento di costi nella fattura. Viene creata automaticamente una sezione relativa alla fattura per ogni profilo di fatturazione nell'account. È possibile creare sezioni aggiuntive per organizzare i costi in base alle esigenze. Ogni sezione della fattura viene visualizzata sulla fattura con le spese addebitate nel mese. 

L'immagine seguente mostra una fattura con due sezioni di fattura, Engineering e marketing. Gli addebiti riepilogativi e dettagliati per ogni sezione vengono visualizzati nella fattura. I prezzi indicati nell'immagine sono solo a scopo esemplificativo e non rappresentano i prezzi effettivi dei servizi di Azure. 

![Immagine che mostra una fattura con sezioni](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Struttura di account di fatturazione per scenari comuni

In questa sezione vengono descritti gli scenari comuni per organizzare i costi e le strutture degli account di fatturazione corrispondenti:

|Scenario  |Struttura  |
|---------|---------|
|Jack si iscrive ad Azure e necessita di una singola fattura mensile. | Un profilo di fatturazione e una sezione di fattura. Questa struttura viene configurata automaticamente per Jack quando si iscrive ad Azure e non richiede ulteriori passaggi. |

![Graphic info per uno scenario di fatturazione semplice](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scenario  |Struttura  |
|---------|---------|
|Contoso è un'organizzazione di piccole dimensioni che necessita di una singola fattura mensile, ma i costi di gruppo per i reparti, ovvero marketing e finanza.  | Un profilo di fatturazione per Contoso e una sezione di fattura per ogni reparto marketing e finanza. |

![Graphic info per uno scenario di fatturazione semplice](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scenario  |Struttura  |
|---------|---------|
|Fabrikam è un'organizzazione di medie dimensioni che necessita di fatture separate per i reparti tecnici e di marketing. Per il reparto tecnico, vogliono raggruppare i costi per ambienti, produzioni e sviluppo.  | Un profilo di fatturazione per ogni reparto marketing e finanza. Per il reparto marketing, una sezione relativa alla fattura per l'ambiente di produzione e di sviluppo. |

![Graphic info per uno scenario di fatturazione semplice](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Crea una nuova sezione di fattura

Per creare una sezione della fattura, è necessario essere un **proprietario del profilo di fatturazione** o un **collaboratore per il profilo di fatturazione**. Per ulteriori informazioni, vedere [Manage Invoice sections for Billing profile](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/mca-section-invoice/search-cmb.png)

3. Selezionare **profili di fatturazione** nel riquadro a sinistra. Selezionare un profilo di fatturazione dall'elenco. La nuova sezione verrà visualizzata nella fattura del profilo di fatturazione selezionato. 

   [![screenshot che mostra l'elenco dei profili di fatturazione](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Selezionare **sezioni fattura** dal riquadro a sinistra e quindi selezionare **Aggiungi** nella parte superiore della pagina.

   [![screenshot che mostra l'aggiunta di sezioni di fattura](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Immettere un nome per la sezione della fattura. 

   [![screenshot che mostra la pagina di creazione della sezione della fattura](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Selezionare **Create** (Crea).

## <a name="create-a-new-billing-profile"></a>Crea un nuovo profilo di fatturazione

Per creare un profilo di fatturazione, è necessario essere un **proprietario dell'account di fatturazione** o un **collaboratore account di fatturazione**. Per ulteriori informazioni, vedere [gestire i profili di fatturazione per l'account di fatturazione](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> La creazione di profili di fatturazione aggiuntivi può compromettere il costo complessivo. Per ulteriori informazioni, vedere [aspetti da considerare quando si aggiungono nuovi profili di fatturazione](#things-to-consider-when-adding-new-billing-profiles).

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/mca-section-invoice/search-cmb.png)

3. Selezionare **profili di fatturazione** nel riquadro a sinistra e quindi selezionare **Aggiungi** nella parte superiore della pagina.

   [![screenshot che mostra l'elenco dei profili di fatturazione](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Se non viene visualizzato il pulsante Aggiungi nella pagina profilo di fatturazione, la funzionalità non è disponibile per l'account. Attualmente, è disponibile solo per gli account che sono stati configurati durante l'utilizzo di un rappresentante Microsoft.

4. Compilare il modulo e fare clic su **Crea**.

   [![screenshot che mostra la pagina di creazione del profilo di fatturazione](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Campo  |Definizione  |
    |---------|---------|
    |Nome     | Nome visualizzato che consente di identificare facilmente il profilo di fatturazione nel portale di Azure.  |
    |Numero di ordine di acquisto    | Numero di ordine di acquisto facoltativo. Il numero di ordine di acquisto verrà visualizzato nelle fatture generate per il profilo di fatturazione. |
    |Indirizzo di fatturazione   | L'indirizzo di fatturazione verrà visualizzato nelle fatture generate per il profilo di fatturazione. |
    |Fattura posta elettronica   | Per ricevere le fatture per questo profilo di fatturazione tramite posta elettronica, selezionare la casella fattura posta elettronica. Se non si sceglie di partecipare, è possibile visualizzare e scaricare le fatture nel portale di Azure.|

5. Selezionare **Create** (Crea).

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Collegare i costi alle sezioni della fattura e ai profili di fatturazione

Una volta personalizzato l'account di fatturazione in base alle esigenze, è possibile collegare le sottoscrizioni e altri prodotti alla sezione della fattura e al profilo di fatturazione desiderati.

### <a name="link-a-new-subscription"></a>Collegare una nuova sottoscrizione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Sottoscrizioni**.

   [![screenshot che mostra la ricerca nel portale per la sottoscrizione](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Selezionare **Aggiungi** nella parte superiore della pagina.

   ![Screenshot che mostra il pulsante Aggiungi nella visualizzazione Sottoscrizioni](./media/mca-section-invoice/subscription-add.png)

4. Se si ha accesso a più account di fatturazione, selezionare l'account di fatturazione del contratto clienti Microsoft.

   ![Screenshot che mostra il pulsante Aggiungi nella visualizzazione Sottoscrizioni](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Selezionare il profilo di fatturazione che verrà fatturato per l'utilizzo della sottoscrizione. Gli addebiti per l'utilizzo di Azure e altri acquisti per questa sottoscrizione verranno addebitati alla fattura del profilo di fatturazione selezionato.

6. Selezionare la sezione fattura per collegare gli addebiti della sottoscrizione. Gli addebiti verranno visualizzati in questa sezione della fattura del profilo di fatturazione.

7. Selezionare un piano di Azure e immettere un nome descrittivo per la sottoscrizione. 

9. Fare clic su **Crea**.  

### <a name="link-existing-subscriptions-and-products"></a>Collegare sottoscrizioni e prodotti esistenti

Se si dispone di sottoscrizioni di Azure esistenti o di altri prodotti, ad esempio Azure Marketplace e risorse di origine dell'app, è possibile spostarli dalla sezione fattura esistente a un'altra sezione della fattura per riorganizzare i costi. 

> [!IMPORTANT]
>
> Le sottoscrizioni e altri prodotti possono essere spostati solo tra le sezioni della fattura che appartengono allo stesso profilo di fatturazione. Lo stato di trasferimento di sottoscrizioni e prodotti tra sezioni Invoice in diversi profili di fatturazione non è supportato.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Sottoscrizioni nel portale](./media/mca-section-invoice/search-cmb.png)

3. Per collegare una sottoscrizione a una nuova sezione della fattura, selezionare **sottoscrizioni di Azure** dal lato sinistro della schermata. Per altri prodotti come Azure Marketplace e le risorse di origine dell'app, selezionare **addebiti ricorrenti**.

   [![screenshot che mostra l'opzione per modificare la sezione della fattura](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Nella pagina fare clic sui puntini di sospensione (tre punti) per la sottoscrizione o il prodotto che si desidera collegare a una nuova sezione di fattura. Selezionare la **sezione modifica fattura**.

5. Selezionare la sezione nuova fattura nell'elenco a discesa. Nell'elenco a discesa verranno visualizzate solo le sezioni della fattura associate allo stesso profilo di fatturazione della sezione della fattura esistente.

    [![screenshot che mostra la selezione di una nuova sezione di fattura](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Selezionare **Salva**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Aspetti da considerare quando si aggiungono nuovi profili di fatturazione

### <a name="azure-usage-charges-may-be-impacted"></a>Potrebbero essere interessati addebiti per l'utilizzo di Azure

Nell'account di fatturazione per un contratto cliente Microsoft, l'utilizzo di Azure viene aggregato mensilmente per ogni profilo di fatturazione. I prezzi per le risorse di Azure con prezzi a livelli vengono determinati in base all'utilizzo per ogni profilo di fatturazione separatamente. L'utilizzo non viene aggregato tra i profili di fatturazione durante il calcolo del prezzo. Questo potrebbe influisce sul costo complessivo dell'utilizzo di Azure per gli account con più profili di fatturazione.

Viene ora esaminato un esempio di come i costi variano per due scenari. I prezzi usati negli scenari sono solo a scopo esemplificativo e non rappresentano i prezzi effettivi dei servizi di Azure.

#### <a name="you-only-have-one-billing-profile"></a>È presente un solo profilo di fatturazione.

Si supponga di usare l'archiviazione BLOB in blocchi di Azure, che costa USD. 00184 per GB per i primi 50 terabyte (TB) e quindi 00177 per GB per i successivi 450 terabyte (TB). È stato usato 100 TB nelle sottoscrizioni addebitate al profilo di fatturazione. di seguito è riportato il costo dell'importo.

|  Prezzo del livello (USD) |Quantità | Importo (USD)|
|---------|---------|---------|
|1,84 per TB per i primi 50 TB/mese    | 50 TB        | 92,0   |
|1,77 per TB per i prossimi 450 TB/mese    |  50 TB         | 88,5   |
|Totale     |     100 TB  | 180,5

Il totale degli addebiti per l'uso di 100 TB di dati in questo scenario è **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Sono disponibili più profili di fatturazione.

Si supponga di aver creato un altro profilo di fatturazione e di aver usato 50 GB tramite le sottoscrizioni fatturate al primo profilo di fatturazione e 50 GB tramite sottoscrizioni fatturate al secondo profilo di fatturazione. di seguito è riportato il costo addebitato.

`Charges for the first billing profile`

|  Prezzo del livello (USD) |Quantità | Importo (USD)|
|---------|---------|---------|
|1,84 per TB per i primi 50 TB/mese    | 50 TB        | 92,0  |
|1,77 per TB per i prossimi 450 TB/mese    |  0 TB         | 0,0  |
|Totale     |     50 TB  | 92,0 

`Charges for the second billing profile`

|  Prezzo del livello (USD) |Quantità | Importo (USD)|
|---------|---------|---------|
|1,84 per TB per i primi 50 TB/mese    | 50 TB        | 92,0  |
|1,77 per TB per i prossimi 450 TB/mese    |  0 TB         | 0,0  |
|Totale     |     50 TB  | 92,0 

Gli addebiti totali per l'utilizzo di 100 TB di dati in questo scenario sono **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>I vantaggi della prenotazione di Azure potrebbero non essere validi per tutte le sottoscrizioni

Le prenotazioni di Azure con ambito condiviso vengono applicate alle sottoscrizioni in un unico profilo di fatturazione e non sono condivise tra i profili di fatturazione. 

![Grafico informazioni per l'applicazione di prenotazione per la struttura di un account di fatturazione diverso](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

Nell'immagine precedente, Contoso dispone di due sottoscrizioni. Il vantaggio di prenotazione di Azure viene applicato in modo diverso a seconda della struttura dell'account di fatturazione. Nello scenario a sinistra, il vantaggio relativo alla prenotazione viene applicato a entrambe le sottoscrizioni addebitate al profilo di fatturazione tecnico. Nello scenario a destra, il vantaggio della prenotazione verrà applicato solo alla sottoscrizione 1 poiché è l'unica sottoscrizione addebitata al profilo di fatturazione di Engineering. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Opzioni per Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'altra sottoscrizione di Azure per il Contratto del cliente Microsoft](create-subscription.md)
- [Gestire i ruoli di fatturazione nel portale di Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure dagli utenti in altri account di fatturazione](mca-request-billing-ownership.md)
