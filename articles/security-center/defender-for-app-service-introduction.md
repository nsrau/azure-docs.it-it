---
title: Azure Defender per il servizio app - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per il servizio app.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c53f7e712668f32766feaf76d6a08582bda9af22
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449108"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Introduzione ad Azure Defender per il servizio app

Servizio app di Azure è una piattaforma completamente gestita per la creazione e l'hosting di API e app Web senza l'onere della gestione dell'infrastruttura. Fornisce funzionalità di gestione e monitoraggio e informazioni operative dettagliate per soddisfare i requisiti di prestazioni, sicurezza e conformità di livello aziendale. Per altre informazioni, vedere [Servizio app di Azure](https://azure.microsoft.com/services/app-service/).

**Azure Defender per il servizio app** sfrutta la scalabilità del cloud per identificare gli attacchi alle applicazioni in esecuzione sul servizio app. Gli utenti malintenzionati mettono alla prova le applicazioni Web per individuare e sfruttare i punti deboli. Prima di essere instradate verso ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso diversi gateway, in cui vengono ispezionate e registrate. Questi dati vengono poi usati per identificare exploit e utenti malintenzionati, oltre che per apprendere nuovi modelli che verranno usati successivamente.

Grazie alla visibilità di cui Azure dispone come provider di servizi cloud, il Centro sicurezza analizza i log interni del servizio app per identificare la metodologia di attacco usata in più obiettivi. La metodologia comprende, ad esempio, analisi su larga scala e attacchi distribuiti. Gli attacchi di questo tipo in genere provengono da un piccolo subset di indirizzi IP e mostrano modelli di ricerca per indicizzazione in endpoint simili su più host. Questi attacchi cercano una pagina o un plug-in vulnerabile e non possono essere identificati dal punto di vista di un singolo host.


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Anteprima|
|Prezzi:|[Azure Defender per il servizio app](azure-defender.md) è soggetto alle tariffe visualizzate nella [pagina dei prezzi](security-center-pricing.md)|
|Piani di servizio app supportati:|![Sì](./media/icons/yes-icon.png) Basic, Standard, Premium, Isolato o Linux<br>![No](./media/icons/no-icon.png) Gratuito, Condiviso o Consumo<br>[Altre informazioni sui piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Cosa è possibile proteggere con Azure Defender per il servizio app?

Con il piano di servizio app abilitato, il Centro sicurezza valuta le risorse coperte da tale piano e genera raccomandazioni sulla sicurezza in base ai risultati ottenuti. Il Centro sicurezza protegge l'istanza di macchina virtuale in cui è in esecuzione il servizio app e l'interfaccia di gestione. Monitora anche le richieste e le risposte inviate da e verso le applicazioni in esecuzione nel servizio app.

Se si sta eseguendo un piano di servizio app basato su Windows, il Centro sicurezza ha anche accesso alle sandbox e alle macchine virtuali sottostanti. Insieme ai dati di log indicati in precedenza, l'infrastruttura può fornire indicazioni su ciò che accade, da un nuovo attacco in circolazione alle compromissioni nei computer dei clienti. Pertanto, anche se il Centro sicurezza viene distribuito dopo che le vulnerabilità di un'app Web sono state già sfruttate, può essere in grado di rilevare gli attacchi in atto.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteggere le app Web e le API del servizio app di Azure
Per proteggere il piano di servizio app di Azure con Azure Defender per il servizio app:

- Verificare di avere un piano di servizio app supportato associato a computer dedicati. I piani supportati sono elencati sopra nella sezione [Disponibilità](#availability).

- Abilitare **Azure Defender** nella sottoscrizione (se si vuole è possibile abilitare solo il piano **Azure Defender per il servizio app**) come descritto in [Prezzi del Centro sicurezza di Azure](security-center-pricing.md).

Il Centro sicurezza è integrato in modo nativo con il servizio app, eliminando la necessità di eseguire la distribuzione e l'onboarding; l'integrazione è trasparente.

>[!NOTE]
> La pagina Prezzi e impostazioni elenca un numero di istanze per **Quantità delle risorse**. Questo numero rappresenta il numero totale di istanze di calcolo, in tutti i piani di servizio app in questa sottoscrizione, in esecuzione al momento dell'apertura della pagina del piano tariffario.
>
> Servizio app di Azure offre una varietà di piani. Il piano di servizio app definisce il set di risorse di calcolo per l'esecuzione di un'app Web. Sono equivalenti alle server farm nell'hosting Web convenzionale. È possibile configurare una o più app per eseguirle nelle stesse risorse di calcolo (o nello stesso piano di servizio app).
>
>Per verificare il conteggio, passare a "Piani di servizio app" nel portale di Azure, dove è possibile vedere il numero di istanze di calcolo usate da ogni piano. 



## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per il servizio app. 

Per i materiali correlati, vedere gli articoli seguenti: 

- Che un avviso venga generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).
- Per un elenco degli avvisi del servizio app di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureappserv).
- Per altre informazioni sui piani di servizio app, vedere [Piani del servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).
- > [!div class="nextstepaction"]
    > [Abilitare Azure Defender](security-center-pricing.md)