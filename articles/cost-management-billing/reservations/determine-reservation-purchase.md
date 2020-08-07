---
title: Determinare la prenotazione di Azure da acquistare
description: Questo articolo consente di determinare la prenotazione da acquistare.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 08/04/2020
ms.author: banders
ms.openlocfilehash: 909d712e0c8e8ea242c27ab93f16a36e22d36aae
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87759895"
---
# <a name="determine-what-reservation-to-purchase"></a>Determinare la prenotazione da acquistare

Tutte le prenotazioni, ad eccezione di Azure Databricks, vengono applicate su base oraria. È consigliabile acquistare le prenotazioni in base all'utilizzo di base coerente. Esistono diversi modi per determinare cosa acquistare e questo articolo consente di determinare la prenotazione da acquistare.

L'acquisto di una capacità maggiore rispetto all'utilizzo cronologico comporta una prenotazione sottoutilizzata. È consigliabile evitare il sottoutilizzo quando possibile. La capacità riservata non usata non viene trasferita da un'ora a quella successiva. L'utilizzo eccedente la quantità riservata viene addebitato in base a tariffe con pagamento in base al consumo più costose.

## <a name="analyze-usage-data"></a>Analizzare i dati di utilizzo

Usare le sezioni seguenti per analizzare i dati di utilizzo giornalieri per determinare l'utilizzo baseline e la prenotazione da acquistare.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>Analizzare l'utilizzo per l'acquisto di un'istanza di macchina virtuale riservata

Identificare le dimensioni di macchina virtuale appropriate per l'acquisto. Una prenotazione acquistata per le macchine virtuali della serie ES, ad esempio, non si applica alle macchine virtuali della serie E e viceversa.

Le macchine virtuali della serie promo non ricevono uno sconto per la prenotazione, quindi è necessario rimuoverle dall'analisi.

Per limitare i risultati all'utilizzo delle macchine virtuali idonee, applicare i filtri seguenti ai dati di utilizzo:

- Filtrare **MeterCategory** in base a **Macchine virtuali**.
- Recuperare le informazioni relative a **ServiceType** da **AdditionalInfo**. Le informazioni indicano le dimensioni appropriate per la macchina virtuale. Ad esempio, E32 Standard.
- Usare il campo **ResourceLocation** per determinare il data center di utilizzo.

Ignorare le risorse che presentano meno di 24 ore di utilizzo in un giorno.

Per eseguire un'analisi a livello di famiglia delle dimensioni istanza, è possibile ottenere i valori di flessibilità delle dimensioni istanza da [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv). Combinare i valori con i dati per eseguire l'analisi. Per altre informazioni sulla flessibilità delle dimensioni istanza, vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-usage-for-an-azure-synapse-analytics-reserved-instance-purchase"></a>Analizzare l'utilizzo per l'acquisto di un'istanza riservata di Azure Synapse Analytics

La capacità riservata si applica ai prezzi delle DWU di Azure Synapse Analytics. Non si applica al costo della licenza di Azure Synapse Analytics o a costi diversi da quelli di calcolo.

Per limitare i risultati all'utilizzo delle istanze di Azure Synapse Analytics idonee, applicare i filtri seguenti ai dati di utilizzo:


- Filtrare **MeterCategory** in base a **Database SQL**.
- Filtrare **Metername** in base a **vCore**.
- Filtrare **MeterSubCategory** per tutti i record di utilizzo contenenti _Calcolo_ nel nome.

Ottenere il valore per **vCore** da **AdditionalInfo**. Questo valore indica il numero di VCore usati. La quantità corrisponde al valore di **VCore** moltiplicato per il numero di ore in cui è stato usato il database.

I dati segnalano l'utilizzo coerente per:

- Combinazione del tipo di database. Ad esempio: istanza gestita o pool elastico per database singolo.
- Livello di servizio. Ad esempio: utilizzo generico o Business Critical.
- Generazione. Ad esempio, Gen5.
- Percorso della risorsa

### <a name="analysis-for-azure-synapse-analytics"></a>Analisi per Azure Synapse Analytics

La capacità riservata si applica all'utilizzo di DWU per Azure Synapse Analytics e viene acquistata con incrementi di 100 DWU. Per limitare i risultati all'utilizzo delle istanze idonee, applicare i filtri seguenti ai dati di utilizzo:

- Filtrare **Metername** in base a **100 DWU**.
- Filtrare **Sottocategoria del contatore** in base a **Con ottimizzazione per il calcolo Gen2**.

Usare il campo **Posizione risorsa** per determinare l'utilizzo di Azure Synapse Analytics in un'area.

