---
title: Fatture per la registrazione ad Azure Enterprise
description: Questo articolo illustra come gestire la fattura della registrazione EA di Azure.
author: bandersmsft
ms.author: banders
ms.date: 06/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 16f89fa99c9619e3956a089763ece1b96aff4480
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298126"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Fatture per la registrazione ad Azure Enterprise

Questo articolo illustra come gestire la fattura del Contratto Enterprise (EA, Enterprise Agreement) di Azure. La fattura è una rappresentazione del conto. Esaminarla per verificarne la correttezza. È anche consigliabile acquisire familiarità con altre attività che potrebbero essere necessarie per la gestione della fattura.

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>Modificare un numero di ordine di acquisto per una fattura per eccedenza futura

Azure Enterprise Portal genera automaticamente un numero di ordine d'acquisto predefinito, a meno che l'amministratore aziendale non ne imposti uno prima della data della fattura. L'amministratore aziendale può aggiornare il numero di ordine d'acquisto fino a sette giorni dopo la ricezione di un messaggio di posta elettronica automatico di notifica della fattura.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Per aggiornare il numero di ordine di acquisto dei servizi di Azure:

1. In Azure Enterprise Portal selezionare **Report** > **Usage Summary** (Riepilogo utilizzo).
1. Nell'angolo superiore destro selezionare **Edit PO Numbers** (Modifica numeri ordini di acquisto).
1. Selezionare il pulsante di opzione **Servizi di Azure**.
1. Scegliere un intervallo di date dal menu a discesa del campo **Periodo di fatturazione**.

   È possibile modificare un numero di ordine d'acquisto nei sette giorni successivi alla ricezione di una notifica di fatturazione, ma prima di pagare la fattura.
1. Immettere un nuovo numero di ordine d'acquisto nel campo **Numero di ordine di acquisto**.
1. Selezionare **Salva** per inviare la modifica.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Per aggiornare il numero di ordine d'acquisto di Azure Marketplace:

1. In Azure Enterprise Portal selezionare **Report** > **Usage Summary** (Riepilogo utilizzo).
1. Nell'angolo superiore destro selezionare **Edit PO Numbers** (Modifica numeri ordini di acquisto).
1. Selezionare il pulsante di opzione **Marketplace**.
1. Scegliere un intervallo di date dal menu a discesa del campo **Periodo di fatturazione**.

   È possibile modificare un numero di ordine d'acquisto nei sette giorni successivi alla ricezione di una notifica di fatturazione, ma prima di pagare la fattura.
1. Immettere un nuovo numero di ordine d'acquisto nel campo **Numero di ordine di acquisto**.
1. Selezionare **Salva** per inviare la modifica.

## <a name="azure-enterprise-billing-frequency"></a>Frequenza di fatturazione di Azure Enterprise

Microsoft addebita tutti gli acquisti di servizi di Microsoft Azure una volta all'anno in corrispondenza della data di entrata in vigore della registrazione. Per qualsiasi utilizzo in eccesso rispetto agli importi dell'impegno, Microsoft fattura in modo posticipato.

- I corrispettivi dell'impegno vengono suddivisi in base a una tariffa mensile e addebitati annualmente in anticipo.
- I corrispettivi dell'eccedenza vengono calcolati ogni mese e addebitati in via posticipata alla fine del periodo di fatturazione.

### <a name="billing-intervals"></a>Intervalli di fatturazione

L'intervallo di fatturazione dipende dalla modalità scelta per effettuare gli acquisti degli impegni. L'impegno annuale coincide con una delle date seguenti:

- Data di ricorrenza annuale della registrazione
- Data di validità della sottoscrizione di modifica di un anno.

La data di ricezione della fattura per eccedenza dipende dalla data di inizio e di impostazione della registrazione:

