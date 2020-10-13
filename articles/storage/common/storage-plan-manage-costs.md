---
title: Pianificare e gestire i costi per l'archiviazione BLOB di Azure
description: Informazioni su come pianificare e gestire i costi per l'archiviazione BLOB di Azure tramite l'analisi dei costi in portale di Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 75b464c140bfda6c3f3559d3bfdbe1e6bc2e7f24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760737"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Pianificare e gestire i costi per l'archiviazione BLOB di Azure

Questo articolo consente di pianificare e gestire i costi per l'archiviazione BLOB di Azure. Per prima cosa, è necessario stimare i costi usando il calcolatore dei prezzi di Azure. Dopo aver creato l'account di archiviazione, ottimizzare l'account in modo da pagare solo le informazioni necessarie. Usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È anche possibile esaminare i costi previsti e monitorare le tendenze di spesa per identificare le aree in cui si vuole agire.

Tenere presente che i costi per l'archiviazione BLOB sono solo una parte dei costi mensili nella fattura di Azure. Sebbene questo articolo spieghi come stimare e gestire i costi per l'archiviazione BLOB, verranno addebitati tutti i servizi e le risorse di Azure usati per la sottoscrizione di Azure, inclusi i servizi di terze parti. Dopo aver acquisito familiarità con la gestione dei costi per l'archiviazione BLOB, è possibile applicare metodi simili per gestire i costi per tutti i servizi di Azure usati nella sottoscrizione.

## <a name="estimate-costs"></a>Stimare i costi

Usa il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) per stimare i costi prima di creare e iniziare a trasferire i dati in un account di archiviazione di Azure.

1. Nella pagina [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) scegliere il riquadro **account di archiviazione** .

2. Scorrere la pagina verso il basso e individuare la sezione **account di archiviazione** della stima.

