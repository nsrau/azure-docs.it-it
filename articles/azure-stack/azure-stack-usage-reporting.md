---
title: Report di dati di utilizzo dello Stack di Azure in Azure | Documenti Microsoft
description: Informazioni su come impostare i dati di utilizzo reporting nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2A397C83-1A6C-4290-B0EF-FDC34EE290CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: mabrigg;AlfredoPizzirani
ms.openlocfilehash: 451e8c604e48d9cfc42284f2413fa5ed4fea03bc
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Report di dati di utilizzo dello Stack di Azure in Azure 

Dati di utilizzo, denominati anche i dati relativi al consumo, rappresentano la quantità di risorse utilizzate. 

Sistemi a più nodi di Azure Stack che utilizzano un modello di fatturazione in base al consumo devono segnalare i dati di utilizzo in Azure per scopi di fatturazione.  Gli operatori di Azure Stack devono configurare l'istanza di Azure Stack ai dati di utilizzo di report in Azure.

> [!NOTE]
> Report di dati di utilizzo è necessario per gli utenti a più nodi di Azure Stack che acquistano una licenza in base al modello di pagamento come-di-uso. È facoltativo per i clienti che acquistano una licenza in base al modello di capacità (vedere il [come acquistare pagina](https://azure.microsoft.com/overview/azure-stack/how-to-buy/ to learn more about pricing in Azure Stack)). Per gli utenti di Azure Stack Development Kit, gli operatori di Azure Stack possono segnalare i dati di utilizzo e testare la funzionalità. Tuttavia, gli utenti non verrà addebitati alcun utilizzo che comportano. 


![flusso di fatturazione](media/azure-stack-usage-reporting/billing-flow.png)

Dati di utilizzo viene inviati dallo Stack di Azure ad Azure attraverso il Bridge di Azure. In Azure, il sistema di commerce elabora i dati di utilizzo e genera la fattura. Dopo la generazione di fattura, il proprietario della sottoscrizione di Azure è possibile visualizzare e scaricarlo dal [centro Account Azure](https://account.windowsazure.com/Subscriptions). Per informazioni sulle modalità con cui è concesso in licenza dello Stack di Azure, vedere il [Azure Stack assemblaggio e prezzi documento](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Configurare Segnalazione errori dati di utilizzo

Per configurare Segnalazione errori dati di utilizzo, è necessario [registrare l'istanza dello Stack di Azure con Azure](azure-stack-register.md). Come parte del processo di registrazione, il componente Bridge di Azure di Stack di Azure, che si connette dello Stack di Azure in Azure e invia i dati di utilizzo, è configurato. I seguenti dati di utilizzo viene inviati dallo Stack di Azure in Azure:

- **Controllare l'ID** : ID univoco per la risorsa che è stata utilizzata.
- **Quantità** : quantità di utilizzo delle risorse.
- **Percorso** : percorso in cui è distribuita la risorsa di Azure Stack corrente.
- **URI di risorsa** – completo URI della risorsa per cui viene segnalato l'utilizzo.
- **ID sottoscrizione** : ID sottoscrizione dell'utente dello Stack di Azure. Si tratta della sottoscrizione (di Azure Stack) locale.
- **Tempo** : ora di inizio e fine dei dati di utilizzo. È un ritardo tra il momento quando vengono utilizzate queste risorse nello Stack di Azure e quando i dati di utilizzo viene segnalati per commerce. Report dati di utilizzo di pipeline commerce in Azure e Azure i dati di utilizzo di funzioni di aggregazione dello Stack per ogni 24 ore accetta un'altra alcune ore. In tal caso, l'utilizzo che si verifica poco prima di mezzanotte potrebbero essere visualizzate in Azure il giorno successivo.

## <a name="generate-usage-data-reporting"></a>Generare report di dati di utilizzo

1. Per testare i dati di utilizzo reporting, creare una serie di risorse nello Stack di Azure. Ad esempio, è possibile creare un [account di archiviazione](azure-stack-provision-storage-account.md), [macchina virtuale Windows Server](azure-stack-provision-vm.md) e una VM Linux con Basic e SKU Standard per visualizzare la modalità di segnalazione dell'utilizzo di componenti di base. I dati di utilizzo per diversi tipi di risorse vengono segnalati in metri diversi.

2. Lasciare le risorse in esecuzione per alcune ore. Informazioni sull'utilizzo vengono raccolti circa una volta ogni ora. Dopo aver raccolto, i dati vengono trasmessi in Azure ed elaborati nel sistema commerciale di Azure. Questo processo può richiedere alcune ore.

## <a name="view-usage---csp-subscriptions"></a>Visualizza utilizzo - sottoscrizioni CSP

Se è stato registrato usando una sottoscrizione di CSP lo Stack di Azure, è possibile visualizzare le informazioni sull'utilizzo e gli addebiti nello stesso modo in cui si visualizza il consumo di Azure. Utilizzo dello Stack Azure verranno inclusa nella fattura e nel file di riconciliazione, disponibile tramite [Partner Center](https://partnercenter.microsoft.com/partner/home). Il file di riconciliazione viene aggiornato ogni mese. Se si desidera accedere alle informazioni sull'utilizzo di Azure Stack recenti, è possibile utilizzare le API di Partner Center.

   ![centro di partner](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Visualizza utilizzo – sottoscrizioni Enterprise Agreement

Se è registrato lo Stack di Azure usando un abbonamento contratto Enterprise, è possibile visualizzare le informazioni sull'utilizzo e gli addebiti nel [EA portale](https://ea.azure.com/). Utilizzo dello Stack Azure verrà inclusi nel download avanzate con l'utilizzo di Azure nella sezione report nel portale di EA. 

## <a name="view-usage--other-subscriptions"></a>Visualizza utilizzo: altre sottoscrizioni

Se è stata registrata utilizzando un altro tipo di sottoscrizione, ad esempio, una sottoscrizione di pagamento a consumo, lo Stack di Azure è possibile visualizzare informazioni sull'utilizzo e gli addebiti nel centro account Azure. Accedi al [centro Account Azure](https://account.windowsazure.com/Subscriptions) come Azure account amministratore e selezionare la sottoscrizione di Azure che è utilizzata per registrare lo Stack di Azure. È possibile visualizzare i dati di utilizzo dello Stack di Azure, l'importo addebitato per ognuna delle risorse utilizzate, come illustrato nella figura seguente:

   ![flusso di fatturazione](media/azure-stack-usage-reporting/pricing-details.png)

Per il Kit di sviluppo dello Stack di Azure, le risorse di Azure Stack non vengono addebitate in modo, il prezzo viene visualizzato come $0,00. Quando a più nodi di Azure Stack diventano disponibili in genere, è possibile visualizzare il costo effettivo per ognuna di queste risorse.

## <a name="which-azure-stack-deployments-are-charged"></a>Le distribuzioni cui Stack di Azure vengono addebitate?

Utilizzo delle risorse è gratuito per Azure Stack Development Kit. Mentre per i sistemi a più nodi di Azure Stack, viene addebitati il carico di lavoro macchine virtuali, servizi di archiviazione e servizi di App.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Gli utenti vengono addebitati per l'infrastruttura di macchine virtuali?

No. Dati di utilizzo per alcuni provider di risorse macchine virtuali di Azure Stack viene segnalati in Azure, ma non sono previsti costi per queste macchine virtuali, né per le macchine virtuali create durante la distribuzione abilitare l'infrastruttura di Azure Stack.  

Gli utenti vengono addebitati solo per le macchine virtuali che vengono eseguiti in sottoscrizioni tenant. Tutti i carichi di lavoro deve essere distribuiti in sottoscrizioni tenant ai fini della conformità con le condizioni di licenza dello Stack di Azure.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Dispone di una licenza di Windows Server che si desidera utilizzare nello Stack di Azure, come devo fare?

Utilizzo delle licenze esistenti evita la generazione di controlli di utilizzo. Licenze di Windows Server esistente utilizzabile nello Stack di Azure, come descritto nella sezione "Utilizzo di software esistente con lo Stack di Azure" di [Guida di gestione delle licenze di Azure Stack](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). I clienti devono distribuire le macchine virtuali di Windows Server, come descritto nel [vantaggio ibrido di licenza di Windows Server](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) argomento per usare le licenze esistenti.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>La sottoscrizione è previsto un pagamento per le risorse utilizzate?
La sottoscrizione che viene fornita quando [registrazione dello Stack di Azure con Azure](azure-stack-register.md) viene addebitato.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>I tipi di sottoscrizioni sono supportati per i report di dati di utilizzo?

Per multinode dello Stack di Azure, sono supportate le sottoscrizioni Enterprise Agreement (EA) e CSP. Per il Kit di sviluppo dello Stack di Azure, le sottoscrizioni Enterprise Agreement (EA), pagamento a consumo, CSP e MSDN supportano report dati di utilizzo.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Non i dati di utilizzo della generazione di report in sovrani cloud?

Nel Kit di sviluppo dello Stack di Azure, report di utilizzo dei dati richiede le sottoscrizioni vengono create nel sistema globale di Azure. Le sottoscrizioni create in uno dei cloud sovrani (cloud di Azure per enti pubblici, Azure in Germania e Cina di Azure) non possono essere registrate con Azure, in modo che non supportano report dati di utilizzo.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Come gli utenti possono identificare i dati di utilizzo dello Stack di Azure nel portale di fatturazione di Azure?

Gli utenti possono visualizzare i dati di utilizzo dello Stack di Azure nel file di dettagli di utilizzo. Per informazioni su come ottenere il file di dettagli di utilizzo, consultare il [scaricare i file di utilizzo dall'articolo centro Account Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). Il file di dettagli di utilizzo contiene i controlli di Stack di Azure che identificano l'archiviazione di Azure Stack e le macchine virtuali. Tutte le risorse usate nello Stack di Azure vengono segnalate sotto l'area denominata "Azure Stack".

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Perché l'utilizzo registrata nello Stack di Azure non corrisponda il report generato da centro Account Azure?

È sempre un delaybetween i dati di utilizzo ha segnalati l'utilizzo di Azure Stack API e i dati di utilizzo riportati dal centro Account Azure... Questo ritardo rappresenta il tempo necessario per caricare dati di utilizzo dallo Stack di Azure in Azure commerce. A causa di questo ritardo, utilizzo che si verifica poco prima di mezzanotte potrebbero essere visualizzate in Azure il giorno successivo. Se si utilizza il [Azure Stack utilizzo API](azure-stack-provider-resource-api.md)e confrontare i risultati per l'utilizzo indicato nel portale di fatturazione di Azure, si noterà una differenza.

## <a name="next-steps"></a>Passaggi successivi

* [API di utilizzo del provider](azure-stack-provider-resource-api.md)  
* [API di utilizzo del tenant](azure-stack-tenant-resource-usage-api.md)
* [Domande frequenti sull'utilizzo](azure-stack-usage-related-faq.md)