- **Registrazioni dirette con data di inizio precedente al 1° maggio 2018**:
  - Se si ha un Contratto Enterprise (EA) diretto, il periodo di fatturazione per i servizi di Azure è annuale e sono esclusi i servizi di Azure Marketplace. Il periodo di fatturazione è basato sulla data di ricorrenza annuale, ovvero la data in cui il contratto è diventato effettivo.
  - Se si supera il 150% della soglia di impegno monetario (MC) del Contratto Enterprise, si passerà automaticamente a un periodo di fatturazione trimestrale basato sulla data di ricorrenza. Si riceverà anche una fattura per eccedenza dei servizi Azure.
  - Se non si supera il 150% della soglia MC, la registrazione manterrà un periodo di fatturazione annuale. La fattura per eccedenza verrà ricevuta alla fine dell'anno di impegno.

- **Registrazioni dirette con data di inizio successiva al 1° maggio 2018**:
  - Le fatture relative all'utilizzo di Azure e agli addebiti fatturati separatamente hanno un periodo di fatturazione mensile.
  - Eventuali addebiti non coperti dall'impegno monetario sono dovuti come pagamenti per eccedenze.  

- **Registrazioni indirette con data di inizio precedente al 1° maggio 2018**:

  Se si è un cliente con Contratto Enterprise (EA) indiretto con data di inizio precedente al 1° maggio 2018, verrà impostato un periodo di fatturazione trimestrale. Il partner di canale (CP) emette direttamente la fattura.  

- **Registrazioni indirette con data di inizio successiva al 1° maggio 2018**:

  Il periodo di fatturazione è mensile.  

### <a name="increase-your-monetary-commitment"></a>Aumentare l'impegno monetario

È possibile aumentare l'impegno in qualsiasi momento. Verranno fatturati i mesi rimanenti del periodo di impegno annuale. Ad esempio, se si effettua la registrazione per una sottoscrizione di modifica di un anno e si aumenta l'impegno nel corso del sesto mese, verranno fatturati i restanti sei mesi del periodo di validità. Le quantità dell'impegno verranno quindi aggiornate per gli ultimi sei mesi del periodo di validità dell'impegno. Le nuove quantità verranno usate per determinare gli addebiti delle eventuali eccedenze.

### <a name="overage"></a>Eccedenza

In caso di eccedenza, vengono fatturati l'utilizzo o le prenotazioni che hanno superato l'impegno durante il periodo di fatturazione. Per visualizzare una suddivisione del calcolo delle quantità in eccedenza per le singole voci, fare riferimento al report di riepilogo dell'utilizzo oppure contattare il partner di canale.

Per ogni voce in fattura verrà visualizzato quanto segue:

- **Importo totale**: gli addebiti totali
- **Utilizzo dell'impegno**: la quantità dell'impegno usata per coprire gli addebiti
- **Importo netto**: gli addebiti che superano l'impegno

Le imposte applicabili vengono calcolate solo sull'importo netto in eccedenza rispetto all'impegno.

La fatturazione dell'eccedenza è automatizzata. La tempistica delle notifiche e delle fatture dipende dalla data di fine del periodo di fatturazione.

- La notifica dell'eccedenza viene in genere inviata sette giorni dopo la data di fine della fatturazione.
- Le fatture per eccedenze vengono inviate da sette a nove giorni dopo la notifica.
- È possibile esaminare gli addebiti e aggiornare i numeri di ordine d'acquisto generati dal sistema durante i sette giorni che intercorrono tra la notifica dell'eccedenza e la fatturazione.

### <a name="azure-marketplace"></a>Azure Marketplace

Dal periodo di fatturazione di aprile 2019, i clienti hanno iniziato a ricevere un'unica fattura per tutti gli addebiti di Azure e Azure Marketplace anziché due fatture distinte. Questa modifica non si applica ai clienti in Australia, Giappone o Singapore.

Durante la transizione alla fattura unica, si riceverà una fattura parziale per Azure Marketplace. Questa fattura separata finale conterrà gli addebiti per Azure Marketplace precedenti alla data di aggiornamento della fatturazione. Gli addebiti per Azure Marketplace successivi a questa data verranno inclusi nella fattura di Azure. Dopo il periodo di transizione, tutti gli addebiti per Azure e Azure Marketplace saranno inclusi nella fattura unica.  

### <a name="adjust-billing-frequency"></a>Modificare la frequenza di fatturazione

