---
title: Segnalare i dati di utilizzo di Azure Stack in Azure | Microsoft Docs
description: Informazioni su come configurare i dati di utilizzo, creazione di report in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: e4e1b3cb823d08948c31aa6486c08a930a377dfa
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161957"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Segnalare i dati di utilizzo di Azure Stack in Azure 

I dati di utilizzo, detto anche dati relativi al consumo, rappresentano la quantità di risorse usate. 

Azure Stack multinodo i sistemi che utilizzano il modello di fatturazione in base al consumo devono segnalare i dati di utilizzo in Azure per scopi di fatturazione. Operatori di Azure Stack è necessario configurare l'istanza di Azure Stack per segnalare i dati di utilizzo in Azure.

> [!NOTE]
> Report sull'utilizzo dei dati è obbligatorio per gli utenti a più nodi di Azure Stack che acquistano una licenza in base al modello di come è a pagamento. È facoltativo per i clienti che acquistano una licenza in base al modello di capacità (vedere la [modalità di acquisto pagina](https://azure.microsoft.com/overview/azure-stack/how-to-buy/). Per gli utenti di Azure Stack Development Kit, operatori di Azure Stack possono segnalare i dati di utilizzo e la funzionalità di test. Tuttavia, gli utenti non verranno addebitati per qualsiasi utilizzo che comportano. 

![flusso di fatturazione](media/azure-stack-usage-reporting/billing-flow.png)

