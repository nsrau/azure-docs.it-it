---
title: Esaminare l'utilizzo e i costi in Gestione dei costi di Azure | Microsoft Docs
description: Esaminare l'utilizzo e i costi per tenere traccia delle tendenze, rilevare le inefficienze e creare avvisi.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/19/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 4440ca81545978ab7438f1684ce71ded24c326e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="review-usage-and-costs"></a>Esaminare l'utilizzo e i costi

Gestione dei costi di Azure di Cloudyn visualizza l'utilizzo e i costi consentendo di tenere traccia delle tendenze, rilevare le inefficienze e creare avvisi. Tutti i dati di utilizzo e i dati sui costi sono visualizzati nei dashboard e nei report di Cloudyn. Gli esempi di questa esercitazione illustrano come esaminare l'utilizzo e i costi tramite i dashboard e i report. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Tenere traccia delle tendenze di utilizzo e dei costi
> * Rilevare le inefficienze dell'utilizzo
> * Creare avvisi per spese inusuali o eccessive



## <a name="open-the-cloudyn-portal"></a>Aprire il portale Cloudyn

È possibile esaminare tutte le informazioni sull'utilizzo e sui costi nel portale Cloudyn. Aprire il portale Cloudyn dal portale di Azure o passare a https://app.cloudyn.com e accedere.

## <a name="track-usage-and-cost-trends"></a>Tenere traccia delle tendenze di utilizzo e dei costi

È possibile tenere traccia del denaro effettivamente speso per l'utilizzo e i costi con i report nel tempo per identificare le tendenze. Per iniziare a osservare le tendenze, usare il report Actual Cost Over Time (Costi effettivi nel tempo). Nel menu dei report nella parte superiore del portale fare clic su **Costo** > **Analisi dei costi** > **Actual Cost Over Time** (Costo effettivo nel tempo). Quando viene aperto per la prima volta, al report non sono applicati gruppi o filtri.

Di seguito è illustrato un report di esempio:

![report di esempio](./media/tutorial-review-usage/actual-cost01.png)

Il report visualizza tutte le spese degli ultimi 30 giorni. Per visualizzare solo le spese per i servizi di Azure, applicare il gruppo Servizio e quindi applicare un filtro per tutti i servizi di Azure. La figura seguente illustra i servizi filtrati.

![servizi filtrati](./media/tutorial-review-usage/actual-cost02.png)

Nell'esempio precedente è stato speso meno denaro a partire dal 31-08-2017 rispetto al periodo precedente. La tendenza del costo continua per i diversi servizi per circa nove giorni. Successivamente, le spese aggiuntive continuano come nel periodo precedente. Un numero eccessivo di colonne, tuttavia, può oscurare una tendenza chiara. È possibile modificare la visualizzazione del report in un grafico a linee o ad area per visualizzare i dati visibili in altre visualizzazioni. La figura seguente illustra la tendenza più chiaramente.

![tendenza nel report](./media/tutorial-review-usage/actual-cost03.png)

Nell'esempio è chiaramente visibile che il costo di Archiviazione di Azure è calato a partire dal 31-08-2017 mentre la spesa per gli altri servizi di Azure si è mantenuta costante. Che cosa ha causato il calo della spesa? In questo esempio alcuni dipendenti erano in vacanza e non hanno usato il servizio Archiviazione.

## <a name="detect-usage-inefficiencies"></a>Rilevare le inefficienze dell'utilizzo

I report dell'utilità di ottimizzazione migliorano l'efficienza, ottimizzano l'utilizzo e identificano i modi in cui è possibile risparmiare il denaro speso nelle risorse cloud. I report sono particolarmente utili con consigli di ridimensionamento che hanno come obiettivo la riduzione del numero di macchine virtuali inattive o costose.

Un problema comune alle organizzazioni che spostano per la prima volta le risorse nel cloud è rappresentato dalla strategia di virtualizzazione. Le organizzazioni usano spesso un approccio simile a quello usato per la creazione di macchine virtuali per l'ambiente di virtualizzazione locale. Presuppongono che per ridurre i costi sia sufficiente spostare le macchine virtuali locali nel cloud, senza apportare altre modifiche. Tuttavia, è probabile che questo approccio non riduca i costi.