La frequenza di fatturazione di un cliente è annuale, trimestrale o mensile. Il ciclo di fatturazione viene determinato quando il cliente firma il contratto. La fatturazione mensile è l'intervallo di fatturazione più breve.

- Per modificare un periodo di fatturazione da annuale a trimestrale per le registrazioni dirette, è necessaria l'**approvazione** di un amministratore aziendale. Per le registrazioni indirette è necessaria l'approvazione di un amministratore partner. La modifica diventa effettiva alla fine del trimestre di fatturazione corrente.
- Per cambiare un periodo di fatturazione da annuale o trimestrale a mensile, è necessaria una **modifica** del contratto.  Qualsiasi modifica al periodo di fatturazione della registrazione esistente richiede l'approvazione di un amministratore aziendale o della persona identificata come "contatto fatturazione".
- **Inviare** l'approvazione al [supporto di Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Selezionare la categoria di problema: **Fatturazione**.

La modifica diventa effettiva alla fine del trimestre di fatturazione corrente.

Se viene firmata una modifica M503, è possibile cambiare la frequenza di fatturazione di qualsiasi contratto in mensile, qualunque sia la frequenza originale. 

### <a name="request-an-invoice-copy"></a>Richiedere una copia della fattura

Per richiedere una copia della fattura, contattare il proprio partner.

## <a name="credits-and-adjustments"></a>Crediti e rettifiche