I dati di utilizzo viene inviati da Azure Stack in Azure attraverso il Bridge di Azure. In Azure, il sistema di e-commerce elabora i dati di utilizzo e genera la fattura. Dopo la generazione di fattura, il proprietario della sottoscrizione di Azure può visualizzare e scaricala dal [centro Account Azure](https://account.windowsazure.com/Subscriptions). Per altre informazioni sul modo in cui viene concesso in licenza di Azure Stack, vedere la [Azure Stack assemblaggio e prezzi documento](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Configurare Segnalazione errori dati di utilizzo

Per configurare Segnalazione errori dati di utilizzo, è necessario [registrare l'istanza di Azure Stack con Azure](azure-stack-register.md). Come parte del processo di registrazione, il componente Bridge di Azure di Azure Stack, che connette Azure Stack in Azure e invia i dati di utilizzo, sia configurato. I seguenti dati di utilizzo viene inviati da Azure Stack in Azure:

- **ID contatore** : ID univoco per la risorsa che è stata utilizzata.
- **Quantità** : quantità di utilizzo delle risorse.
- **Percorso** : percorso in cui è distribuita la risorsa di Azure Stack corrente.
- **URI della risorsa** : URI della risorsa per cui viene segnalato l'utilizzo in modo completo.
- **ID sottoscrizione** : ID sottoscrizione dell'utente Azure Stack, ovvero la sottoscrizione (Azure Stack) locale.
- **Tempo** : ora di inizio e fine dei dati di utilizzo. C'è un ritardo tra il momento, quando vengono utilizzate queste risorse in Azure Stack e se i dati di utilizzo viene segnalati per e-commerce. Creazione di report i dati di utilizzo per la pipeline di e-commerce in Azure e Azure i dati di utilizzo di funzioni di aggregazione dello Stack per ogni 24 ore necessaria qualche ora un altro. Quindi, utilizzo che si verifica poco prima di mezzanotte potrebbero essere visualizzate in Azure il giorno successivo.

## <a name="generate-usage-data-reporting"></a>Generare report sui dati sull'utilizzo

- Per testare i dati di utilizzo reporting, creare alcune risorse in Azure Stack. Ad esempio, è possibile creare un [account di archiviazione](azure-stack-provision-storage-account.md), [macchina virtuale Windows Server](azure-stack-provision-vm.md) e una VM Linux con SKU Basic e Standard per visualizzare la modalità di segnalazione dell'utilizzo di core. I dati di utilizzo per i diversi tipi di risorse vengono segnalati nella metriche diverse.

- Lasciare le risorse in esecuzione per alcune ore. Informazioni sull'utilizzo vengono raccolti circa una volta ogni ora. Dopo aver raccolto, questi dati vengono trasmessi in Azure ed elaborati nel sistema di e-commerce di Azure. Questo processo può richiedere alcune ore.

## <a name="view-usage---csp-subscriptions"></a>Visualizza utilizzo - sottoscrizioni CSP

Se è stato registrato usando una sottoscrizione CSP di Azure Stack, è possibile visualizzare l'utilizzo e costi allo stesso modo in cui è visualizzare l'utilizzo di Azure. Utilizzo di Azure Stack verrà incluso nella fattura e nel file di riconciliazione, disponibile tramite il [Partner Center](https://partnercenter.microsoft.com/partner/home). File di riconciliazione viene aggiornato ogni mese. Se si desidera accedere alle informazioni recenti sull'utilizzo di Azure Stack, è possibile usare Partner Center API.

![Centro per i partner](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage--enterprise-agreement-subscriptions"></a>Visualizzazione di utilizzo – sottoscrizioni Enterprise Agreement

Se è la registrazione di Azure Stack tramite una sottoscrizione Enterprise Agreement, è possibile visualizzare l'utilizzo e dei costi il [portale EA](https://ea.azure.com/). Utilizzo di Azure Stack è incluso nel download avanzate e il loro utilizzo di Azure nella sezione report nel portale. 

## <a name="view-usage--other-subscriptions"></a>Visualizzazione di utilizzo – altre sottoscrizioni

Se è la registrazione di Azure Stack usando qualsiasi altra sottoscrizione digitare; ad esempio, una sottoscrizione con pagamento a consumo, è possibile visualizzare informazioni sull'utilizzo e costi nel centro account Azure. Accedi per il [centro Account Azure](https://account.windowsazure.com/Subscriptions) come Azure dell'account amministratore e selezionare la sottoscrizione di Azure usato per registrare lo Stack di Azure. È possibile visualizzare i dati di utilizzo di Azure Stack, l'importo addebitato per ognuna delle risorse usate, come illustrato nell'immagine seguente:

![flusso di fatturazione](media/azure-stack-usage-reporting/pricing-details.png)

Per Azure Stack Development Kit, le risorse di Azure Stack non vengono addebitate, in modo che il prezzo viene visualizzato come 0,00 dollari.

## <a name="which-azure-stack-deployments-are-charged"></a>Quali distribuzioni di Azure Stack vengono addebitate?

Utilizzo delle risorse è gratuito per Azure Stack Development Kit. Azure sistemi multi-nodo di Stack, il carico di lavoro macchine virtuali, servizi di archiviazione e servizi App, vengono addebitati.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Gli utenti vengono addebitati per l'infrastruttura di macchine virtuali?

 No. I dati di utilizzo per alcuni provider di risorse di Azure Stack le macchine virtuali viene segnalati ad Azure, ma non sono previsti addebiti per le macchine virtuali, né per le macchine virtuali create durante la distribuzione abilitare l'infrastruttura di Azure Stack.  

Gli utenti vengono addebitati solo per le macchine virtuali che vengono eseguiti in sottoscrizioni tenant. Tutti i carichi di lavoro devono essere distribuiti in sottoscrizioni di tenant per la conformità con le condizioni di licenza di Azure Stack.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Ha una licenza di Windows Server che desidera usare in Azure Stack, che cosa occorre fare?

Usando le licenze esistenti evita la generazione di contatori di utilizzo. Le licenze esistenti di Windows Server sono utilizzabili in Azure Stack, come descritto nella sezione "Using software esistente con Azure Stack" del [Guida alle licenze di Azure Stack](https://go.microsoft.com/fwlink/?LinkId=851536). I clienti devono implementare le proprie macchine virtuali Windows Server come descritto nel [vantaggio Hybrid per licenza di Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) articolo per poter usare le proprie licenze esistenti.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>La sottoscrizione viene addebitata per le risorse utilizzate?

La sottoscrizione che viene fornita in occasione [la registrazione di Azure Stack con Azure](azure-stack-register.md) viene addebitato.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Quali tipi di sottoscrizioni sono supportate per la segnalazione dei dati di utilizzo?

Per multinodo Azure Stack, sono supportate le sottoscrizioni Enterprise Agreement (EA) e CSP. Per Azure Stack Development Kit, le sottoscrizioni Enterprise Agreement (EA), con pagamento a consumo, CSP e MSDN supportano segnalazione errori dati di utilizzo.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Esegue il reporting lavoro nel cloud con sovranità dei dati di utilizzo?

In Azure Stack Development Kit, segnalazione errori dati di utilizzo richiede le sottoscrizioni che vengono create nel sistema Azure globale. Le sottoscrizioni create in uno dei cloud sovrani (cloud di Azure per enti pubblici, Azure Germania e Cina di Azure) non possono essere registrate con Azure, in modo che non supportano report sull'utilizzo dei dati.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Il motivo per cui l'utilizzo segnalato in Azure Stack non corrisponde il report generato dal centro Account di Azure?

È sempre un ritardo tra i dati di utilizzo segnalati per l'utilizzo di Azure Stack le API e i dati di utilizzo segnalati dal centro Account di Azure. Questo ritardo è il tempo necessario per caricare i dati di utilizzo di Azure Stack per e-commerce di Azure. A causa di questo ritardo, utilizzo che si verifica poco prima di mezzanotte potrebbero essere visualizzate in Azure il giorno successivo. Se si usa la [utilizzo di Azure Stack API](azure-stack-provider-resource-api.md)e confrontare i risultati per l'utilizzo segnalato nel portale di fatturazione di Azure, è possibile visualizzare una differenza.

## <a name="next-steps"></a>Passaggi successivi

* [API di utilizzo del provider](azure-stack-provider-resource-api.md)  
* [API di utilizzo del tenant](azure-stack-tenant-resource-usage-api.md)
* [Domande frequenti sull'utilizzo](azure-stack-usage-related-faq.md)
* [Gestire l'utilizzo e fatturazione come Provider di servizi Cloud](azure-stack-add-manage-billing-as-a-csp.md)
