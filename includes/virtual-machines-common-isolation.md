---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123225"
---
Calcolo di Azure offre dimensioni delle macchine virtuali con piano Isolato per uno specifico tipo di hardware e dedicate a un singolo cliente.  Queste dimensioni delle macchine virtuali sono particolarmente adatte ai carichi di lavoro che richiedono un elevato livello di isolamento dagli altri clienti, per i carichi di lavoro con aspetti come i requisiti normativi e di conformità.  I clienti possono anche scegliere di suddividere ulteriormente le risorse di tali macchine virtuali con piano Isolato usando il [supporto di Azure per le macchine virtuali annidate](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

L'utilizzo di dimensioni con piano Isolato garantisce che la macchina virtuale sia l'unica in esecuzione nella specifica istanza del server.  Le offerte di macchine virtuali con piano Isolato correnti includono:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Ulteriori informazioni su ogni dimensione isolata disponibile [sono disponibili qui](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>Ritiro D15_v2/DS15_v2 isolamento il 15 maggio 2020
**Aggiornamento del 10 febbraio 2020: La sequenza temporale di pensionamento "isolamento" è stata estesa al 15 maggio 2020"**

Host dedicato di Azure è ora GA, che consente di eseguire le macchine virtuali Linux e Windows dell'organizzazione in server fisici a tenant singolo. Si prevede di sostituire completamente le macchine virtuali di Azure isolate con l'host dedicato di Azure.We plan to fully replace isolated Azure VMs with Azure Dedicated Host. Dopo **il 15 maggio 2020** le macchine virtuali di Azure D15_v2/DS15_v2 non saranno più isolate dall'hardware.

## <a name="how-does-this-affect-me"></a>Quali sono le conseguenze di questa modifica?
Dopo il 15 maggio 2020, non forniremo più una garanzia di isolamento per le macchine virtuali di Azure D15_v2/DS15_v2. 

## <a name="what-actions-should-i-take"></a>Quali azioni devo intraprendere?
Se l'isolamento hardware non è necessario, non è necessario eseguire alcuna azione. 

Se è necessario l'isolamento per l'utente, prima del 15 maggio 2020, è necessario:

Eseguire [la migrazione del](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) carico di lavoro all'host dedicato di Azure.- Migrate your workload to Azure Dedicated Host.

[Richiedere l'accesso](https://aka.ms/D15iRequestAccess) a una macchina virtuale di Azure e D15i_v2 DS15i_v2 per ottenere le stesse prestazioni di prezzo. Questa opzione è disponibile solo per gli scenari di istanze riservate con pagamento in base al tratto e un anno.    

Eseguire la migrazione del carico di lavoro a un'altra macchina virtuale isolata di [Azure.- Migrate](https://azure.microsoft.com/blog/resize-virtual-machines/) your workload to another Azure isolated virtual machine. 

Per i dettagli vedi di seguito:

## <a name="timeline"></a>Sequenza temporale
| Data | Azione | 
| --- | --- |
| 18 novembre 2019 | Disponibilità di D/DS15i_v2 (PAYG, 1 anno RI) |
| giovedì 14 maggio 2020  | Ultimo giorno per acquistare D/DS15i_v2 1 anno RI | 
| giovedì 15 maggio 2020   | Garanzia di isolamento D/DS15_v2 rimossa | 
| giovedì 15 maggio 2021  | Ritiro D/DS15i_v2 (tutti i clienti tranne chi ha acquistato 3 anni RI di D/DS15_v2 prima del 18 novembre 2019)| 
| del 17 novembre 2022  | Ritiro D/DS15i_v2 quando sono state fatte rietà triennale (per i clienti che hanno acquistato 3 anni RI di D/DS15_v2 prima del 18 novembre 2019) | 

## <a name="faq"></a>Domande frequenti
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>D: La taglia D/DS15_v2 andrà in pensione?
**R:** No, solo la funzionalità "isolamento" verrà ritirata. Se non è necessario l'isolamento, non è necessario intraprendere alcuna azione.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>D: La taglia D/DS15i_v2 andrà in pensione?
**R:** Sì, la dimensione è disponibile solo fino al 15 maggio 2021. Per i clienti che hanno acquistato Ri de 3 anni su D/DS15_v2 prima del 18 novembre 2019 avranno accesso a D/DS15i_v2 fino al 17 novembre 2022.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>D: Perché non vedo le nuove dimensioni D/DS15i_v2 nel portale?
**R**: Se sei un cliente D/DS15_v2 corrente e desideri utilizzare le nuove dimensioni D/DS15i_v2, compila questo [modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>D: Perché non vedo alcuna quota per le nuove dimensioni D/DS15i_v2?
**R**: Se sei un cliente D/DS15_v2 corrente e desideri utilizzare le nuove dimensioni D/DS15i_v2, compila questo [modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>D: Quando andranno in pensione le altre dimensioni isolate?
**R:** Forniremo promemoria con 12 mesi di anticipo rispetto allo smantellamento ufficiale delle taglie.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>D: C'è un tempo di inattività quando la mia macchina virtuale atterra su un hardware non isolato?
**R**: Se non è necessario l'isolamento, non è necessario intraprendere alcuna azione e non si vedrebbe alcun tempo di inattività.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>D: Sono previste modifiche dei costi per il passaggio a una macchina virtuale non isolata?
**A**: No 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>D: Ho già acquistato un'istanza riservata di 1 o 3 anni per D15_v2 o Ds15_v2. Come verrà applicato lo sconto all'utilizzo della macchina virtuale?
**R:** Le RI acquistate prima del 18 novembre 2019 estenderanno automaticamente la copertura alla nuova serie di VM isolate. 

| RI |  Flessibilità delle dimensioni dell'istanza | Idoneità al benefit |   
| --- | --- | --- |
|   D15_v2  |   Disattivato     |   D15_v2 e D15i_v2 |    
|   D15_v2  |   Attivato  |   D15_v2 serie e D15i_v2 riceveranno tutti il beneficio del RI. |    
|   D14_v2  |   Attivato  |   D15_v2 serie e D15i_v2 riceveranno tutti il beneficio del RI. |    
 
Allo stesso modo per la serie Dsv2.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>D: Desidero acquistare istanze riservate aggiuntive per Dv2. Qual è l'opzione migliore?
**R:** Tutte le RI acquistate dopo il 18 novembre 2019 hanno il seguente comportamento. 

| RI |  Flessibilità delle dimensioni dell'istanza | Idoneità al benefit |   
| --- | --- | --- |
| D15_v2 |  Disattivato |   D15_v2 solo  
| D15_v2 |  Attivato |    D15_v2 serie riceverà il beneficio RI. Le nuove D15i_v2 non avranno diritto al ri di beneficiare di questo tipo di RI. | 
| D15i_v2 |     Disattivato | D15i_v2 solo per i |  
| D15i_v2 |     Attivato  | D15i_v2 solo per i | 
 
La flessibilità delle dimensioni dell'istanza non può essere utilizzata per applicare ad altre dimensioni, ad esempio D2_v2, D4_v2 o D15_v2. Allo stesso modo, per la serie Dsv2.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>D: Posso acquistare un nuovo RI triennale per D15i_v2 e DS15i_v2?
**R:** Purtroppo no, solo 1 anno RI è disponibile per il nuovo acquisto.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>D: È possibile spostare l'istanza riserva tramite D15_v2/DS15_v2 esistente in un'istanza riservata di dimensioni isolate?
**R**: Questa azione non è necessaria poiché il vantaggio si applicherà sia alle dimensioni isolate che a quella non isolate. Tuttavia, Azure supporterà la modifica di istanze riservate di D15_v2/DS15_v2 esistenti in D15i_v2/DS15i_v2. Per tutte le altre istanze riservate Dv2/Dsv2, utilizzare l'istanza riservata esistente o acquistare nuove istanze riservate per le dimensioni isolate.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>D: Sono un cliente di Azure Service Fabric che si affida ai livelli di durabilità Silver o Gold.D: I'm an Azure Service Fabric Customer relying on the Silver or Gold Durability Tiers. Questo cambiamento mi ha un impatto?
**R:** No. Le garanzie fornite dai livelli di [durabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) di Service Fabric continueranno a funzionare anche dopo questa modifica. Se è necessario l'isolamento hardware fisico per altri motivi, potrebbe essere comunque necessario eseguire una delle azioni descritte in precedenza. 