È possibile visualizzare tutti i crediti o le rettifiche applicate alla registrazione nella sezione **Report** di [Azure Enterprise Portal](https://ea.azure.com).

Per visualizzare i crediti:

1. In [Azure Enterprise Portal](https://ea.azure.com) selezionare la sezione **Report**.
1. Selezionare **Usage Summary** (Riepilogo utilizzo).
1. Nell'angolo superiore destro cambiare visualizzazione passando da **M** a **C**.
1. Estendere il campo di rettifica nella tabella Impegno del servizio di Azure.
1. Verranno visualizzati i crediti applicati alla registrazione e una breve descrizione. Ad esempio: Credito per contratto di servizio.

## <a name="pay-your-overage-with-your-monetary-commitment"></a>Pagare l’eccedenza con l'impegno monetario

Per applicare l'impegno monetario alle eccedenze, è necessario soddisfare i criteri seguenti:

- Sono stati sostenuti addebiti per eccedenze che non sono stati pagati e che rientrano entro un anno dalla data di fine del servizio fatturato.
- L'importo dell'impegno monetario disponibile copre il numero totale di addebiti sostenuti, incluse tutte le fatture di Azure precedenti non pagate.
- Il periodo di fatturazione che si vuole completare deve essere completamente chiuso. La fatturazione viene chiusa completamente dopo il quinto giorno di ogni mese.
- Il periodo di fatturazione che si vuole compensare deve essere completamente chiuso.
- Lo Sconto per impegno in Azure è basato sul nuovo impegno effettivo meno gli eventuali fondi pianificati per il consumo precedente. Questo requisito si applica solo agli addebiti per eccedenza contratti. Poiché è valido solo per i servizi che utilizzano l'impegno monetario, non si applica agli addebiti per Azure Marketplace. Gli addebiti per Azure Marketplace vengono fatturati separatamente.

Per completare una compensazione dell'eccedenza, l'utente o il team degli account può aprire una richiesta di supporto. È necessaria un'approvazione tramite posta elettronica da parte dell'amministratore aziendale o del contatto fatturazione.

## <a name="move-charges-to-another-enrollment"></a>Spostare gli addebiti in un'altra registrazione

I dati di utilizzo vengono spostati solo quando un trasferimento viene retrodatato. Sono disponibili due opzioni per spostare i dati di utilizzo da una registrazione a un'altra:

- Trasferimenti di account da una registrazione a un'altra
- Trasferimenti della registrazione da una registrazione a un'altra

Per entrambe le opzioni, è necessario inviare una [richiesta di supporto](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) al team di supporto EA per assistenza. 

## <a name="enterprise-agreement-usage-calculations"></a>Calcoli di utilizzo del Contratto Enterprise

Per informazioni su prezzi pubblici di base, unità di misura, domande frequenti e linee guida per la creazione di report sull'utilizzo per ogni singolo servizio, vedere [Servizi di Azure](https://azure.microsoft.com/services/) e [Prezzi di Azure](https://azure.microsoft.com/pricing/). Altre informazioni sui calcoli del Contratto Enterprise sono disponibili nelle sezioni seguenti.

### <a name="enterprise-agreement-units-of-measure"></a>Unità di misura del Contratto Enterprise

Le unità di misura per i contratti Enterprise sono spesso diverse da quelle di altri programmi Microsoft, ad esempio il Contratto di Sottoscrizione Microsoft Online. Questa differenza indica che, per diversi servizi, l'unità di misura viene aggregata per offrire prezzi normalizzati. L'unità di misura nella visualizzazione Usage Summary (Riepilogo utilizzo) di Azure Enterprise Portal è sempre la misura Enterprise. Un elenco completo delle unità di misura e conversioni correnti per ogni servizio è disponibile nel file di Excel [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Regole di arrotondamento

Azure Enterprise Portal applica la logica di arrotondamento gaussiano o del banchiere dello standard IEEE. Questa logica arrotonda i numeri alla cifra pari più vicina per i valori non interi. La logica di arrotondamento per eccesso più comune arrotonda sempre le cifre non intere alla successiva cifra più alta. Questo metodo di Azure Enterprise Portal effettivamente fornisce una somma totale più precisa per il gruppo rispetto alla logica standard di Excel.

Ovvero, quando la prima cifra eliminata è il 5 e non sono presenti cifre che seguono oppure le cifre che seguono sono zeri, viene eseguito l'arrotondamento alla cifra pari più vicina. Ad esempio, 2,315 e 2,325 arrotondati al centesimo più vicino diventano 2,32.

Come riferimento, la tabella seguente mostra le formule di Excel che è possibile usare per modellare le regole di Azure Enterprise Portal per l'arrotondamento e la conversione:

| Scenario | Formula della logica del banchiere |
| --- | --- |
| Arrotondamento dell'utilizzo | =MROUND({_origine_}, 0,0002) |
| Arrotondamento dei prezzi (due decimali) | =MROUND({_origine_}, 0,02) |
| Arrotondamento dei prezzi (zero decimali) | =MROUND({_origine_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Conversione tra il report relativo ai dettagli dell'utilizzo e la pagina di riepilogo dell'utilizzo

Il report dei dati di utilizzo scaricati include l'utilizzo delle risorse non elaborato fino a sei posizioni decimali. Tuttavia, i dati di utilizzo visualizzati in Azure Enterprise Portal sono arrotondati a quattro posizioni decimali per le unità di impegno e troncati a zero decimali per le unità di eccedenza. I dati di utilizzo non elaborati vengono arrotondati a quattro cifre prima della conversione nelle unità usate in Azure Enterprise Portal. Le unità Enterprise convertite vengono quindi arrotondate di nuovo a quattro cifre. Le ore effettive utilizzate prima della conversione sono visibili solo nel report sull'utilizzo scaricato e non in Azure Enterprise Portal.

Ad esempio: Si supponga che il report relativo ai dettagli dell'utilizzo indichi 694,533404 ore di SQL Server effettive. Queste unità vengono convertite in 6,94533404 di 100 ore di calcolo e quindi arrotondate a 6,9453 e visualizzate in Azure Enterprise Portal.

- Per determinare l'importo di fatturazione totale, le unità visualizzate vengono moltiplicate per il prezzo unitario dell'impegno e il risultato viene troncato a due decimali. Per lo yen giapponese (JPY) e il won coreano (KRW), l'importo totale viene arrotondato a zero decimali.
- Per l'eccedenza, le unità di fatturazione vengono troncate a sei cifre e quindi moltiplicate per il prezzo unitario di eccedenza per determinare l'importo di fatturazione totale.
- Per la fatturazione del provider di servizi gestiti (MSP), tutto l'utilizzo associato a un reparto contrassegnato come MSP viene troncato a zero decimali dopo la conversione nell'unità di misura del Contratto Enterprise. Di conseguenza, la somma di questo utilizzo potrebbe essere inferiore alla somma totale di tutti i dati di utilizzo visualizzati in Azure Enterprise Portal, a seconda che il provider di servizi gestiti rientri o meno nel saldo dell'impegno monetario o sia in eccedenza.

### <a name="graduated-pricing"></a>Prezzi progressivi

I prezzi del Contratto Enterprise attualmente non includono i prezzi progressivi in cui gli addebiti per unità diminuiscono man mano che l'utilizzo aumenta. Quando si passa da un Contratto di Sottoscrizione Microsoft Online con prezzi progressivi a un Contratto Enterprise, i prezzi sono proporzionali al livello di base del servizio meno eventuali rettifiche applicabili per gli sconti del Contratto Enterprise.

### <a name="partial-hour-billing"></a>Fatturazione di ore parziali

Tutto l'utilizzo viene fatturato in base ai minuti convertiti in ore parziali e non in base a incrementi di ore intere. Le tariffe Enterprise orarie elencate vengono applicate alle ore totali più le ore parziali.

### <a name="average-daily-consumption"></a>Utilizzo giornaliero medio

Per alcuni servizi i prezzi sono su base mensile, mentre l'utilizzo è indicato su base giornaliera. In questi casi, l'utilizzo viene calcolato una volta al giorno, suddiviso per 31 e sommato per il numero di giorni incluso nel mese di fatturazione. Di conseguenza, le tariffe non sono mai più alte di quanto previsto per un mese e sono leggermente inferiori per i mesi con meno di 31 giorni.

### <a name="compute-hours-conversion"></a>Conversione delle ore di calcolo

Prima del 28 gennaio 2016, l'utilizzo di macchine virtuali A0, A2, A3 e A4 Standard e Basic e dei servizi cloud veniva calcolato come minuti del contatore della macchina virtuale A1. Le macchine virtuali A0 venivano conteggiate come frazioni dei minuti della macchina virtuale A1, mentre le macchine virtuali A2, A3 e A4 venivano convertite in multipli. Poiché questi criteri hanno causato confusione per i clienti, è stata implementata una modifica che assegna l'utilizzo per minuto ai contatori A0, A2, A3 e A4 dedicati.

La nuova misurazione è diventata effettiva tra il 27 e il 28 gennaio 2016. Nel file CSV scaricato che mostra l'utilizzo durante questo periodo di transizione sono riportati:

- Utilizzo calcolato con il contatore A1
- Utilizzo calcolato con il nuovo contatore dedicato che corrisponde alla dimensione della distribuzione.

| **Dimensione della distribuzione** | **Utilizzo calcolato come multiplo di A1 fino al 26 gennaio 2016** | **Utilizzo calcolato con un contatore dedicato a partire dal 27 gennaio 2016** |
| --- | --- | --- |
| A0 | 0,25 dell'ora A1 | 1 ora A0 |
| A2 | 2 ore A1 | 1 ora A2 |
| A3 | 4 ore A1 | 1 ora A3 |
| A4. | 8 ore A1 | 1 ora A4 |

### <a name="regions"></a>Regioni

Per i servizi in cui la zona e l'area influiscono sui prezzi, vedere la tabella seguente per una mappa delle zone e delle aree geografiche:

| Area geografica | Aree di trasferimento dati | Aree CDN |
| --- | --- | --- |
| Zona 1 | Europa settentrionale <br> Europa occidentale <br> Stati Uniti occidentali <br> Stati Uniti orientali <br> Stati Uniti centro-settentrionali <br> Stati Uniti centro-meridionali <br> Stati Uniti orientali 2 <br> Stati Uniti centrali | America del Nord <br> Europa |
| Zona 2 | Asia Pacifico orientale <br> Asia Pacifico sud-orientale <br> Giappone orientale <br> Giappone occidentale <br> Australia orientale <br> Australia sud-orientale | Asia Pacifico <br> Giappone <br> America Latina <br> Medio Oriente/Africa <br> Australia orientale <br> Australia sud-orientale |
| Zona 3 | Brasile meridionale |   |

Non sono previsti addebiti per i dati in uscita tra i servizi ospitati all'interno dello stesso data center. Ad esempio, Office 365 e Azure.

### <a name="monetary-commitment-and-unbilled-usage"></a>Impegno monetario e utilizzo non fatturato

L'impegno monetario di Azure è un importo pagato in anticipo per i servizi di Azure. L'impegno monetario viene utilizzato man mano che si usano i servizi. I servizi proprietari di Azure vengono fatturati in base all'impegno monetario. Tuttavia, alcuni addebiti vengono fatturati separatamente, mentre i servizi di Azure Marketplace non utilizzano l'impegno monetario.

### <a name="charges-billed-separately"></a>Addebiti fatturati separatamente

Alcuni prodotti e servizi forniti da origini di terze parti non utilizzano l'impegno monetario di Azure. Questi elementi vengono fatturati separatamente come parte della fattura per eccedenza del periodo di fatturazione standard.

Tutti gli addebiti per Azure e Azure Marketplace sono stati riuniti in un'unica fattura allineata al periodo di fatturazione della registrazione. La fattura unica non si applica ai clienti in Australia, Giappone o Singapore.

La fattura unica riporta innanzitutto l'utilizzo di Azure, seguito da eventuali addebiti per Azure Marketplace. Per i clienti in Australia, Giappone o Singapore, gli addebiti per Azure Marketplace sono inclusi in una fattura separata.

Se alla fine del periodo di fatturazione standard non risulta alcun utilizzo in eccedenza, gli addebiti calcolati separatamente verranno fatturati separatamente. Questo processo si applica ai clienti in Australia, Giappone e Singapore.

I servizi seguenti vengono fatturati separatamente:

- Canonical
- Citrix XenApp Essentials
- Utente registrato Citrix XenDesktop
- Openlogic
- Utente registrato XenApp Essentials con Diritti di accesso remoto
- Ubuntu Advantage
- Visual Studio Enterprise (mensile)
- Visual Studio Enterprise (annuale)
- Visual Studio Professional (mensile)
- Visual Studio Professional (annuale)

## <a name="what-to-expect-after-change-of-channel-partner"></a>Cosa aspettarsi dopo la modifica del partner del canale

Se la modifica del partner canale viene eseguita a metà del mese, il cliente riceverà una fattura per l'utilizzo con il precedente partner associato e un'altra fattura per l'utilizzo con il nuovo partner.

Le fatture verranno rilasciate il mese successivo al termine del periodo di fatturazione. Se la cadenza di fatturazione è mensile, la fattura di settembre verrà rilasciata nel mese di ottobre per entrambi i partner. Se il ciclo di fatturazione è trimestrale o annuale, il cliente può aspettarsi una fattura per il precedente partner associato per l'utilizzo nel relativo periodo, mentre il restante periodo riguarderà il nuovo partner in base alla cadenza di fatturazione.

## <a name="next-steps"></a>Passaggi successivi

- I file di Excel seguenti offrono informazioni dettagliate sui servizi di Azure e vengono aggiornati il giorno 6 e 20 di ogni mese:

   | Titolo | Descrizione | Nome file |
   | --- | --- | --- |
   | [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Elenca tutti i servizi attivi e include: <br>  <ul><li>categoria del servizio</li>   <li>nome descrittivo del servizio</li>   <li>nome dell'impegno e numero di parte</li> <li>nome del consumo e numero di parte</li>   <li>unità di misura</li>   <li>fattori di conversione tra utilizzo indicato nel report e utilizzo visualizzato in Azure Enterprise Portal</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Service Download Fields](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Questo foglio di calcolo fornisce un elenco di tutte le possibili combinazioni dei campi relativi ai servizi nel report di download dell'utilizzo. | Service\_Download\_Fields.xlsx |

- Per informazioni su come comprendere la fattura e gli addebiti, vedere [Informazioni sulla fattura del contratto Enterprise di Azure](../understand/review-enterprise-agreement-bill.md).
- Per iniziare a usare Azure Enterprise Portal, vedere [Introduzione ad Azure Enterprise Portal](ea-portal-get-started.md).
