---
title: 'Azure Defender per il servizio app: vantaggi e funzionalità'
description: Scopri i vantaggi e le funzionalità di Azure Defender per il servizio app.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d15e881ca1246d463d8054fe5bd094efaff0cdd5
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977172"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Introduzione ad Azure Defender per il servizio app

App Azure servizio è una piattaforma completamente gestita per la creazione e l'hosting di API e app Web senza doversi preoccupare della gestione dell'infrastruttura. Fornisce funzionalità di gestione, monitoraggio e informazioni operative per soddisfare i requisiti di prestazioni, sicurezza e conformità di livello aziendale. Per ulteriori informazioni, vedere [app Azure Service](https://azure.microsoft.com/services/app-service/).

**Azure Defender per il servizio app** usa la scalabilità del cloud per identificare gli attacchi destinati alle applicazioni in esecuzione sul servizio app. Gli utenti malintenzionati mettono alla prova le applicazioni Web per individuare e sfruttare i punti deboli. Prima di essere instradate verso ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso diversi gateway, in cui vengono ispezionate e registrate. Questi dati vengono poi usati per identificare exploit e utenti malintenzionati, oltre che per apprendere nuovi modelli che verranno usati successivamente.

Grazie alla visibilità di cui Azure dispone come provider di servizi cloud, il Centro sicurezza analizza i log interni del servizio app per identificare la metodologia di attacco usata in più obiettivi. La metodologia comprende, ad esempio, analisi su larga scala e attacchi distribuiti. Gli attacchi di questo tipo in genere provengono da un piccolo subset di indirizzi IP e mostrano modelli di ricerca per indicizzazione in endpoint simili su più host. Questi attacchi cercano una pagina o un plug-in vulnerabile e non possono essere identificati dal punto di vista di un singolo host.


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Anteprima|
|Prezzi|[Azure Defender per il servizio app](azure-defender.md) viene fatturato come indicato nella [pagina dei prezzi](security-center-pricing.md)|
|Piani di servizio app supportati:|![Sì ](./media/icons/yes-icon.png) Basic, standard, Premium, isolated o Linux<br>![Non disponibile ](./media/icons/no-icon.png) , condiviso o a consumo<br>[Altre informazioni sui piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Nazionale/sovrano (US Gov, Cina gov, altri gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Che cosa protegge Azure Defender per il servizio app?

Con il piano di servizio app abilitato, il Centro sicurezza valuta le risorse coperte dal piano di servizio app e genera raccomandazioni sulla sicurezza in base ai risultati ottenuti. Il Centro sicurezza protegge l'istanza di macchina virtuale in cui è in esecuzione il servizio app e l'interfaccia di gestione. Monitora anche le richieste e le risposte inviate da e verso le applicazioni in esecuzione nel servizio app.

Se si sta eseguendo un piano di servizio app basato su Windows, il Centro sicurezza ha anche accesso alle sandbox e alle macchine virtuali sottostanti. Insieme ai dati di log indicati in precedenza, l'infrastruttura può fornire indicazioni su ciò che accade, da un nuovo attacco in circolazione alle compromissioni nei computer dei clienti. Pertanto, anche se il Centro sicurezza viene distribuito dopo che le vulnerabilità di un'app Web sono state già sfruttate, può essere in grado di rilevare gli attacchi in atto.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Proteggere le app Web e le API del servizio app di Azure
Per proteggere il piano di servizio app Azure con Azure Defender per il servizio app:

- Assicurarsi di disporre di un piano di servizio app supportato associato a computer dedicati. I piani supportati sono elencati sopra nella pagina relativa alla [disponibilità](#availability).

- Abilitare **Azure Defender** per la sottoscrizione. è possibile abilitare facoltativamente solo **Azure Defender per** il piano di servizio app, come descritto in [prezzi del Centro sicurezza di Azure](security-center-pricing.md)

Il Centro sicurezza è integrato in modo nativo con il servizio app, eliminando la necessità di distribuzione e onboarding, l'integrazione è trasparente.

>[!NOTE]
> La pagina prezzi e impostazioni elenca un numero di istanze per la **quantità di risorse**. Rappresenta il numero totale di istanze di calcolo, in tutti i piani di servizio app in questa sottoscrizione, in esecuzione al momento dell'apertura della pagina del piano tariffario.
>
> App Azure servizio offre un'ampia gamma di piani. Il piano di servizio app definisce il set di risorse di calcolo per l'esecuzione di un'app Web. Sono equivalenti alle server farm nell'hosting Web convenzionale. È possibile configurare una o più app per eseguirle nelle stesse risorse di calcolo (o nello stesso piano di servizio app).
>
>Per convalidare il conteggio, passare a "piani di servizio app" nel portale di Azure, in cui è possibile visualizzare il numero di istanze di calcolo usate da ogni piano. 



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come Azure Defender per il servizio app. 

Per i materiali correlati, vedere gli articoli seguenti: 

- Che un avviso venga generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md).
- Per un elenco degli avvisi del servizio app di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureappserv).
- Per altre informazioni sui piani di servizio app, vedere [Piani del servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).
- > [!div class="nextstepaction"]
    > [Abilitare Azure Defender](security-center-pricing.md)