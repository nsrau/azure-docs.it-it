---
title: Organizzare la fattura in base alle esigenze - Azure
description: Informazioni su come organizzare i costi nella fattura.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: c9cb1d7d1dcc6e7872b22d8c58fe44b9bce25f13
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200745"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Organizzare i costi personalizzando l'account di fatturazione

L'account di fatturazione per il Contratto del cliente Microsoft offre la flessibilità necessaria per organizzare i costi in base alle esigenze, a seconda che si tratti di un ambiente di reparto, progetto o sviluppo.

Questo articolo descrive come usare il portale di Azure per organizzare i costi. Questo articolo si applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Strutturare l'account con profili di fatturazione e sezioni della fattura

Nell'account di fatturazione per un Contratto del cliente Microsoft, per organizzare i costi si usano profili di fatturazione e sezioni della fattura.

![Screenshot che illustra la gerarchia di fatturazione del Contratto del cliente Microsoft](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Profilo di fatturazione

Un profilo di fatturazione rappresenta una fattura e le informazioni di fatturazione correlate, come i metodi di pagamento e l'indirizzo di fatturazione. Viene generata una fattura mensile all'inizio del mese per ogni profilo di fatturazione incluso nell'account. La fattura contiene gli addebiti per l'utilizzo di Azure e per altri acquisti eseguiti nel corso del mese precedente.

Quando si esegue l'iscrizione ad Azure, insieme all'account di fatturazione viene creato automaticamente un profilo di fatturazione. È possibile creare profili di fatturazione aggiuntivi per organizzare i costi in più fatture mensili.

> [!IMPORTANT]
>
> La creazione di profili di fatturazione aggiuntivi può influire sui costi complessivi. Per altre informazioni, vedere [Aspetti da considerare quando si aggiungono nuovi profili di fatturazione](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Sezione della fattura

Una sezione della fattura rappresenta un raggruppamento di costi all'interno della fattura. Per ogni profilo di fatturazione dell'account viene creata automaticamente una sezione della fattura. È possibile creare sezioni aggiuntive per organizzare i costi in base alle esigenze. Ogni sezione della fattura visualizza gli addebiti relativi al mese di riferimento.

L'immagine seguente illustra una fattura con due sezioni, Engineering e Marketing. Nella fattura sono visualizzati il riepilogo e il dettaglio degli addebiti per ogni sezione. I prezzi indicati nell'immagine sono solo a scopo esemplificativo e non rappresentano i prezzi effettivi dei servizi di Azure.

![Immagine che illustra una fattura con sezioni](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Struttura dell'account di fatturazione per scenari comuni

Questa sezione descrive gli scenari comuni per l'organizzazione dei costi e delle strutture degli account di fatturazione corrispondenti:

|Scenario  |Struttura  |
|---------|---------|
|Jack si iscrive ad Azure. Gli serve un'unica fattura mensile. | Un profilo di fatturazione e una sezione della fattura. Questa struttura viene configurata automaticamente per Jack quando si iscrive ad Azure e non richiede passaggi aggiuntivi. |

![Immagine grafica con informazioni relative a uno scenario di fatturazione semplice](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scenario  |Struttura  |
|---------|---------|
|Contoso è un'organizzazione di piccole dimensioni che necessita di un'unica fattura mensile, ma con i costi raggruppati per reparto. I reparti sono Marketing ed Engineering.  | Un profilo di fatturazione per Contoso e una sezione della fattura per ognuno dei reparti Marketing ed Engineering. |

![Immagine grafica con informazioni relative a uno scenario di fatturazione semplice](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scenario  |Struttura  |
|---------|---------|
|Fabrikam è un'organizzazione di medie dimensioni che necessita di una fattura separata per ognuno dei propri reparti, Engineering e Marketing. Per il reparto Engineering, Fabrikam vuole raggruppare i costi in base agli ambienti: Production e Development.  | Un profilo di fatturazione per ognuno dei reparti Marketing ed Engineering. Per il reparto Engineering, una sezione della fattura per ognuno degli ambienti Production e Development. |

![Immagine grafica con informazioni relative a uno scenario di fatturazione semplice](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Creare una nuova sezione della fattura

Per creare una sezione della fattura, è necessario essere un **proprietario del profilo di fatturazione** o un **collaboratore per il profilo di fatturazione**. Per altre informazioni, vedere [Gestire le sezioni della fattura per il profilo di fatturazione](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/mca-section-invoice/search-cmb.png)

3. Selezionare **Profili di fatturazione** dal riquadro a sinistra. Selezionare un profilo di fatturazione dall'elenco. La nuova sezione verrà visualizzata nella fattura del profilo di fatturazione selezionato.

   [![Screenshot che visualizza l'elenco dei profili di fatturazione](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Selezionare **Sezioni della fattura** dal riquadro a sinistra e quindi selezionare **Aggiungi** nella parte superiore della pagina.

   [![Screenshot che illustra l'aggiunta di sezioni della fattura](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Immettere un nome per la sezione della fattura.

   [![Screenshot che illustra la pagina di creazione della sezione della fattura](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Selezionare **Create** (Crea).

## <a name="create-a-new-billing-profile"></a>Creare un nuovo profilo di fatturazione

Per creare un profilo di fatturazione, è necessario essere **proprietario dell'account di fatturazione** o **collaboratore per l'account di fatturazione**. Per altre informazioni, vedere [Gestire i profili di fatturazione per l'account di fatturazione](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> La creazione di profili di fatturazione aggiuntivi può influire sui costi complessivi. Per altre informazioni, vedere [Aspetti da considerare quando si aggiungono nuovi profili di fatturazione](#things-to-consider-when-adding-new-billing-profiles).

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione nel portale](./media/mca-section-invoice/search-cmb.png)

3. Selezionare **Profili di fatturazione** dal riquadro a sinistra e quindi selezionare **Aggiungi** nella parte superiore della pagina.

   [![Screenshot che visualizza l'elenco dei profili di fatturazione](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Se nella pagina Profilo di fatturazione non viene visualizzato il pulsante Aggiungi, la funzionalità non è disponibile per l'account. Questa è attualmente disponibile solo per gli account che sono stati configurati in collaborazione con un rappresentante Microsoft.

4. Compilare il modulo e fare clic su **Crea**.

   [![Screenshot che illustra la pagina di creazione di un profilo di fatturazione](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Campo  |Definizione  |
    |---------|---------|
    |Nome     | Nome visualizzato che consente di identificare facilmente il profilo di fatturazione nel portale di Azure.  |
    |Numero di ordine di acquisto    | Numero di ordine di acquisto facoltativo. Il numero di ordine di acquisto verrà visualizzato nelle fatture generate per il profilo di fatturazione. |
    |Indirizzo di fatturazione   | L'indirizzo di fatturazione verrà visualizzato nelle fatture generate per il profilo di fatturazione. |
    |Fattura tramite posta elettronica   | Per ricevere le fatture relative a questo profilo di fatturazione tramite posta elettronica, selezionare la casella Fattura tramite posta elettronica. Se non si seleziona esplicitamente questa casella, è possibile visualizzare e scaricare le fatture nel portale di Azure.|

5. Selezionare **Create** (Crea).

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Collegare gli addebiti alle sezioni della fattura e ai profili di fatturazione

Dopo aver personalizzato l'account di fatturazione in base alle esigenze, è possibile collegare le sottoscrizioni e altri prodotti alla sezione della fattura e al profilo di fatturazione desiderati.

### <a name="link-a-new-subscription"></a>Collegare una nuova sottoscrizione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Sottoscrizioni**.

   [![Screenshot che illustra la ricerca di sottoscrizioni nel portale](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Selezionare **Aggiungi** nella parte superiore della pagina.

   ![Screenshot che mostra il pulsante Aggiungi nella visualizzazione Sottoscrizioni](./media/mca-section-invoice/subscription-add.png)

4. Se si ha accesso a più account di fatturazione, selezionare quello relativo al Contratto del cliente Microsoft.

   ![Screenshot che mostra il pulsante Aggiungi nella visualizzazione Sottoscrizioni](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Selezionare il profilo di fatturazione a cui fatturare l'utilizzo della sottoscrizione. I costi per l'utilizzo di Azure e per gli altri acquisti correlati a questa sottoscrizione verranno addebitati nella fattura per il profilo di fatturazione selezionato.

6. Selezionare la sezione della fattura per collegare gli addebiti correlati alla sottoscrizione. Gli addebiti verranno visualizzati in questa sezione della fattura del profilo di fatturazione.

7. Selezionare un piano di Azure e immettere un nome descrittivo per la sottoscrizione.

9. Fare clic su **Crea**.  

### <a name="link-existing-subscriptions-and-products"></a>Collegare sottoscrizioni e prodotti esistenti

Se si hanno già sottoscrizioni di Azure o altri prodotti, ad esempio Azure Marketplace e risorse di origine delle app, è possibile spostarli dalla sezione della fattura in cui si trovano a un'altra sezione, per riorganizzare i costi.

> [!IMPORTANT]
>
> Le sottoscrizioni e gli altri prodotti possono essere spostati solo tra sezioni della fattura appartenenti allo stesso profilo di fatturazione. Lo spostamento di sottoscrizioni e prodotti tra sezioni della fattura in profili di fatturazione diversi non è supportato.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di Sottoscrizioni nel portale](./media/mca-section-invoice/search-cmb.png)

3. Per collegare una sottoscrizione a una nuova sezione della fattura, selezionare **Sottoscrizioni di Azure** sul lato sinistro della schermata. Per altri prodotti, ad esempio Azure Marketplace e le risorse di origine delle app, selezionare **Addebiti ricorrenti**.

   [![Screenshot che illustra l'opzione per il cambiamento della sezione della fattura](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Nella pagina fare clic sui tre puntini di sospensione in corrispondenza della sottoscrizione o del prodotto che si vuole collegare a una nuova sezione della fattura. Selezionare **Cambia la sezione della fattura**.

5. Selezionare la nuova sezione della fattura nell'elenco a discesa. L'elenco a discesa visualizza solo le sezioni della fattura associate allo stesso profilo di fatturazione della sezione della fattura corrente.

    [![Screenshot che illustra la selezione di una nuova sezione della fattura](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Selezionare **Salva**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Aspetti da considerare quando si aggiungono nuovi profili di fatturazione

### <a name="azure-usage-charges-may-be-impacted"></a>Possibile impatto sui costi di utilizzo di Azure

Nell'account di fatturazione per un Contratto del cliente Microsoft, l'utilizzo di Azure viene aggregato ogni mese per ogni profilo di fatturazione. I prezzi per le risorse di Azure con determinazione a livelli vengono stabiliti separatamente in base all'utilizzo per ogni profilo di fatturazione. Per il calcolo del prezzo, l'utilizzo non viene aggregato tra i profili di fatturazione. Questo può influire sul costo complessivo dell'utilizzo di Azure per gli account con più profili di fatturazione.

Verrà ora esaminato un esempio di variazione dei costi per due scenari. I prezzi usati negli scenari sono solo a scopo esemplificativo e non rappresentano i prezzi effettivi dei servizi di Azure.

#### <a name="you-only-have-one-billing-profile"></a>Unico profilo di fatturazione

Si supponga di usare l'archiviazione BLOB in blocchi di Azure, che costa 0,00184 USD per GB per i primi 50 TB e quindi 0,00177 USD per GB per i 450 TB successivi. Per le sottoscrizioni fatturate al profilo di fatturazione sono stati usati 100 TB. Di seguito sono indicati i costi addebitati.

|  Prezzo a livelli (USD) |Quantità | Importo (USD)|
|---------|---------|---------|
|1,84 per TB/mese per i primi 50 TB    | 50 TB        | 92   |
|1,77 per TB/mese per i 450 TB successivi    |  50 TB         | 88,5   |
|Totale     |     100 TB  | 180,5

L'addebito totale per l'uso di 100 TB di dati in questo scenario è di **180,5** USD.

#### <a name="you-have-multiple-billing-profiles"></a>Più profili di fatturazione

Si supponga ora di aver creato un altro profilo di fatturazione e di aver usato 50 GB attraverso le sottoscrizioni fatturate al primo profilo di fatturazione e 50 GB attraverso le sottoscrizioni fatturate al secondo profilo di fatturazione. Di seguito sono indicati i costi addebitati.

`Charges for the first billing profile`

|  Prezzo a livelli (USD) |Quantità | Importo (USD)|
|---------|---------|---------|
|1,84 per TB/mese per i primi 50 TB    | 50 TB        | 92  |
|1,77 per TB/mese per i 450 TB successivi    |  0 TB         | 0,0  |
|Totale     |     50 TB  | 92

`Charges for the second billing profile`

|  Prezzo a livelli (USD) |Quantità | Importo (USD)|
|---------|---------|---------|
|1,84 per TB/mese per i primi 50 TB    | 50 TB        | 92  |
|1,77 per TB/mese per i 450 TB successivi    |  0 TB         | 0,0  |
|Totale     |     50 TB  | 92

L'addebito totale per l'uso di 100 TB di dati in questo scenario è di **184** USD (92 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>I vantaggi delle prenotazioni di Azure potrebbero non applicarsi per tutte le sottoscrizioni

Le prenotazioni di Azure con ambito condiviso vengono applicate alle sottoscrizioni in un unico profilo di fatturazione e non sono condivise tra profili di fatturazione.

![Immagine grafica con informazioni relative all'applicazione di prenotazioni per strutture di account di fatturazione diverse](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

Nell'immagine precedente, Contoso ha due sottoscrizioni. Il vantaggio della prenotazione di Azure viene applicato in modo diverso a seconda della struttura dell'account di fatturazione. Nello scenario a sinistra, il vantaggio della prenotazione viene applicato a entrambe le sottoscrizioni fatturate al profilo di fatturazione del reparto Engineering. Nello scenario a destra, il vantaggio della prenotazione viene applicato solo a Subscription 1 (Sottoscrizione 1), dato che è la sola sottoscrizione fatturata al profilo di fatturazione del reparto Engineering.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'altra sottoscrizione di Azure per il Contratto del cliente Microsoft](create-subscription.md)
- [Gestire i ruoli di fatturazione nel portale di Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure dagli utenti in altri account di fatturazione](mca-request-billing-ownership.md)