È possibile aumentare o ridurre l'utilizzo di Azure Synapse Analytics nel corso della giornata. Comunicare con il team che ha gestito l'istanza di Azure Synapse Analytics per ottenere informazioni sull'utilizzo di base.

Passare a Prenotazioni nel portale di Azure e acquistare capacità riservata di Azure Synapse Analytics in base a multipli di 100 DWU.

## <a name="reservation-purchase-recommendations"></a>Raccomandazioni per l'acquisto di prenotazioni

Le raccomandazioni per l'acquisto di prenotazioni vengono calcolate analizzando i dati di utilizzo orario negli ultimi 7, 30 e 60 giorni. Azure calcola quali sono i costi se si dispone di una prenotazione e li confronta con i costi effettivi con pagamento in base al consumo sostenuti per la durata dell'intervallo di tempo. Il calcolo viene eseguito per ogni quantità usata durante l'intervallo di tempo. Viene quindi raccomandata la quantità che offre il maggior risparmio.

Si supponga di usare ad esempio 500 macchine virtuali per la maggior parte del tempo e che talvolta l'utilizzo registri un picco di 700 macchine virtuali. In questo esempio, Azure calcola il risparmio sia per 500 macchine virtuali sia per 700 macchine virtuali. Poiché l'utilizzo di 700 macchine virtuali è sporadico, il calcolo della raccomandazione determina che il maggior risparmio si registra con l'acquisto di una prenotazione di 500 macchine virtuali, quindi viene fornita una raccomandazione per la quantità di 500 macchine virtuali.

Tenere presente quanto segue:

- Le raccomandazioni per le prenotazioni vengono calcolate usando le tariffe di utilizzo su richiesta che si applicano all'utente.
- Le raccomandazioni vengono calcolate per dimensioni singole, non per la famiglia di dimensioni dell'istanza.
- La quantità raccomandata per un ambito viene ridotta nello stesso giorno in cui si acquistano le prenotazioni per l'ambito.
    - Tuttavia, un aggiornamento per la raccomandazione relativa alla quantità di prenotazione tra ambiti può richiedere fino a 25 giorni. Se ad esempio si acquista in base alle raccomandazioni per ambito condiviso, la rettifica delle raccomandazioni per l'ambito relativo alla sottoscrizione singola può richiedere fino a 25 giorni.

## <a name="recommendations-in-the-azure-portal"></a>Raccomandazioni nel portale di Azure

Gli acquisti delle prenotazioni calcolati dal motore di raccomandazioni vengono visualizzati nella scheda **Consigliati** del [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs). Di seguito è riportata un'immagine di esempio.

![Immagine che mostra le raccomandazioni](./media/determine-reservation-purchase/select-product-ri.png)

Altre informazioni sulle [raccomandazioni](reserved-instance-purchase-recommendations.md#recommendations-in-the-azure-portal).

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Raccomandazioni nell'app Gestione costi di Power BI

I clienti con contratto Enterprise e con Contratto del cliente Microsoft possono usare i report di copertura delle istanze di macchina virtuale riservate per le raccomandazioni relative a macchine virtuali e acquisti. I report di copertura mostrano l'utilizzo totale e l'utilizzo coperto dalle istanze riservate.

1. Ottenere l'[app Gestione costi](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
2. Passare al report Copertura istanze di macchina virtuale riservate - Ambito singolo o condiviso, a seconda dell'ambito in base a cui si vuole effettuare l'acquisto.
3. Selezionare l'area, la famiglia di dimensioni dell'istanza per visualizzare l'utilizzo, la copertura delle istanze riservate e la raccomandazione di acquisto per il filtro selezionato.

## <a name="recommendations-in-azure-advisor"></a>Raccomandazioni in Azure Advisor

Le raccomandazioni di acquisto delle prenotazioni per le macchine virtuali sono disponibili in [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview).

- In Advisor sono disponibili solo le raccomandazioni relative a una singola sottoscrizione.
- Le raccomandazioni di Advisor vengono calcolate in base ai 30 giorni precedenti. I risparmi stimati si applicano a un periodo di prenotazione di 3 anni.
- Se si acquista una prenotazione con ambito condiviso, la rimozione delle raccomandazioni di acquisto delle prenotazioni di Advisor può richiedere fino a 30 giorni.

## <a name="recommendations-using-apis"></a>Raccomandazioni tramite le API

Usare l'API REST [Raccomandazioni per la prenotazione](/rest/api/consumption/reservationrecommendations/list) per visualizzare le raccomandazioni a livello di codice.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](reserved-instance-windows-software-costs.md)