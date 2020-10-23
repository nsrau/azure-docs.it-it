---
title: Risolvere i problemi di fatturazione di Azure CSP con le tabelle pivot del file di utilizzo
description: Questo articolo illustra la risoluzione dei problemi di fatturazione di Azure Cloud Solution Provider (CSP) mediante le tabelle pivot create dai file di utilizzo in formato CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026602"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Risolvere i problemi di fatturazione di CSP con le tabelle pivot del file di utilizzo

Questo articolo illustra la risoluzione dei problemi di fatturazione di Cloud Solution Provider (CSP) mediante le tabelle pivot nei file di riconciliazione (utilizzo) del Centro per i partner. I file di utilizzo di Azure contengono tutte le informazioni sull'utilizzo e il consumo di Azure. Le informazioni nel file consentono di:

- Ottenere informazioni sul modo in cui le prenotazioni di Azure vengono usate e applicate
- Riconciliare le informazioni in Gestione costi di Azure con la fattura emessa
- Risolvere i problemi correlati a un picco dei costi
- Calcolare l'importo di un rimborso per un contratto di servizio

L'uso delle informazioni dei file di utilizzo consente di migliorare la comprensione e la diagnostica dei problemi correlati all'utilizzo. I file di utilizzo vengono generati in formato CSV. I file di utilizzo possono essere file CSV di grandi dimensioni e risulta quindi più facile modificarli e visualizzarli come tabelle pivot in un'applicazione di foglio di calcolo come Excel. Gli esempi in questo articolo usano Excel, ma è possibile usare qualsiasi applicazione di foglio di calcolo.

