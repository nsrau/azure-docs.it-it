---
title: Azure Site Recovery Deployment Planner per distribuzioni da VMware ad Azure | Microsoft Docs
description: Guida dell'utente di Azure Site Recovery Deployment Planner.
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/28/2017
ms.author: nisoneji
ms.openlocfilehash: 36309eb85244435a853013448c83d125420c001c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="cost-estimation-report-of-azure-site-recovery-deployment-planner"></a>Report di stima dei costi di Azure Site Recovery Deployment Planner  

Il report di Deployment Planner offre il riepilogo della stima dei costi nei fogli [Recommendations](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) (Raccomandazioni) e l'analisi dettagliata dei costi nel foglio Cost Estimation (Stima costi), che contiene l'analisi dettagliata dei costi per VM. 

### <a name="cost-estimation-summary"></a>Riepilogo della stima dei costi 
Il grafico offre la visualizzazione di riepilogo del costo stimato totale del ripristino di emergenza in Azure per l'area di destinazione scelta e la valuta specificata per la generazione del report.
Riepilogo della stima dei costi

![Riepilogo della stima dei costi](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Il riepilogo consente di conoscere il costo che è necessario sostenere per archiviazione, calcolo, rete e licenza in caso di protezione di tutte le VM compatibili in Azure con Azure Site Recovery. Il costo viene calcolato sulle VM compatibili e non su tutte le VM profilate.  
 
È possibile visualizzare i costi su base mensile o annuale. Vedere altre informazioni sulle [aree di destinazione supportate](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) e sulle [valute supportate](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Cost by components** (Costo per componente): il costo totale per il ripristino di emergenza è suddiviso tra quattro componenti, ovvero calcolo, archiviazione, rete e costo della licenza di Azure Site Recovery. Il costo viene calcolato in base all'utilizzo che si verificherà durante la replica e in fase di esercitazione sul ripristino di emergenza per il calcolo, l'archiviazione (Premium e Standard), la connessione ExpressRoute/VPN configurata tra il sito locale e Azure e la licenza di Azure Site Recovery.

**Cost by states** (Costo per stato). Il costo totale del ripristino di emergenza viene classificato in base a due diversi stati: Replication (Replica) e DR-Drill (Esercitazione sul ripristino di emergenza). 

**Costo della replica**: costo che verrà addebitato durante la replica. Copre il costo dell'archiviazione, della rete e della licenza di Azure Site Recovery. 

**Costo dell'esercitazione sul ripristino di emergenza**: costo che verrà addebitato durante i failover di test. Durante il failover di test, Azure Site Recovery attiva macchine virtuali. Il costo dell'esercitazione sul ripristino di emergenza copre il costo di calcolo e di archiviazione delle VM in esecuzione. 

**Azure storage cost per Month/Year** (Costo di archiviazione di Azure al mese/all'anno). Mostra il costo di archiviazione totale che verrà addebitato per l'archiviazione Premium e Standard per la replica e l'esercitazione sul ripristino di emergenza.

## <a name="detailed-cost-analysis"></a>Analisi dettagliata dei costi
I prezzi di Azure per il calcolo, l'archiviazione, la rete e così via variano nelle diverse aree di Azure. È possibile generare un report di stima dei costi con i prezzi di Azure più recenti in base alla sottoscrizione, all'offerta associata alla sottoscrizione e all'area di Azure di destinazione specificata, nella valuta indicata. Per impostazione predefinita, lo strumento usa l'area di Azure Stati Uniti occidentali 2 e il dollaro USA (USD) come valuta. Se sono state usate un'altra area e un'altra valuta, alla successiva generazione di un report senza ID sottoscrizione, ID offerta, area di destinazione e valuta verranno applicati i prezzi dell'ultima area di destinazione usata e l'ultima valuta usata per la stima dei costi.
Questa sezione mostra l'ID sottoscrizione e l'ID offerta usati per la generazione del report.  Se non ne sono stati usati, è vuota.

Nell'intero report, le celle contrassegnate in grigio sono di sola lettura. Le celle in bianco possono essere modificate in base ai propri requisiti.

![Dettagli della stima dei costi 1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Costo complessivo del ripristino di emergenza per componente
La prima sezione mostra il costo complessivo del ripristino di emergenza per componente e per stato. 

**Compute** (Calcolo): costo delle VM IaaS eseguite in Azure per le esigenze del ripristino di emergenza. Include le VM create da Azure Site Recovery durante le esercitazioni sul ripristino di emergenza (failover di test) e le VM in esecuzione in Azure come SQL Server con Gruppi di disponibilità AlwaysOn e controller di dominio/Domain Name Server.

**Storage** (Archiviazione): costo dell'utilizzo delle risorse di archiviazione di Azure per le esigenze del ripristino di emergenza. Include l'utilizzo delle risorse di archiviazione per la replica e durante le esercitazioni sul ripristino di emergenza.
Network (Rete): costo della connessione ExpressRoute e VPN da sito a sito per le esigenze del ripristino di emergenza. 

**ASR license** (Licenza ASR): costo della licenza di Azure Site Recovery per tutte le VM compatibili. Se nella tabella dell'analisi dettagliata dei costi è stata immessa manualmente una VM, viene incluso anche il costo della licenza di Azure Site Recovery per tale VM.

### <a name="overall-dr-cost-by-states"></a>Costo complessivo del ripristino di emergenza per stato
Il costo totale del ripristino di emergenza viene classificato in base a due diversi stati: Replication (Replica) e DR-Drill (Esercitazione sul ripristino di emergenza).

**Costo della replica**: il costo viene addebitato al momento della replica. Copre il costo dell'archiviazione, della rete e della licenza di Azure Site Recovery. 

**Costo dell'esercitazione sul ripristino di emergenza**: il costo viene addebitato al momento delle esercitazioni sul ripristino di emergenza. Durante tali esercitazioni, Azure Site Recovery attiva macchine virtuali. Il costo dell'esercitazione sul ripristino di emergenza copre il costo di calcolo e di archiviazione delle VM in esecuzione.
La durata totale delle esercitazioni sul ripristino di emergenza in un anno è uguale al numero di esercitazioni sul ripristino di emergenza moltiplicato per la durata di ognuna (in giorni). Il costo medio delle esercitazioni sul ripristino di emergenza (al mese) è uguale al costo totale delle esercitazioni sul ripristino di emergenza diviso 12.

### <a name="storage-cost-table"></a>Tabella del costo di archiviazione
Questa tabella mostra il costo di archiviazione Premium e Standard addebitato per la replica e le esercitazioni sul ripristino di emergenza con e senza sconto.

### <a name="site-to-azure-network"></a>Rete da sito ad Azure
Selezionare l'impostazione appropriata in base ai propri requisiti. 

**ExpressRoute**: per impostazione predefinita, lo strumento seleziona il piano ExpressRoute più vicino corrispondente alla larghezza di banda di rete necessaria per la replica differenziale. È possibile modificare il piano in base ai propri requisiti.

**Gateway VPN**: selezionare il gateway VPN, se presente nell'ambiente in uso. L'impostazione predefinita è NA (N/D).

**Target Region** (Area di destinazione): area di Azure specificata per il ripristino di emergenza. Il prezzo usato nel report per il calcolo, l'archiviazione, la rete e la licenza è basato sui prezzi di Azure per tale area. 

### <a name="vm-running-on-azure"></a>VM in esecuzione in Azure
Se sono presenti VM DNS o controller di dominio oppure VM di SQL Server con Gruppi di disponibilità AlwaysOn eseguite in Azure per il ripristino di emergenza, è possibile specificare il numero di VM e le dimensioni per considerare il relativo costo di calcolo nel costo totale del ripristino di emergenza. 

### <a name="apply-overall-discount-if-applicable"></a>Applicazione di un eventuale sconto complessivo
I partner o i clienti di Azure che hanno diritto a qualsiasi sconto sul prezzo complessivo di Azure possono usare l'apposito campo. Lo strumento applica lo sconto (in percentuale) su tutti i componenti.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Numero di macchine virtuali, tipo e costo di calcolo (all'anno)
La tabella mostra il numero di VM Windows e non Windows e il relativo costo di calcolo per le esercitazioni sul ripristino di emergenza.

### <a name="settings"></a>Impostazioni 
**Using managed disk** (Uso disco gestito): specifica se durante le esercitazioni sul ripristino di emergenza viene usato un disco gestito. L'impostazione predefinita è Yes (Sì). Se il parametro -UseManagedDisks è stato impostato su No, per il calcolo del costo viene usato il prezzo dei dischi non gestiti.

**Currency** (Valuta): valuta in cui viene generato il report. Cost duration (Durata costo): è possibile visualizzare tutti i costi per un mese o per l'intero anno. 

## <a name="detailed-cost-analysis-table"></a>Tabella di analisi dettagliata dei costi
![Analisi dettagliata dei costi](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) La tabella elenca la suddivisione dei costi per ogni VM compatibile. È possibile usare questa tabella anche per ottenere il costo stimato del ripristino di emergenza di Azure delle VM non profilate aggiungendo manualmente le VM. È utile nei casi in cui è necessario stimare i costi di Azure per una nuova distribuzione di ripristino di emergenza senza una profilatura dettagliata.
Per aggiungere manualmente le VM: 
1.  Fare clic sul pulsante "Insert row" (Inserisci riga) per inserire una nuova riga tra la riga iniziale e quella finale.

2.  Compilare le colonne seguenti in base alle dimensioni approssimative delle VM e al numero di VM corrispondenti alla configurazione: 

* Number of VMs (Numero di VM), IaaS size (Your selection) (Dimensioni IaaS selezionate)
* Storage type - Standard/Premium (Tipo di archiviazione - Standard/Premium)
* VM total storage size (GB) (Spazio di archiviazione totale VM - GB)
* Number of DR-Drills in a year (N. esercitazioni ripristino di emergenza in un anno) 
* Each DR-Drill duration (Days) (Durata di ogni esercitazione sul ripristino di emergenza - giorni) 
* OS Type (Tipo di sistema operativo)
* Ridondanza dei dati 
* Vantaggio Azure Hybrid Use

3.  È possibile applicare lo stesso valore a tutte le VM nella tabella facendo clic sul pulsante "Apply to all" (Applica a tutte) per il numero di esercitazioni sul ripristino di emergenza in un anno, la durata di ogni esercitazione sul ripristino di emergenza (in giorni), la ridondanza dei dati e il vantaggio Azure Hybrid Use.

4.  Fare clic su "Re-calculate cost" (Ricalcola costo) per aggiornare il costo.

**VM Name** (Nome VM): nome della VM.

**Number of VMs** (Numero di VM): numero di VM corrispondenti alla configurazione. È possibile aggiornare il numero delle VM esistenti se sono presenti VM con configurazione simile che non sono state profilate ma verranno protette.

**IaaS size (Recommendation)** (Dimensioni IaaS consigliate): dimensioni del ruolo VM consigliate dallo strumento per le VM compatibili. 

**IaaS size (Your selection)** (Dimensioni IaaS selezionate): per impostazione predefinita, è lo stesso valore delle dimensioni del ruolo VM consigliate. È possibile modificare il ruolo in base ai propri requisiti. Il costo di calcolo è basato sulle dimensioni selezionate.

**Storage type** (Tipo di archiviazione): tipo di archiviazione usato dalla VM. L'archiviazione può essere Standard o Premium.

**VM total storage size (GB)** (Spazio di archiviazione totale VM - GB): spazio di archiviazione totale della VM.

**Number of DR-Drills in a year** (N. esercitazioni ripristino di emergenza in un anno): numero di esercitazioni sul ripristino di emergenza eseguite in un anno. Il valore predefinito è 4 volte in un anno. È possibile modificarlo per VM specifiche oppure applicare il nuovo valore a tutte le VM immettendolo nella prima riga e facendo clic sul pulsante "Apply to all" (Applica a tutte). Il costo totale delle esercitazioni sul ripristino di emergenza viene calcolato in base al numero di esercitazioni in un anno e alla durata di ognuna.  

**Each DR-Drill duration (Days)** (Durata di ogni esercitazione sul ripristino di emergenza - giorni): durata di ogni esercitazione sul ripristino di emergenza. Per impostazione predefinita, la durata è 7 giorni ogni 90, come previsto dal [vantaggio Ripristino di emergenza di Software Assurance](https://azure.microsoft.com/en-in/pricing/details/site-recovery). È possibile modificare il periodo per VM specifiche oppure applicare un nuovo valore a tutte le VM immettendolo nella prima riga e facendo clic sul pulsante "Apply to all" (Applica a tutte). Il costo totale delle esercitazioni sul ripristino di emergenza viene calcolato in base al numero di esercitazioni in un anno e alla durata di ognuna.
  
**OS Type** (Tipo di sistema operativo): tipo di sistema operativo della VM. Può essere Windows o Linux. Se il tipo di sistema operativo è Windows, alla VM può essere applicato il vantaggio Azure Hybrid Use. 

**Data redundancy** (Ridondanza dei dati): può corrispondere a LRS (Archiviazione con ridondanza locale), GRS (Archiviazione con ridondanza geografica) o RA-GRS (Archiviazione con ridondanza geografica e accesso in lettura). L'impostazione predefinita è LRS (Archiviazione con ridondanza locale). È possibile modificare il tipo in base al proprio account di archiviazione per VM specifiche oppure applicare il nuovo tipo a tutte le VM modificandolo nella prima riga e facendo clic sul pulsante "Apply to all" (Applica a tutte).  Il costo di archiviazione della replica viene calcolato in base al prezzo della ridondanza dei dati selezionata. 

**Azure Hybrid Use Benefit** (Vantaggio Azure Hybrid Use): il vantaggio Azure Hybrid Use può essere eventualmente applicato alle VM Windows.  Il valore predefinito è Yes. È possibile modificare l'impostazione per VM specifiche oppure aggiornare tutte le VM facendo clic sul pulsante "Apply to all" (Applica a tutte).

**Total Azure consumption** (Utilizzo totale di Azure): include il costo di calcolo, di archiviazione e della licenza di Azure Site Recovery per il ripristino di emergenza. Viene visualizzato il costo mensile o annuale in base alla selezione effettuata.

**Steady state replication cost** (Costo replica stazionaria): include il costo di archiviazione per la replica.

**Total DR-Drill cost (average)** (Costo totale esercitazioni sul ripristino di emergenza - media): include il costo di calcolo e di archiviazione per l'esercitazione sul ripristino di emergenza.

**ASR license cost** (Costo licenza ASR): costo della licenza di Azure Site Recovery.

## <a name="supported-target-regions"></a>Aree di destinazione supportate
Azure Site Recovery Deployment Planner offre la stima dei costi per le aree di Azure riportate di seguito. Se la propria area non è inclusa nell'elenco, è possibile usare qualsiasi area elencata con i prezzi più simili a quelli della propria area.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Valute supportate
Azure Site Recovery Deployment Planner può generare il report dei costi con una qualsiasi delle valute seguenti.

|Valuta|Nome||Valuta|Nome||Valuta|Nome|
|---|---|---|---|---|---|---|---|
|ARS|Peso argentino ($)||AUD|Dollaro australiano ($)||BRL|Real brasiliano (R$)|
|CAD|Dollaro canadese ($)||CHF|Franco svizzero (CHF)||DKK|Corona danese (kr)|
|EUR|Euro (€)||GBP|Sterlina britannica (£)||HKD|Dollaro di Hong Kong (HK$)|
|IDR|Rupia indonesiana (Rp)||INR|Rupia indiana (₹)||JPY|Yen giapponese (¥)|
|KRW|Won coreano (₩)||MXN|Peso messicano (MXN$)||MYR|Ringgit malese (RM$)|
|NOK|Corona norvegese (kr)||NZD|Dollaro neozelandese ($)||RUB|Rublo russo (руб)|
|SAR|Riyal saudita (SR)||SEK|Corona svedese (kr)||TWD|Dollaro taiwanese (NT$)|
|TRY|Lira turca (TL)||USD| Dollaro USA ($)||ZAR|Rand sudafricano (R)|

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla protezione di [VM VMware in Azure con Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-vmware-to-azure).