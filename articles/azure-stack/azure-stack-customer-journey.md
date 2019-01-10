---
title: Procedura dettagliata l'integrazione di Data Center di Azure Stack | Microsoft Docs
description: Informazioni su cosa occorre aspettarsi per una corretta distribuzione locale di Azure Stack nel tuo Data Center.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: asganesh
ms.openlocfilehash: 554fd9c32c9831d45a40c62a871e3a8d5f8d7cb9
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191752"
---
# <a name="azure-stack-datacenter-integration"></a>Integrazione di Data Center di Azure Stack

Questo articolo descrive l'esperienza dei clienti di Azure Stack end-to-end dall'acquisto di una soluzione integrata tramite per una corretta distribuzione sul posto da un provider di soluzioni. Usare queste informazioni per facilitare il tuo viaggio e per consentire di impostare le aspettative su ciò che è, come cliente di Azure Stack, deve prevedere quando l'integrazione di Azure Stack nel tuo Data Center.

I clienti di Azure Stack è bene aspettarsi le fasi di integrazione di Data Center seguenti:

|     |Fase di pianificazione|Processo di ordine|Pre-distribuzione|Processo di factory|Distribuzione di hardware|Distribuzione in loco|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Coinvolgi i partner per offrire il supporto per la prevendita.|Preparare gestione licenze software e i contratti in base alle esigenze.|Fornire gli strumenti necessari per raccogliere i requisiti di integrazione di Data Center e la documentazione per cliente.|Fornire le compilazioni della linea di base e degli strumenti più recenti aggiornamenti catena cadenza mensile.|N/D|Supporto per tecnici del supporto Microsoft eventuali problemi di distribuzione.|
|**Partner**|Consigliare opzioni di soluzione in base ai requisiti del cliente.<br><br>Se necessario, è possibile proporre modello di verifica (POC).<br><br>Stabilire una relazione di business.<br><br>Decidere il livello di supporto.|Preparare i contratti necessari al cliente.<br><br>Creare l'ordine di acquisto dei clienti.<br><br>Decidere su sequenza temporale di recapito.<br><br>Contatto cliente con Microsoft se necessario.|Fornire i clienti con corsi di formazione necessari verificare le opzioni di integrazione conoscenza di tutti i prerequisiti per la distribuzione e Data Center.<br><br>Aiutare il cliente con la convalida dei dati raccolti per garantire la completezza e accuratezza.|Applicare l'ultima compilazione baseline convalidato.<br><br>Applicare il Microsoft deployment toolkit necessari.|Spedire hardware al sito del cliente.|Distribuzione gestita da un tecnico in sede.<br><br>Rack e dello stack.<br><br>Distribuzione dell'host (HLH) del ciclo di vita dell'hardware.<br><br>Distribuzione di Azure Stack.<br><br>Consegnare al cliente.|
|**Cliente**|Vengono descritti i casi di utilizzo previsto e specificare i requisiti.|Determinare il modello di fatturazione da usare, esaminare e approvare i contratti.|Completare il foglio di lavoro di distribuzione e assicurarsi che tutti i prerequisiti di distribuzione sono soddisfatto e pronto per la distribuzione.|N/D|Preparare i Data Center, garantendo tutti obbligatori power e raffreddamento, la connettività del bordo e altri requisiti di integrazione di Data Center necessari siano presenti.|Essere disponibile durante la distribuzione per fornire le credenziali della sottoscrizione e supporta se sono presenti domande sui dati forniti.|
| | | | | | | |


## <a name="planning-phase"></a>Fase di pianificazione
La fase di pianificazione è quando Microsoft o il partner di soluzioni di Azure Stack, collaborerà con te per valutare e comprendere le esigenze per determinare se Azure Stack è la soluzione ideale per te:

Essi consentono di stabilire quanto segue:

-   È la soluzione ideale per l'organizzazione di Azure Stack?

-   Quale soluzione dimensioni sono necessari?

-   Il tipo di modello di fatturazione e gestione delle licenza funzionerà per l'organizzazione?

-   Quali sono le alimentazione e raffreddamento requisiti?

Per garantire che la soluzione hardware rientrerà meglio le tue esigenze, il [Capacity Planner di Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) può essere utilizzato per assistere nell'acquisto anticipato di pianificazione per determinare la capacità appropriata e la configurazione per l'hardware di Azure Stack soluzione.