Solo gli amministratori fatturazione e gli amministratori globali possono accedere per scaricare i file di riconciliazione. Per altre informazioni, vedere [Informazioni su come leggere le voci nei file di riconciliazione del Centro per i partner](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Ottenere i dati e formattarli

Poiché i file di utilizzo di Azure sono in formato CSV, è necessario preparare i dati per l'uso in Excel. Seguire questi passaggi per formattare i dati come tabella.

1. Scaricare il file di utilizzo seguendo le istruzioni disponibili in [Trovare una fattura](/partner-center/read-your-bill#find-your-bill).
1. Apri il file in Excel.
1. I dati non formattati sono simili all'esempio seguente.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Selezionare il primo campo della tabella, **PartnerID** (IDPartner).
1. Premere CTRL + MAIUSC + Freccia GIÙ e quindi CTRL + MAIUSC + Freccia destra per selezionare tutte le informazioni nella tabella.
1. Nel menu in alto selezionare **Inserisci** > **Tabella**. Nella casella Crea tabella selezionare **Tabella con intestazioni** e quindi selezionare **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Esempio che mostra i dati non formattati in Excel" :::
1. Nel menu in alto selezionare **Inserisci** > **Tabella pivot** e quindi selezionare **OK**. Questa azione crea un nuovo foglio nel file e passa all'area della tabella pivot nel lato destro del foglio.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

L'area Campi di tabella pivot è un'area con trascinamento della selezione. Passare alla sezione successiva per creare la tabella pivot.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Creare una tabella pivot per visualizzare i costi di Azure in base alle risorse

In questa sezione viene creata una tabella pivot in cui è possibile risolvere i problemi dell'utilizzo generale complessivo di Azure. La tabella di esempio può semplificare l'indagine per individuare il servizio che utilizza la quantità più elevata di risorse. In alternativa, è possibile visualizzare le risorse che generano i costi più elevati e ottenere informazioni sul modo in cui vengono applicati addebiti per un servizio.

1. Nell'area Campi di tabella pivot trascinare **Nome servizio** e **Risorsa** nell'area **Righe**. Posizionare **Risorsa** sotto **Nome servizio**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Posizionare quindi **Totale imposte incluse** nell'area **Valori**. È anche possibile usare la colonna Quantità utilizzata per ottenere informazioni sulle unità di consumo e sulle transazioni, ad esempio, GB e ore oppure transazioni invece del costo in diverse valute, come USD, EUR e INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. È ora disponibile un dashboard per l'indagine generalizzata del consumo. È possibile filtrare per visualizzare un servizio specifico usando le opzioni di filtro nella tabella pivot.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Per filtrare un secondo livello in una tabella pivot, ad esempio una risorsa, selezionare un elemento di secondo livello nella tabella.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Per filtri aggiuntivi, è possibile aggiungere **IDSottoscrizione** e **Nome società del cliente** all'area **Filtri** e selezionare l'ambito specifico.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Creare una tabella pivot per visualizzare l'utilizzo di Azure in base alla data

In questa sezione viene creata una tabella pivot in cui è possibile risolvere i problemi dell'utilizzo generale complessivo di Azure in base a Quantità utilizzata e data. L'identificazione dei picchi di fatturazione in base a data e servizio risulta utile. In alternativa, è possibile visualizzare le risorse che generano i costi più elevati e ottenere informazioni sul modo in cui vengono applicati addebiti per un servizio.

Il file di riconciliazione include due tabelle. Una tabella si trova nella parte superiore (tabella principale) e un'altra tabella è disponibile nella parte inferiore del documento. La seconda tabella include molte informazioni della prima tabella, ma non include i dettagli su prezzi o costo. Include la data di utilizzo e la quantità utilizzata.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Usare la stessa procedura della sezione [Ottenere i dati e formattarli](#get-the-data-and-format-it) per creare una tabella di Excel con le informazioni disponibili nella parte inferiore del file di riconciliazione.
1. Quando la tabella è pronta ed è disponibile nel foglio della tabella pivot, usare gli stessi passaggi della sezione create-pivot-table-to-view-azure-costs-by-resources per preparare il dashboard. Invece di usare Totale imposte incluse, inserire **Quantità utilizzata** nell'area **Valori**.
1. Aggiungere **Data di utilizzo** alla sezione delle colonne. La tabella pivot dovrebbe avere un aspetto simile all'esempio seguente.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. È ora disponibile un dashboard che mostra l'utilizzo per ogni data. È possibile estendere ogni mese selezionando il simbolo **+** .

Il dashboard mostra la quantità utilizzata in unità come GB, ore e trasferimenti.

Per visualizzare il prezzo per giorno, è possibile aggiungere **GUID risorsa** all'area **Righe**. Nella tabella superiore aggiungere il prezzo unitario (**ListPrice**, PrezzoDiListino) per la risorsa. Moltiplicare **ListPrice** (PrezzoDiListino) per **Quantità utilizzata** per calcolare gli addebiti al lordo delle imposte. Gli importi devono corrispondere.

Alcune risorse (servizi) hanno prezzi dimensionati in base alla quantità utilizzata. Alcune risorse, ad esempio, hanno un prezzo superiore per i primi 100 GB utilizzati e un prezzo inferiore per i GB usati successivamente. Quando si calcolano manualmente i costi, occorre tenere in considerazione i prezzi dimensionati.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Creare una tabella pivot per visualizzare il costo per una risorsa specifica

Una singola risorsa può comportare diversi addebiti per servizi diversi. È ad esempio possibile che a una macchina virtuale vengano applicati addebiti per risorse di calcolo, licenze del sistema operativo, larghezza di banda (trasferimenti dei dati), utilizzo di istanze riservate e archiviazione per snapshot. Quando si vuole esaminare l'utilizzo complessivo per risorse specifiche, i passaggi seguenti illustrano in modo dettagliato la creazione di un dashboard per visualizzare l'utilizzo complessivo con i file di utilizzo.

I file di riconciliazione non contengono dettagli specifici per le risorse. È quindi necessario usare il file di utilizzo aggregato. Contattare il [supporto tecnico per la fatturazione di Azure](https://go.microsoft.com/fwlink/?linkid=2083458) per richiedere il file di utilizzo aggregato per la sottoscrizione. I file aggregati vengono generati a livello di sottoscrizione. I dati non formattati sono simili all'esempio seguente.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

Il file contiene le colonne seguenti.

- **UsageStart** (InizioUtilizzo) e **UsageEnd** (FineUtilizzo)- Data per ogni voce, ovvero ogni unità di utilizzo. Ad esempio, ogni giorno.
- **MeteredResourceID** (IDRisorsaAConsumo)- In Azure corrisponde all'ID contatore.
- **Proprietà** - Contiene l'ID istanza (nome della risorsa) con altri dettagli, ad esempio la posizione.
- **Quantità** - Quantità utilizzata nel file di riconciliazione.

1. Selezionare il primo campo della tabella, **PartnerID** (IDPartner).  
1. Premere CTRL + MAIUSC + Freccia GIÙ e quindi CTRL + MAIUSC + Freccia destra per selezionare tutte le informazioni nella tabella.
1. Nel menu in alto selezionare **Inserisci** > **Tabella**. Nella casella Crea tabella selezionare **Tabella con intestazioni** e quindi selezionare **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Esempio che mostra i dati non formattati in Excel" :::
1. Nel menu in alto selezionare **Inserisci** > **Tabella pivot** e quindi selezionare **OK**. Questa azione crea un nuovo foglio nel file e passa all'area della tabella pivot nel lato destro del foglio.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Aggiungere quindi **MeteredResourceID** (IDRisorsaAConsumo) all'area **Righe** e **Quantità** a **Valori**. I risultati mostrano le informazioni sull'utilizzo complessivo. Per dettagli aggiuntivi, inserire **UsageEndDateTime** (DataOraFineUtilizzo) nell'area **Colonne**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Per visualizzare un report complessivo, aggiungere **Proprietà** a **Righe** in **MeteredResourceID** (IDRisorsaAConsumo). Mostra un dashboard completo per l'utilizzo.
1. Per filtrare in base a una risorsa specifica, aggiungere **Proprietà** all'area **Filtri** e selezionare l'utilizzo desiderato. È possibile usare la ricerca per trovare un nome di risorsa.
    Per visualizzare il costo per la risorsa, trovare la quantità utilizzata totale e moltiplicare il valore per il prezzo di listino. Il prezzo di listino è specifico per ogni GUID di risorsa (MeteredResourceID, IDRisorsaAConsumo). Se una risorsa utilizza molti MeteredResourceID (IDRisorsaAConsumo), è necessario annotare il valore totale per ogni ID.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Gestione costi di Azure per i partner](../costs/get-started-partners.md).