Il problema è che l'infrastruttura esistente è già stata pagata. Gli utenti potevano creare e mantenere in esecuzione macchine virtuali di grandi dimensioni, inattive o meno, senza alcuna conseguenza. Lo spostamento di macchine virtuali di grandi dimensioni o inattive nel cloud può *aumentare* i costi. L'allocazione dei costi per le risorse è molto importante quando si sottoscrivono contratti con i provider di servizi cloud. È necessario pagare per quanto sottoscritto, indipendentemente dal fatto che la risorsa venga usata interamente o meno.

Il report Cost Effective Sizing Recommendations (Consigli di ridimensionamento per la riduzione dei costi) identifica i risparmi annuali potenziali confrontando la capacità dei tipi di istanza delle macchine virtuali con i dati cronologici di utilizzo della CPU e della memoria.  

Nel menu dei report nella parte superiore del portale fare clic su **Optimizer** (Utilità di ottimizzazione)  > **Pricing Optimization** (Ottimizzazione prezzi)  > **Cost Effective Sizing Recommendations** (Consigli di ridimensionamento per la riduzione dei costi). Applicare un filtro del provider impostandolo su Azure per visualizzare solo le macchine virtuali di Azure. Di seguito è riportata una figura di esempio.

![Macchine virtuali di Azure](./media/tutorial-review-usage/sizing01.png)

In questo esempio è stato possibile risparmiare $ 3.114 seguendo i consigli per modificare i tipi di istanza di macchina virtuale. Fare clic sul segno più (+) in **Details** (Dettagli) per il primo consiglio. Di seguito sono illustrati i dettagli del primo consiglio.

![dettagli del consiglio](./media/tutorial-review-usage/sizing02.png)

Fare clic sul segno più accanto a **List of Candidates** (Elenco dei candidati) per visualizzare gli ID delle istanze della macchina virtuale.

![Elenco dei candidati](./media/tutorial-review-usage/sizing03.png)

## <a name="create-alerts-for-unusual-spending"></a>Creare avvisi per spese inusuali

È possibile inviare automaticamente avvisi agli stakeholder relativi ad anomalie nelle spese e rischi di spese eccessive. È possibile creare avvisi in modo semplice e rapido usando report che supportano gli avvisi basati su soglie di budget e costo.

È possibile creare un avviso per qualsiasi spesa utilizzando un report Cost (Costo). In questo esempio usare il report Actual Cost Over Time (Costo effettivo nel tempo) per ricevere un avviso quando la spesa della macchina virtuale di Azure si avvicina al budget totale. Nel menu dei report nella parte superiore del portale fare clic su **Costo** > **Analisi dei costi** > **Actual Cost Over Time** (Costo effettivo nel tempo). Impostare **Groups** (Gruppi) su **Service** (Servizio) e impostare **Filter on the service** (Filtro sul servizio) su **Azure/VM** (Azure/macchina virtuale). Nella parte superiore destra del report fare clic su **Actions** (Azioni) e quindi selezionare **Schedule report** (Pianifica report).

Usare la scheda **Scheduling** (Pianificazione) per inviare a se stessi un messaggio di posta elettronica con il report con la frequenza desiderata. Nel report inviato tramite posta elettronica sono inclusi tutti i tag, i raggruppamenti e i filtri usati. Fare clic sulla scheda **Threshold** (Soglia) e selezionare **Actual Cost vs. Threshold** (Costo effettivo/soglia). Se si ha a disposizione un budget totale di $ 500.000 e si vuole ricevere una notifica quando il costo si avvicina alla metà del budget, creare un **Red alert** (Avviso rosso) a $ 250.000 e un **Yellow alert** (Avviso giallo) a $ 240.000. Scegliere quindi il numero di avvisi consecutivi. Dopo aver ricevuto il numero totale di avvisi specificato, non vengono inviati altri avvisi. Salvare il report pianificato.

![report di esempio](./media/tutorial-review-usage/schedule-alert01.png)

È anche possibile scegliere Cost Percentage vs. Budget (Percentuale costo/budget) per la creazione degli avvisi. Con questa metrica è possibile usare le percentuali del budget anziché i valori di valuta.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Tenere traccia delle tendenze di utilizzo e dei costi
> * Rilevare le inefficienze dell'utilizzo
> * Creare avvisi per spese inusuali o eccessive


Passare all'esercitazione successiva per apprendere come controllare l'accesso ai dati.

> [!div class="nextstepaction"]
> [Controllo dell'accesso ai dati](tutorial-user-access.md)