3. Scegliere Opzioni dagli elenchi a discesa. 

   Quando si modifica il valore di questi elenchi a discesa, la stima dei costi cambia. Tale stima viene visualizzata nell'angolo superiore e nella parte inferiore della stima. 
    
   ![Monitorare i costi con il riquadro analisi costi](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Quando si modifica il valore dell'elenco a discesa tipo, vengono modificate anche altre opzioni visualizzate in questo foglio di **testo** . Usare i collegamenti nella sezione **altre informazioni** per ottenere altre informazioni sulle opzioni di ogni opzione e sul modo in cui queste opzioni influiscono sul prezzo delle operazioni correlate all'archiviazione. 

4. Modificare le opzioni rimanenti per visualizzarne gli effetti sulla stima.

## <a name="optimize-costs"></a>Ottimizzare i costi

Prendere in considerazione l'uso di queste opzioni per ridurre i costi. 

- Riserva capacità di archiviazione

- Organizzare i dati in livelli di accesso

- Spostare automaticamente i dati tra i livelli di accesso

Questa sezione illustra ogni opzione in modo più dettagliato. 

#### <a name="reserve-storage-capacity"></a>Riserva capacità di archiviazione

È possibile risparmiare sui costi di archiviazione per i dati BLOB con la capacità riservata di archiviazione di Azure. La capacità riservata di archiviazione di Azure offre uno sconto sulla capacità per i BLOB in blocchi e per Azure Data Lake Storage Gen2 dati negli account di archiviazione standard quando si esegue il commit di una prenotazione per uno o tre anni. Una prenotazione fornisce una quantità fissa di capacità di archiviazione per il periodo di prenotazione. La capacità riservata di archiviazione di Azure può ridurre significativamente i costi di capacità per i BLOB in blocchi e i dati Azure Data Lake Storage Gen2. 

Per altre informazioni, vedere [ottimizzare i costi per l'archiviazione BLOB con capacità riservata](https://docs.microsoft.com/azure/storage/blobs/storage-blob-reserved-capacity).

#### <a name="organize-data-into-access-tiers"></a>Organizzare i dati in livelli di accesso

È possibile ridurre i costi inserendo i dati BLOB nei livelli di accesso più convenienti. È possibile scegliere tra tre livelli progettati per ottimizzare i costi legati all'uso dei dati. Ad esempio *, il livello di accesso* frequente ha un costo di archiviazione superiore ma un costo di accesso inferiore. Se pertanto si prevede di accedere spesso ai dati, il livello di accesso frequente potrebbe essere la scelta più economica. Se si prevede di accedere ai dati *con una frequenza* minore, è possibile che il livello di accesso sporadico o *Archivio* risulti più appropriato, perché aumenta il costo dell'accesso ai dati, riducendo al tempo stesso i costi di archiviazione dei dati.    

Per altre informazioni, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal).

#### <a name="automatically-move-data-between-access-tiers"></a>Spostare automaticamente i dati tra i livelli di accesso

Usare i criteri di gestione del ciclo di vita per spostare periodicamente i dati tra i livelli per risparmiare il maggior numero di denaro. Questi criteri possono spostare i dati in utilizzando le regole specificate. Ad esempio, è possibile creare una regola che sposta i BLOB nel livello archivio se il BLOB non è stato modificato in 90 giorni. Creando criteri che regolano il livello di accesso dei dati, è possibile progettare le opzioni di archiviazione meno costose per le proprie esigenze.

Per altre informazioni, vedere [gestire il ciclo di vita dell'archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

## <a name="create-budgets"></a>Creare i budget

È possibile creare [budget](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) per gestire i costi e creare avvisi per informare automaticamente le persone interessate in caso di anomalie di spesa e rischi di costi eccessivi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, quindi sono utili come parte di una strategia di monitoraggio dei costi complessiva. Tuttavia, potrebbero avere funzionalità limitate per gestire i costi dei singoli servizi di Azure, come il costo di archiviazione di Azure, perché sono progettati per tenere traccia dei costi a un livello superiore.

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano le risorse di Azure con archiviazione di Azure, si incorrono i costi. I costi delle unità di utilizzo delle risorse variano in base a intervalli di tempo (secondi, minuti, ore e giorni) o all'utilizzo di unità (byte, megabyte e così via). I costi vengono sostenuti non appena viene avviata l'utilizzo di archiviazione di Azure. È possibile visualizzare i costi nel riquadro [analisi costi](../../cost-management-billing/costs/quick-acm-cost-analysis.md) del portale di Azure.

Quando si usa l'analisi dei costi, è possibile visualizzare i costi di archiviazione di Azure in grafici e tabelle per intervalli di tempo diversi. Alcuni esempi sono il giorno, il mese corrente e quello precedente e l'anno. È anche possibile visualizzare i costi rispetto ai budget e ai costi previsti. Passando a visualizzazioni più lunghe nel tempo è possibile identificare le tendenze di spesa e vedere dove potrebbero essersi verificate le eccedenze. Se sono stati creati budget, è anche possibile vedere dove sono stati superati.

>[!NOTE]
> L'analisi dei costi supporta diversi tipi di account di Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](../../cost-management-billing/costs/understand-cost-mgt-data.md). Per visualizzare i dati relativi ai costi, è necessario disporre almeno dell''accesso in lettura per l''account Azure. Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../../cost-management-billing/costs/assign-access-acm-data.md).

Per visualizzare i costi di archiviazione di Azure nell'analisi dei costi:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Aprire la finestra **Gestione costi e fatturazione** , selezionare **Gestione costi** dal menu e quindi selezionare **analisi dei costi**. È quindi possibile modificare l'ambito per una sottoscrizione specifica dall'elenco a discesa **ambito** .

   ![Monitorare i costi con il riquadro analisi costi](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Per visualizzare solo i costi per archiviazione di Azure, selezionare **Aggiungi filtro** , quindi selezionare **nome servizio**. Quindi, scegliere **archiviazione** dall'elenco. 

   Ecco un esempio che mostra i costi solo per archiviazione di Azure:

   ![Monitorare i costi di archiviazione con il riquadro analisi costi](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

Nell'esempio precedente viene visualizzato il costo corrente per il servizio. Vengono visualizzati anche i costi in base alle aree di Azure (località) e al gruppo di risorse. È anche possibile aggiungere altri filtri (ad esempio, un filtro per visualizzare i costi per gli account di archiviazione specifici).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla gestione dei costi con l' [analisi dei costi](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Per altre informazioni su come funziona il prezzo con archiviazione di Azure, vedere gli articoli seguenti:

- [Panoramica dei prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Ottimizzare i costi per l'archiviazione BLOB con capacità riservata](../blobs/storage-blob-reserved-capacity.md)