Il foglio di calcolo *non* dovrà essere utilizzato come soluzione alternativa per le indagini e analisi delle soluzioni hardware che meglio soddisfare le proprie esigenze. Durante la pianificazione per la distribuzione di Azure Stack, è consigliabile leggere anche il [considerazioni relative all'integrazione di datacenter generale](azure-stack-datacenter-integration.md) per Azure Stack i sistemi integrati.

## <a name="order-process-phase"></a>Fase del processo di ordine
In questa fase, molte delle domande per quanto riguarda la fattibilità sarebbe sono stati completati. Ora che è pronti per eseguire il commit all'acquisto di Azure Stack e dopo l'accesso a tutti i contratti necessari e gli ordini di acquisto, verrà richiesto di fornire i dati di requisiti di integrazione per il provider di soluzioni.

## <a name="pre-deployment-phase"></a>Fase di pre-distribuzione
Durante questa fase, è necessario decidere come si desidera integrare il tuo Data Center di Azure Stack. Per semplificare il processo, Microsoft, in collaborazione con i provider di soluzioni, raccolto un modello di requisiti che consentono di raccogliere le informazioni necessarie per pianificare la distribuzione di un sistema integrato all'interno dell'ambiente.

Il [considerazioni relative all'integrazione di datacenter generale](azure-stack-datacenter-integration.md) articolo fornisce informazioni che consentono di completare il modello, noto come il foglio di lavoro di distribuzione. 

> [!IMPORTANT]
> Durante questa fase è importante che tutte le informazioni necessarie vengano esaminate e ha Optate per prima dell'ordinamento della soluzione. Tenere presente che questo passaggio può richiedere molto tempo e richiede il coordinamento e la raccolta dati dai più discipline all'interno dell'organizzazione. 

In fase di pre-distribuzione è necessario stabilire quanto segue:

- **Azure Stack identità e modello di provider di connessione**. È possibile scegliere di distribuire Azure Stack, ovvero [connesso a internet (e in Azure) o disconnesso](azure-stack-connection-models.md). Per ottenere il massimo vantaggio da Azure Stack, inclusi scenari ibridi, si potrebbe voler distribuire connesso ad Azure. La scelta di Active Directory Federation Services (ADFS) o Azure Active Directory (Azure AD) è una decisione una-tantum che è necessario prendere in fase di distribuzione. **È possibile sostituire questa in un secondo momento senza ridistribuire l'intero sistema**.

- **Modello di licenza**. Le opzioni di modello licenza che consente di scegliere da dipendono dal tipo di distribuzione che sarà necessario. La scelta di provider di identità non è rilevante in macchine virtuali tenant o il sistema di identità e gli account che usano.
    - I clienti appartenenti a un [disconnesso distribuzione](azure-stack-disconnected-deployment.md) hanno una sola opzione: basata sulla capacità di fatturazione.

    - I clienti appartenenti a un [connessi distribuzione](azure-stack-connected-deployment.md) possono scegliere tra fatturazione basata sulla capacità e come è a pagamento. Fatturazione basata sulla capacità richiederanno una sottoscrizione di Azure Enterprise Agreement (EA) per la registrazione. È necessario per la registrazione, che fornisce la disponibilità di elementi del marketplace tramite una sottoscrizione di Azure.

- **Integrazione della rete**. [Integrazione della rete](azure-stack-network.md) è fondamentale per la distribuzione, l'operazione e gestione dei sistemi Azure Stack. Esistono diverse considerazioni che concorrono a garantire la soluzione di Azure Stack è resiliente e dispone di un'infrastruttura fisica a disponibilità elevata per supportare le relative operazioni.

- **Integrazione del firewall**. È consigliabile che si [Usa un firewall](azure-stack-firewall.md) per sicuro Azure Stack. I firewall possono aiutare a evitare attacchi DDOS, rilevamento delle intrusioni e l'ispezione del contenuto. Tuttavia, tenere presente che può costituire un collo di bottiglia della velocità effettiva per i servizi di archiviazione di Azure.


- **Requisiti dei certificati**. È fondamentale che tutti i [i certificati necessari](azure-stack-pki-certs.md) sono disponibili *precedenti* a un tecnico in sede in arrivo presso il Data Center per la distribuzione.

Dopo aver raccolto tutte le informazioni necessarie tramite il foglio di lavoro di distribuzione, il provider di soluzioni verrà avviato il processo di factory basato sui dati raccolti per garantire una corretta integrazione di Azure Stack nel tuo Data Center.

## <a name="hardware-delivery-phase"></a>Fase di distribuzione dell'hardware
Il provider di soluzioni collaborerà con te alla programmazione quando la soluzione arriverà alla struttura. Una volta ricevuto e mettere in atto, è necessario pianificare l'ora con il provider di soluzioni per avere a disposizione un ingegnere provengono in loco per eseguire la distribuzione di Azure Stack.

Si tratta **fondamentale** che tutti i prerequisiti vengono bloccati i dati disponibili *prima di in loco engineer pervengono, per distribuire la soluzione*.

-   Tutti i certificati devono essere acquistati e pronto.

-   Nome di dominio deve essere operato in.

-   Tutti i parametri di integrazione di rete vengono finalizzati e corrispondono a ciò che è stata condivisa con il provider di soluzioni.

> [!TIP]
> Se uno qualsiasi di queste informazioni è stata modificata, assicurarsi di comunicare la modifica con il provider della soluzione prima di pianificare la distribuzione effettiva.

## <a name="onsite-deployment-phase"></a>Fase di distribuzione in loco
Per distribuire Azure Stack, un tecnico in sede dal provider di soluzioni hardware dovrà essere presente per avviare la distribuzione. Per garantire una corretta distribuzione, assicurarsi che tutte le informazioni fornite tramite il foglio di lavoro di distribuzione non sono stato modificato. 

Ecco che cosa deve aspettarsi dal tecnico in sede durante l'esperienza di distribuzione:

- Controllare tutti i collegamenti e bordo connettività per assicurare la soluzione correttamente raccolto e soddisfa i requisiti
- Configurare la soluzione HLH (Host del ciclo di vita dell'Hardware)
- Verificare che tutti i BMC, il BIOS e rete impostazioni siano corrette.
- Assicurarsi che il firmware di tutti i componenti sia la versione più recente approvate dalla soluzione
- Avviare la distribuzione

> [!NOTE]
> Per il tecnico in sede, una stored procedure di distribuzione potrebbe richiedere circa una settimana di business.

## <a name="post-integration-phase"></a>Post-integrazione di fase
Prima che la soluzione viene passata al cliente nella fase di post-integrazione, è necessario effettuare alcuni passaggi dal partner. In questa fase, la convalida è importante per garantire che la distribuzione del sistema e funzionino correttamente. 

Le azioni che devono essere eseguite dal Partner OEM sono:

-   [Eseguire test-azurestack](azure-stack-diagnostic-test.md#run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix)

-   [Registrazione con Azure](azure-stack-registration.md)

-   [Diffusione di Marketplace](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

-   File di configurazione opzione di backup

-   Rimuovere DVM

-   Preparare un riepilogo dei clienti per la distribuzione

-   [Controllare gli aggiornamenti per assicurarsi che il software di soluzione viene aggiornato alla versione più recente](azure-stack-updates.md)

Sono disponibili diversi passaggi che sono obbligatori o facoltativi in base al tipo di installazione.

-   Se è stata completata la distribuzione usando [ADFS](azure-stack-integrate-identity.md), quindi lo Stack di Azure timbro dovrà essere integrata con clienti del proprio AD FS.

  > [!NOTE]
  > Questo passaggio è responsabilità del cliente, anche se il partner può scegliere di offrire servizi a tale scopo.

-   Integrazione con un sistema di monitoraggio esistente dai rispettivi partner.

    -   [Integrazione di System Center Operations Manager](azure-stack-integrate-monitor.md) supporta anche funzionalità di gestione della flotta.

    -   [Integrazione di Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>Sequenza temporale complessiva

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Supporto
Azure Stack consente un coerenti con Azure, esperienza di supporto integrato che descrive il ciclo di vita dell'intero sistema. Per supportare completamente i sistemi integrati di Azure Stack, i clienti devono due contratti di supporto; uno con Microsoft (o i Cloud Solution Provider) per il supporto di servizi di Azure e uno con il provider hardware per il supporto del sistema. L'esperienza di supporto integrata fornisce coordinato escalation e risoluzione, in modo che i clienti ottengono un'esperienza di supporto coerente indipendentemente da chi chiamano prima di tutto. I clienti che hanno già piano Premier, Azure - Standard / è incluso il supporto tecnico ProDirect o Partner con Microsoft, supporto del software Azure Stack.

L'esperienza di supporto integrata si avvale di un cambio di Case meccanismo per il trasferimento bidirezionale di casi di supporto e aggiornamenti case tra Microsoft e i partner hardware. Microsoft Azure Stack seguirà la [criterio del ciclo di vita moderni](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle [considerazioni relative all'integrazione di datacenter generale](azure-stack-datacenter-integration.md).
