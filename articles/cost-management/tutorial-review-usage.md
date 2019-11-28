---
title: "Esercitazione: Esaminare l'utilizzo e i costi di Azure con Cloudyn | Microsoft Docs"
description: In questa esercitazione si esamineranno l'utilizzo e i costi per tenere traccia delle tendenze, rilevare le inefficienze e creare avvisi.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management-billing
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: 41abf9d0675e7ea620a15656f97fcaed4fd0ff66
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229794"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Esercitazione: Esaminare l'utilizzo e i costi

Cloudyn mostra l'utilizzo e i costi consentendo di tenere traccia delle tendenze, rilevare le inefficienze e creare avvisi. Tutti i dati di utilizzo e i dati sui costi sono visualizzati nei dashboard e nei report di Cloudyn. Gli esempi di questa esercitazione illustrano come esaminare l'utilizzo e i costi tramite i dashboard e i report.

Gestione costi di Azure offre funzionalità simili a quelle di Cloudyn. Gestione costi di Azure è una soluzione di gestione costi nativa di Azure. È un modo per analizzare i costi, creare e gestire budget, esportare i dati, esaminare e implementare gli elementi consigliati per l'ottimizzazione e di conseguenza risparmiare. Per altre informazioni, vedere [Gestione costi di Azure](overview-cost-mgt.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Tenere traccia delle tendenze di utilizzo e dei costi
> * Rilevare le inefficienze dell'utilizzo
> * Creare avvisi per spese inusuali o eccessive
> * Esportazione dei dati

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

- È necessario disporre di un account Azure.
- È necessario disporre di una registrazione di valutazione o una sottoscrizione a pagamento per Cloudyn.

## <a name="open-the-cloudyn-portal"></a>Aprire il portale Cloudyn

È possibile esaminare tutte le informazioni sull'utilizzo e sui costi nel portale Cloudyn. Aprire il portale di Cloudyn dal portale di Azure oppure passare a https://azure.cloudyn.com ed eseguire l'accesso.

## <a name="track-usage-and-cost-trends"></a>Tenere traccia delle tendenze di utilizzo e dei costi

È possibile tenere traccia del denaro effettivamente speso per l'utilizzo e i costi con i report nel tempo per identificare le tendenze. Per iniziare a osservare le tendenze, usare il report Actual Cost Over Time (Costi effettivi nel tempo). Nella parte superiore sinistra del portale, fare clic su **Costo** > **Analisi dei costi** > **Actual Cost Over Time** (Costo effettivo nel tempo). Quando viene aperto per la prima volta, al report non sono applicati gruppi o filtri.

Di seguito è illustrato un report di esempio:

![Esempio di report dei costi effettivi nel tempo](./media/tutorial-review-usage/actual-cost01.png)

Il report visualizza tutte le spese degli ultimi 30 giorni. Per visualizzare solo le spese per i servizi di Azure, applicare il gruppo Servizio e quindi applicare un filtro per tutti i servizi di Azure. L'immagine seguente mostra i servizi filtrati.

![Esempio che mostra i servizi di Azure filtrati](./media/tutorial-review-usage/actual-cost02.png)

Nell'esempio precedente a partire dal 29-10-2018 la spesa è stata inferiore rispetto al periodo precedente. Un numero eccessivo di colonne può però rendere meno evidente una tendenza ovvia. È possibile modificare la visualizzazione del report in un grafico a linee o ad area per visualizzare i dati visibili in altre visualizzazioni. La figura seguente illustra la tendenza più chiaramente.

![Esempio che mostra una tendenza dei costi delle macchine virtuali di Azure in diminuzione](./media/tutorial-review-usage/actual-cost03.png)

Continuando con l'esempio, è possibile vedere che il costo per macchina virtuale di Azure è diminuito. Anche i costi relativi ad altri servizi di Azure hanno iniziato a diminuire nello stesso giorno. Che cosa ha causato il calo della spesa? In questo esempio è stato completato un progetto di lavoro di grandi dimensioni, di conseguenza è diminuito il consumo di numerosi servizi di Azure.

Per guardare un video di esercitazione su come tenere traccia delle tendenze di utilizzo e dei costi, vedere [Analyzing your cloud billing data vs. time with Cloudyn](https://youtu.be/7LsVPHglM0g) (Analisi del rapporto fatturazione/tempo per il cloud con Cloudyn).

## <a name="detect-usage-inefficiencies"></a>Rilevare le inefficienze dell'utilizzo

I report dell'utilità di ottimizzazione migliorano l'efficienza, ottimizzano l'utilizzo e identificano i modi in cui è possibile risparmiare il denaro speso nelle risorse cloud. I report sono particolarmente utili con consigli di ridimensionamento che hanno come obiettivo la riduzione del numero di macchine virtuali inattive o costose.

Un problema comune alle organizzazioni che spostano per la prima volta le risorse nel cloud è rappresentato dalla strategia di virtualizzazione. Le organizzazioni usano spesso un approccio simile a quello usato per la creazione di macchine virtuali per l'ambiente di virtualizzazione locale. Presuppongono che per ridurre i costi sia sufficiente spostare le macchine virtuali locali nel cloud, senza apportare altre modifiche. Tuttavia, è probabile che questo approccio non riduca i costi.

Il problema è che l'infrastruttura esistente è già stata pagata. Gli utenti potevano creare e mantenere in esecuzione macchine virtuali di grandi dimensioni, inattive o meno, senza alcuna conseguenza. Lo spostamento di macchine virtuali di grandi dimensioni o inattive nel cloud può *aumentare* i costi. L'allocazione dei costi per le risorse è importante quando si sottoscrivono contratti con i provider di servizi cloud. È necessario pagare per quanto sottoscritto, indipendentemente dal fatto che la risorsa venga usata interamente o meno.

Il report Cost Effective Sizing Recommendations (Consigli di ridimensionamento per la riduzione dei costi) identifica i risparmi annuali potenziali confrontando la capacità dei tipi di istanza delle macchine virtuali con i dati cronologici di utilizzo della CPU e della memoria.  

Nel menu nella parte superiore del portale fare clic su **Optimizer** (Utilità di ottimizzazione)  > **Pricing Optimization** (Ottimizzazione dimensioni)  > **Cost Effective Sizing Recommendations** (Consigli di ridimensionamento per la riduzione dei costi). Se utile, applicare un filtro per ridurre il numero di risultati. Di seguito è riportata una figura di esempio.

![Report Cost Effective Sizing Recommendations (Consigli di ridimensionamento per la riduzione dei costi) per le macchine virtuali di Azure](./media/tutorial-review-usage/sizing01.png)

In questo esempio è stato possibile risparmiare $ 2.382 seguendo i consigli per modificare i tipi di istanza di macchina virtuale. Fare clic sul segno più (+) in **Details** (Dettagli) per il primo consiglio. Di seguito sono illustrati i dettagli del primo consiglio.

![Esempio che mostra i dettagli dei consigli](./media/tutorial-review-usage/sizing02.png)

Fare clic sul segno più accanto a **List of Candidates** (Elenco dei candidati) per visualizzare gli ID delle istanze della macchina virtuale.

![Esempio che mostra un elenco di candidati di macchine virtuali da ridimensionare](./media/tutorial-review-usage/sizing03.png)

Per guardare un video di esercitazione per il rilevamento delle inefficienze di utilizzo, vedere [Optimizing VM Size in Cloudyn](https://youtu.be/1xaZBNmV704) (Ottimizzazione delle dimensioni delle macchine virtuali in Cloudyn).

Gestione costi di Azure fornisce anche gli elementi consigliati di ottimizzazione dei costi per i servizi di Azure. Per altre informazioni, vedere [Esercitazione: Ottimizzare i costi grazie agli elementi consigliati](tutorial-acm-opt-recommendations.md).

## <a name="create-alerts-for-unusual-spending"></a>Creare avvisi per spese inusuali

Gli avvisi permettono anche di inviare una notifica automatica alle parti interessate per le anomalie di spesa e i rischi di spesa eccessiva. È possibile creare avvisi usando report che supportano avvisi basati su soglie di budget e costo.

Questo esempio usa il report **Actual Cost Over Time** (Costo effettivo nel tempo) per inviare una notifica quando la spesa di una macchina virtuale di Azure si avvicina al budget totale. In questo scenario si dispone di un budget totale di 20.000 $ e si vuole ricevere una notifica quando i costi si avvicinano alla prima metà del budget, 9.000 $, e un ulteriore avviso quando i costi raggiungono i 10.000 $.

1. Dal menu nella parte superiore del portale di Cloudyn selezionare **Costo** > **Analisi dei costi** > **Actual Cost Over Time** (Costo effettivo nel tempo).
2. Impostare **Groups** (Gruppi) su **Service** (Servizio) e impostare **Filter on the service** (Filtro sul servizio) su **Azure/VM** (Azure/macchina virtuale).
3. Nella parte superiore destra del report selezionare **Actions** (Azioni) e quindi selezionare **Schedule report** (Pianifica report).
4. Per inviare a se stessi un messaggio di posta elettronica del report a intervalli pianificati, selezionare la scheda **Scheduling** (Pianificazione) nella finestra di report **Save or Schedule this report** (Salva o pianifica report). Assicurarsi di selezionare **Send via email** (Invia tramite posta elettronica). Nel report inviato tramite posta elettronica sono inclusi tutti i tag, i raggruppamenti e i filtri usati.
5. Selezionare la scheda **Threshold** (Soglia) e quindi selezionare **Actual Cost vs. Threshold** (Costo effettivo/soglia).
   1. Nella casella di soglia **Red alert** (Avviso rosso) immettere 10000.
   2. Nella casella di soglia **Yellow alert** (Avviso giallo) immettere 9000.
   3. Nella casella **Number of consecutive alerts** (Numero di avvisi consecutivi) immettere il numero di avvisi consecutivi da ricevere. Dopo aver ricevuto il numero totale di avvisi specificato, non vengono inviati altri avvisi.
6. Selezionare **Salva**.

![Esempio che mostra gli avvisi di colore giallo e rosso in base alle soglie di spesa](./media/tutorial-review-usage/schedule-alert01.png)

È anche possibile scegliere la metrica di soglia **Cost Percentage vs. Budget** (Percentuale costo/budget) per la creazione degli avvisi. In questo modo è possibile specificare le soglie come percentuali del budget anziché come valori di valuta.

## <a name="export-data"></a>Esportazione dei dati

Analogamente al modo in cui si creano gli avvisi per i report, è possibile esportare dati da tutti i report. Ad esempio, è possibile esportare un elenco di account Cloudyn o altri dati utente. Per esportare i report, aprire il report e fare clic su **Azioni** nella parte superiore destra del report. Tra le azioni disponibili, è possibile **esportare tutti i dati del report** in modo da poter scaricare o stampare le informazioni. In alternativa, è possibile selezionare **Pianifica report** per pianificare l'invio del report come messaggio di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Tenere traccia delle tendenze di utilizzo e dei costi
> * Rilevare le inefficienze dell'utilizzo
> * Creare avvisi per spese inusuali o eccessive
> * Esportazione dei dati


Passare alla prossima esercitazione per imparare come prevedere la spesa usando i dati cronologici.

> [!div class="nextstepaction"]
> [Prevedere la spesa futura](tutorial-forecast-spending.md)
