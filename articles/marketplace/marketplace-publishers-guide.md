---
title: Guida alla pubblicazione per Azure Marketplace e AppSource
description: Guida dettagliata ed elenchi di controllo per la pubblicazione destinati ai nuovi editori in Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: msmbaldwin
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/18/2018
ms.author: ellacroi
ms.openlocfilehash: 7faa34b5528c53c06aeda25be17cbc5f3db2acdf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-marketplace-and-appsource-publisher-guide"></a>Guida alla pubblicazione per Azure Marketplace e AppSource

La Guida alla pubblicazione per [Azure Marketplace](https://azuremarketplace.microsoft.com) e [AppSource](https://appsource.microsoft.com). progettata per aiutare i nuovi editori e quelli già operativi a sfruttare al meglio le vetrine di Azure Marketplace e AppSource, a eseguire i passaggi necessari per pubblicare le applicazioni e i servizi offerti e a rafforzare così la crescita della propria azienda in partnership con Microsoft. Al termine di questa guida, si avranno maggiori nozioni e si saprà dove trovare informazioni più dettagliate sugli argomenti seguenti:

- Quali sono i vantaggi della presenza in una delle vetrine del marketplace
- Che cosa sono Azure Marketplace e AppSource
- Come sfruttare al meglio queste vetrine
- Quale vetrina è più adatta a determinati servizi e offerte 
- Quali tipi di offerte di applicazioni e servizi è possibile pubblicare
- Quali sono i requisiti tecnici e aziendali per ogni opzione di pubblicazione
- Elenco di controllo degli asset preliminare alla pubblicazione
- Come diventare un editore
- Dove creare e pubblicare le offerte
- Come ottimizzare un'inserzione e usare le risorse di Go-to-Market per una maggiore visibilità
- Come ottenere supporto. Per eventuali domande su Azure Marketplace, AppSource o questa guida alla pubblicazione, contattare il team di Marketplace all'indirizzo **cloudmarketplace@microsoft.com**. 

## <a name="benefits-of-participating-in-the-marketplace"></a>Vantaggi della partecipazione al marketplace

Azure Marketplace e AppSource sono i punti di avvio per attività di commercializzazione comuni con Microsoft e favoriscono la crescita aziendale. Grazie a soluzioni per offerte promozionali di lancio, generazione della domanda e attività di vendita e marketing in collaborazione, gli elenchi del marketplace possono costituire il nucleo centrale del motore di espansione aziendale nel cloud. Non sono previste tariffe per la partecipazione al marketplace. L'obiettivo è quello di consentire ai clienti di conoscere i servizi e le soluzioni migliori offerti dall'ecosistema di partner Microsoft.

Nel marketplace è possibile cogliere notevoli opportunità per promuovere la crescita dell'azienda:

- **Generare opportunità di vendita e clienti potenziali**. È possibile accedere a nuovi mercati con un portfolio più ampio di soluzioni sulla piattaforma cloud Microsoft e usufruire delle offerte di upselling e cross-selling del marketplace. 
- **Migliorare il valore aziendale e ampliare il volume di affari con clienti nuovi ed esistenti**. È possibile aumentare il volume di affari, risolvere i punti deboli dei clienti dovuti allo spostamento dei carichi di lavoro nel cloud nonché ridurre i cicli di vendita e aumentare la redditività offerta dalla vendita di soluzioni complete destinate a carichi di lavoro e scenari di settore specifici.
- **Ottenere informazioni dettagliate di utilità pratica**. Per Microsoft il successo dei clienti è il proprio successo. Ottenere informazioni dettagliate sulle prestazioni degli elenchi tramite il portale Cloud Partner. È possibile acquisire informazioni sulle operazioni in esecuzione, sui clienti potenziali generati e su come ottimizzare le attività della campagna.

>[!NOTE]
>Le app che estendono Office saranno in grado di accedere a tali informazioni dettagliate tramite il processo di pubblicazione delle app di Office.

## <a name="what-are-azure-marketplace-and-appsource"></a>Presentazione di Azure Marketplace e AppSource

Microsoft offre due vetrine del marketplace distinte che consentono ai partner di pubblicare inserzioni sulle proprie offerte, attivare versioni di valutazione ed effettuare transazioni direttamente con i clienti e l'ecosistema Microsoft: [Azure Marketplace] (https://azuremarketplace.microsoft.com) e [AppSource] (https://appsource.microsoft.com). Usando queste vetrine, i clienti possono trovare, provare e acquistare applicazioni e servizi per accelerare la propria trasformazione digitale, mentre gli editori possono espandere la propria azienda grazie alla possibilità di accedere ai clienti e all'ecosistema di partner Microsoft.
 
Le vetrine del marketplace sono organizzate in base a gruppi di destinatari e prodotti cloud Microsoft per consentire ai clienti di trovare la soluzione di cui hanno bisogno. Ogni vetrina offre specifiche opzioni di pubblicazione che consentono agli editori di sfruttare al massimo l'investimento nella pubblicazione, come illustrato in sintesi nella tabella seguente:


|          |Azure Marketplace |AppSource  |
|---------|---------|---------|
|Destinatari     |Professionisti IT, Sviluppatori (ad esempio, esperti in Amministrazione database, Operazioni sicurezza, Operazioni sviluppo e così via)    | Decision maker line-of-business (ad esempio, esperti in Approvvigionamento, Produzione, Contabilità e così via)      |
|Creata con o per l'estensione     |Azure         | Azure, Dynamics 365, Office 365, Power BI e Power Apps       |
|Tipi di soluzioni e servizi     |  Soluzioni per infrastrutture e servizi professionali   | Applicazioni cloud line-of-business complete, componenti aggiuntivi Office 365 e servizi professionali        |
|Opzioni di pubblicazione     |  Desidero essere contattato, Servizi di consulenza, Valutazione, Macchina virtuale, Modelli di soluzioni e App gestite       |  Versione di valutazione gratuita, Ottieni subito, Test drive, Desidero essere contattato, Servizi di consulenza      |
|L'esperienza in-app consente agli utenti di accedere ad app e servizi nel contesto dell'applicazione  | Interfaccia della riga di comando e portale di Azure         | Office 365, Dynamics 365, Power BI, app client di Office       |

## <a name="leveraging-these-storefronts"></a>Come sfruttare al meglio queste vetrine

Ogni vetrina offre soluzioni per soddisfare le esigenze esclusive dei clienti e consente di identificare i destinatari in base al ruolo in modo da offrire la soluzione o il servizio più adatto a ogni singolo cliente.

**Azure Marketplace** offre a professionisti IT e sviluppatori cloud l'opportunità di individuare, provare e acquistare soluzioni SaaS, IaaS e PaaS:


|Esigenza del cliente  |Azure Marketplace |
|---------|---------|
|**Richiede funzionalità aggiuntive per la piattaforma cloud per soddisfare esigenze aziendali e tecniche**     |  Offre un portfolio sempre più ampio di applicazioni e servizi complementari ottimizzati per l'esecuzione in Azure       |
|**Non riesce a individuare facilmente l'applicazione o il servizio adatto alle sue esigenze**    |  Offre una risorsa online dedicata per individuare, provare e acquistare soluzioni e servizi per Azure        |
|**Richiede un meccanismo di distribuzione scalabile per servizi e applicazioni di terze parti**   | Consente la creazione e la configurazione di distribuzioni scalabili per servizi e applicazioni di terze parti        |
|**Richiede nuovi servizi e applicazioni da integrare con soluzioni esistenti**  |   Integra facilmente servizi e applicazioni di terze parti con soluzioni esistenti in Azure      |

Coinvolgere gli utenti aziendali in **AppSource** per trovare, provare e acquisire applicazioni SaaS line-of-business e servizi di implementazione per favorire la crescita di risultati aziendali e ridurre il time-to-value: 


|Esigenza del cliente  |AppSource  |
|---------|---------|
|**Ricerca di soluzioni aziendali che funzionano con i prodotti Microsoft già in uso** | Consente ai clienti di usare servizi e applicazioni di terze parti per estendere le tecnologie e le applicazioni cloud di Microsoft.       |
|**Possibilità di trovare facilmente la soluzione o il servizio di implementazione giusto.**    |   Offre una risorsa online dedicata per individuare, valutare e acquisire applicazioni, servizi, componenti aggiuntivi e altro ancora      |
|**Soluzione line-of-business specifica del settore per risolvere sfide aziendali particolari**   | Offre soluzioni di settore end-to-end per soddisfare requisiti specifici in numerosi settori     |
|**App per migliorare produttività, efficienza e informazioni dettagliate aziendali**    | Offre app per attività line-of-business, ad esempio servizio clienti, risorse umane, operazioni e molte altre        |
| **Partner con esperienza di implementazione per adattare le app alla situazione esclusiva** | Offre un catalogo di servizi consulenza per soluzioni basate su Dynamics 365, Power BI, PowerApps e app di terze parti disponibili in AppSource per consentire agli utenti aziendali di trovare servizi di consulenza per restituire risultati prevedibili |

## <a name="understanding-the-differences-between-storefronts"></a>Differenze tra le vetrine

Quando si sceglie una vetrina è necessario prima di tutto identificare i destinatari della propria offerta: Azure Marketplace è allineato alle esigenze di professionisti IT e sviluppatori, mentre AppSource è pensato per quelle degli utenti aziendali. Se la soluzione offerta è stata progettata per entrambi i tipi di destinatari, è sufficiente pubblicarla una sola volta per esporla in entrambe le vetrine.
 
Ogni vetrina offre inoltre i vantaggi seguenti:

|Vantaggio della vetrina  |Azure Marketplace  |AppSource   |
|---------|---------|---------|
|**Flessibilità nella fatturazione**    | Per le opzioni di fatturazione con pagamento in base al consumo di Macchine virtuali vengono usati i contratti Enterprise Agreement Microsoft o i modelli di vendita diretta sul Web. Le opzioni di prezzo includono una sottoscrizione di livello gratuito, che prevede un'offerta perennemente gratuita, e una sottoscrizione per una versione di valutazione (disponibile subito) offerta gratuitamente a fini promozionali per un periodo di tempo limitato, al termine del quale la sottoscrizione diventa a pagamento. Per entrambe le opzioni di fatturazione è inoltre possibile abilitare l'opzione Bring Your Own License per agevolare gli editori negli scenari di distribuzione di Macchine virtuali tramite App di Azure, ad esempio Modelli di soluzioni o App gestite. In questo caso, i costi di tutte le risorse di Azure di cui viene effettuato il provisioning vengono addebitati direttamente al cliente. | AppSource offre il provisioning dell'esperienza di valutazione, ma per il momento non offre un'opzione di pubblicazione abilitata per la commercializzazione. Ciò consente di sfruttare l'attuale infrastruttura per gli ordini e la fatturazione senza che siano necessari altri investimenti o modifiche        |
|**Facilità di collegamento con altri partner**     |Azure Marketplace non consente attualmente agli editori di collegare all'offerta un provider di servizi o partner di distribuzione, ma renderà disponibile questa funzionalità nel 2018         |  È possibile collegare fornitori di software indipendenti, integratori di sistemi e provider di servizi gestiti per specifici scenari di implementazione e sfruttare così opportunità di vendita collaborativa a nuovi clienti.      |
|**Automazione**     |    Azure Marketplace non consente attualmente agli editori di collegare all'offerta un provider di servizi o partner di distribuzione.     | È possibile sfruttare il provisioning automatizzato di soluzioni SaaS con componenti aggiuntivi e usare Modelli di soluzioni per automatizzare gli scenari di distribuzione e raccolta di dati basati su SaaS.        |È possibile collegare fornitori di software indipendenti, integratori di sistemi e provider di servizi gestiti per specifici scenari di implementazione e sfruttare così opportunità di vendita collaborativa a nuovi clienti.
|**Più tipi di cloud**     |   È possibile pubblicare soluzioni locali e cloud pubbliche tramite Azure Stack oppure eseguire la pubblicazione in Azure per enti pubblici e cloud internazionali come Cina e Germania.      |    AppSource non offre attualmente il supporto per Azure Stack, Azure per enti pubblici o cloud internazionali.     |
|**Presentazione contestualizzata ai clienti**     |  È possibile rendere disponibile la propria soluzione all'interno del portale di Azure per ricerche contestuali (Macchine virtuali e Modelli di soluzioni).       |  Consente di raggiungere più clienti tramite l'esperienza in-app per prodotti Microsoft, ad esempio Dynamics 365, Power BI e Office 365    |

## <a name="select-a-publishing-option"></a>Scelta di un'opzione di pubblicazione

Ogni vetrina supporta più opzioni di pubblicazione (Inserzione, Valutazione e Transazione) e tipi di offerta correlati. È possibile scegliere il tipo di offerta che meglio rappresenta l'applicazione e il servizio pubblicato. Tutte le opzioni di pubblicazione offrono ai partner la possibilità di usufruire della condivisione di clienti potenziali. 

Questa sezione illustra le opzioni disponibili per l'utente in entrambe le vetrine. 


|**Opzione di pubblicazione**  | **Tipo di offerta** | **Vetrina**  |
|---------|---------|---------|
|**Elenco**    |    Desidero essere contattato, Servizi di consulenza     |  Azure Marketplace, AppSource       |
|**Versione di valutazione**   |     Versione di valutazione gratuita, Versione di valutazione SaaS, Demo interattiva, Test drive    |  Azure Marketplace, AppSource       |
|**Transazione**     |   Macchina virtuale, Modello di soluzione, Applicazione gestita      |    Azure Marketplace     |

### <a name="list"></a>INSERZIONE

Usare l'opzione **Desidero essere contattato** quando la partecipazione a livello di valutazione o transazione non è fattibile. Il vantaggio di questo approccio è dato dall'opportunità offerta agli editori di mettere subito in commercio una soluzione per iniziare a ricevere clienti potenziali a cui presentare offerte di base per dare impulso alla propria azienda. Lo svantaggio consiste invece nel coinvolgimento limitato del cliente rispetto ad altri tipi di offerta.

>[!IMPORTANT]
>Desidero essere contattato non è un tipo di elenco consigliato e deve essere usato solo in situazioni in cui non è possibile stabilire un'esperienza di valutazione. Il coinvolgimento dei clienti è migliore con le offerte Valutazione e Transazione. Se si dispone di un tipo di esperienza di valutazione, durante il processo di onboarding l'utente viene guidato a una di queste opzioni, a seconda dello scenario.

Quando l'offerta è costituita principalmente da servizi professionali, ad esempio valutazioni, implementazioni o workshop, usare il tipo di offerta **Servizi di consulenza**. È necessario definire con precisione l'ambito, la durata e il prezzo dell'offerta. Il servizio deve essere limitato a un singolo cliente e fornito presso la sua sede.

### <a name="trial"></a>VALUTAZIONE

Un'esperienza di valutazione aumenta il livello di coinvolgimento offerto ai clienti e consente pertanto una maggiore esposizione della soluzione offerta. Con una versione di valutazione, i clienti hanno la possibilità di esplorare la soluzione prima di acquistarla. Offrendo un'esperienza di valutazione, si hanno maggiori opportunità di promozione nelle vetrine e con molta probabilità si riesce a raggiungere un numero più ampio di clienti potenziali grazie alle occasioni di coinvolgimento offerte.
 
Tutte le opzioni di valutazione vengono distribuite nell'ambiente di valutazione e/o nella sottoscrizione di Azure dell'editore e non nell'ambiente o nella sottoscrizione di Azure del cliente. L'uso delle versioni di valutazione deve essere gestito dal cliente senza l'acquisto di altri componenti e con un intervento minimo di configurazione per completare un semplice caso d'uso. Le versioni di valutazione devono includere il supporto gratuito almeno per la durata del periodo di valutazione. Per ottenere risultati ottimali, gli utenti di una versione di valutazione devono essere seguiti e monitorati lungo un percorso di valutazione ponderato. Si consiglia agli editori di usare sia i clienti potenziali di Marketplace sia la propria competenza in-app per monitorare e gestire gli utenti di una versione di valutazione.

Gli scenari tipi di valutazione sono 4:


|**Opzione di valutazione**  |**Vantaggi principali**  |**Scegliere questa opzione se...**  |
|---------|---------|---------|
|**versione di prova gratuita**    |     Consente ai clienti di valutare il prodotto prima dell'acquisto, con un metodo automatico per convertire la versione di valutazione in offerta a pagamento, oltre ad offrire modelli di verifica e opportunità di collaborazione con i team di vendita Microsoft. |     La soluzione offerta è di tipo Macchina virtuale o Modello di soluzione OPPURE è una soluzione SaaS E si offre un prodotto SaaS multi-tenant, non si ha un'esperienza consolidata per garantire ai clienti l'operatività immediata ed è configurato un tenant singolo ma si aggiungono clienti come utenti guest.|
**Test drive**     |     Consente ai clienti di valutare il prodotto prima dell'acquisto E offre un'esperienza guidata della soluzione in un'installazione preconfigurata. |   La soluzione offerta è di tipo Macchina virtuale o Modello di soluzione oppure è un'app SaaS con tenant singolo, di cui è complicato effettuare il provisioning OPPURE che non prevede un metodo per convertire la versione di valutazione in offerta a pagamento. |
|**Demo interattiva**    |  Consente ai clienti di osservare come funziona un prodotto senza doversi preoccupare dell'installazione.       |    La soluzione richiede un'installazione complessa che sarebbe difficile completare nel periodo di valutazione.     |


#### <a name="free-trial"></a>Versione di valutazione gratuita

Usare una **versione di valutazione gratuita** quando la soluzione o l'app offre una versione basata su SaaS che è possibile provare gratuitamente. Questa opzione ha il vantaggio di attirare clienti potenziali di qualità tra i clienti interessati, in modo da dare impulso alla propria azienda. Le versioni di valutazione gratuite possono essere presentate come account di valutazione con durata o uso limitato e dovrebbero includere un invito all'azione che consenta di accelerare la conversione all'uso del software a pagamento.

#### <a name="test-drive"></a>Test drive

Usare una **test drive** quando la soluzione viene distribuita tramite una o più macchine virtuali con app IaaS o SaaS. Il vantaggio di questo approccio è dato dal provisioning automatico di un'appliance virtuale o dell'intero ambiente della soluzione all'interno di una "presentazione guidata" ospitata da partner per consentire al cliente di valutare la soluzione senza costi aggiuntivi. Non è necessario che il cliente sia già un utente di Azure e questo può essere utile per generare nuovi clienti potenziali di qualità.

Il tipo di offerta **Test drive** offre anche altri vantaggi:

- Il 27% delle ricerche eseguite dagli utenti in Marketplace è filtrato in modo da visualizzare solo offerte con test drive. 
- Le offerte con test drive generano un numero di clienti potenziali superiore del 38% rispetto alle offerte che ne sono prive. 
- Il 36% delle acquisizioni di nuovi clienti in Azure Marketplace proviene dai clienti che hanno valutato una test drive. 
- Consente ai venditori Microsoft sul campo di comprendere meglio le funzionalità di un prodotto per offrire maggiori opportunità di co-selling.

#### <a name="interactive-demo"></a>Demo interattiva

Usare una **demo interattiva** per offrire ai clienti un'esperienza guidata del prodotto. Il vantaggio di questa opzione è dato dalla possibilità di fornire un'esperienza di valutazione di una soluzione complessa senza dover eseguire complicate operazioni di provisioning. Questa opzione consente ai clienti di farsi un'idea generale della soluzione e all'editore di iniziare a ricevere clienti potenziali a cui presentare offerte di base per dare impulso alla propria azienda. 

### <a name="transact"></a>TRANSAZIONE

In Azure Marketplace usare una **macchina virtuale** quando la soluzione viene distribuita come appliance virtuale nella sottoscrizione del cliente. Le macchine virtuali sono completamente abilitate per la commercializzazione tramite i modelli di licenza con pagamento in base al consumo o Bring Your Own License. Microsoft gestisce la transazione commerciale e addebita il costo al cliente per conto dell'editore. Gli editori hanno il vantaggio di sfruttare la relazione di pagamento preferita del cliente con Microsoft, compreso il contratto Enterprise Agreement. 

>[!NOTE]
>Attualmente, gli impegni monetari nell'ambito di un contratto Enterprise Agreement possono essere applicati all'utilizzo di Azure da parte dell'appliance virtuale, ma non ai costi delle licenze software dell'editore.

Usare un **modello di soluzione di Azure** quando una soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre l'appliance virtuale. I modelli di soluzioni possono automatizzare il provisioning di una o più risorse di macchina virtuale e anche effettuare il provisioning di risorse di rete e archiviazione. I modelli di soluzioni possono offrire i vantaggi dell'automazione sia su singole macchine virtuali sia in ambienti completi di soluzioni basate su IaaS. Per altre informazioni sulla creazione di modelli di soluzioni, visitare [questo sito](https://github.com/MicrosoftDocs/azure-docs).

Usare un' **app gestita di Azure** per distribuire una macchina virtuale o un'intera soluzione basata su IaaS a una sottoscrizione di un cliente quando l'editore o il cliente vuole che la soluzione sia gestita da una terza parte, ad esempio un integratore di sistemi o un provider di servizi gestiti. Per altre informazioni, vedere [questo articolo sulla creazione di app gestite](https://docs.microsoft.com/azure/managed-applications/overview). Per un elenco di domande frequenti, vedere [Domande frequenti su Azure Marketplace](https://azure.microsoft.com/marketplace/faq/).

>[!NOTE]
> Le app gestite devono essere distribuibili tramite il marketplace. Se la comunicazione con il cliente rappresenta un problema, si noti che l'utente non sarà in grado di raggiungere i clienti interessati se è attivata la condivisione dei clienti potenziali.

### <a name="azure-certified"></a>Azure Certified

Tutte le macchine virtuali pubblicate in Azure Marketplace sono testate per il programma **Azure Certified**. Il programma garantisce ai clienti che la macchina virtuale sia compatibile con la piattaforma Azure e con il modello di vendita Marketplace, verifica la conformità ai requisiti di sicurezza dell'immagine online, inclusi virus e malware, e consente l'applicazione di un logo per promuovere l'offerta presso i clienti aziendali Microsoft come soluzione convalidata.

#### <a name="marketplace-commercial-considerations"></a>Considerazioni di natura commerciale su Marketplace

Non sono previste tariffe per la partecipazione a Marketplace. Non viene applicato alcun sistema di ripartizione dei ricavi per la partecipazione a Marketplace quando si pubblicano offerte con le opzioni Inserzione, Valutazione e Transazione BYOL. Per altre informazioni, vedere [Criteri di partecipazione a Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

#### <a name="pay-as-you-go-and-bring-your-own-license-billing-options"></a>Opzioni di fatturazione con pagamento in base al consumo e Bring Your Own License

Quando viene usata l'opzione di pubblicazione Transazione con pagamento in base al consumo, i ricavi risultanti dalla concessione di licenze software in base all'utilizzo vengono ripartiti in percentuale tra l'editore e Microsoft, rispettivamente per l'80% e il 20%. A una singola offerta possono essere applicati entrambi i modelli di fatturazione con pagamento in base al consumo e Bring Your Own License e tali modelli possono coesistere al livello dell'offerta come SKU separati. È possibile definire questa configurazione all'interno dell'offerta nel portale Cloud Partner.

Considerare questo esempio:

Se si abilita l'opzione di pagamento a consumo:


|Costo della licenza   | € 1,00 all'ora        |
|---------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)     | € 0,14 all'ora  |
|**Importo addebitato da Microsoft al cliente**    | **€ 1,14 all'ora**       |

In questo scenario Microsoft addebita 1,14 euro all'ora per l'uso dell'immagine di macchina virtuale pubblicata.


|**Microsoft addebita** |**€ 1,14 all'ora**  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza | € 0,80 all'ora        |
|Microsoft trattiene il 20% del costo della licenza    | € 0,20 all'ora        |
|Microsoft trattiene il costo dell'utilizzo di Azure     |   € 0,14 all'ora      |

Se invece si abilita l'opzione Bring Your Own License:

|Costo della licenza     | Il costo della licenza viene concordato e addebitato dall'editore        |
|---------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    | € 0,14 all'ora         |
|**Importo addebitato da Microsoft al cliente**     | **€ 0,14 all'ora**        |

In questo scenario Microsoft addebita 0,14 euro all'ora per l'uso dell'immagine di macchina virtuale pubblicata. 

|**Microsoft addebita**    |   **€ 0,14 all'ora**      |
|---------|---------|
|Microsoft trattiene il costo dell'utilizzo di Azure     |    € 0,14 all'ora     |
|Microsoft trattiene lo 0% del costo della licenza     |  € 0,00 all'ora       |

### <a name="single-billing-and-payment-methods"></a>Metodi di pagamento e fatturazione singola

Un importante vantaggio offerto dall'opzione di pubblicazione Transazione è dato dal fatto che Microsoft può emettere direttamente al cliente una singola fattura che include i costi di licenza e l'utilizzo di Azure sottostante. In questo scenario Microsoft addebita e riscuote per conto dell'editore, che può quindi evitare di stabilire una relazione diretta di approvvigionamento con il cliente. Ciò consente di risparmiare tempo e risorse evitando le attività di fatturazione per concentrarsi sulle vendite.

### <a name="enterprise-agreement"></a>Enterprise Agreement

I clienti Microsoft usano talvolta un contratto Enterprise Agreement (EA) per pagare i prodotti Microsoft, incluso l'utilizzo di Azure. Questa opzione di pagamento è stata pensata per le organizzazioni che vogliono concedere licenze software e servizi cloud per un periodo minimo di tre anni. I clienti possono scegliere di distribuire i pagamenti anziché effettuare un unico pagamento iniziale. Quando un cliente del contratto EA usa un'opzione di pubblicazione Transazione con pagamento in base al consumo, la fatturazione relativa ai costi di licenza software dell'editore viene eseguita in base al ciclo di fatturazione trimestrale delle eccedenze del contratto EA.

### <a name="monetary-commitment"></a>Impegno monetario 

Ogni cliente del contratto Enterprise Agreement può aggiungere Azure al proprio contratto a fronte di un impegno monetario iniziale per Azure. Questo impegno viene consumato nel corso dell'anno usando una combinazione dei numerosi servizi cloud offerti da Azure nei data center internazionali.

## <a name="prerequisites-for-marketplace-publishing"></a>Prerequisiti per la pubblicazione in Marketplace

### <a name="for-all-marketplace-publishing-options"></a>Per tutte le opzioni di pubblicazione in Marketplace


|**Requisito**  |**Dettagli**  |**Opzione di pubblicazione**  |
|---------|---------|---------|
|**Criteri di partecipazione**    | Esaminare i criteri di partecipazione ad Azure Marketplace [qui] (https://azure.microsoft.com/support/legal/marketplace/participation-policies/).       | Inserzione, Valutazione, Transazione        |
|**Integrazione con Microsoft**    | Le offerte di Azure Marketplace dovrebbero sfruttare o estendere i servizi di Microsoft Azure, come Calcolo, Rete o Archiviazione, e allinearsi a una categoria esistente di Azure Marketplace, ad esempio Database, Sicurezza, Rete e così via. Per l'elenco completo, visitare [questa pagina](https://azuremarketplace.microsoft.com/marketplace/apps).        | Inserzione, Valutazione, Transazione        |
|**Destinatari**    | Le offerte di Azure Marketplace devono essere destinate a professionisti IT, sviluppatori cloud o altri clienti con specifici ruoli tecnici.       |  Inserzione, Valutazione, Transazione 
|**Gestione dei clienti potenziali**    | Per ricevere clienti potenziali da Marketplace, è necessario configurare il proprio sistema CRM (Marketo, Microsoft Dynamics o Salesforce) in modo da accettare i dati relativi ai clienti potenziali.        |   Inserzione, Valutazione, Transazione      |
|**Informativa sulla privacy e condizioni per l'utilizzo**     |   L'informativa sulla privacy dell'editore deve essere disponibile tramite un URL pubblico e le condizioni per l'utilizzo devono essere specificate in formato testo al momento della pubblicazione.      |   Inserzione, Valutazione, Transazione      |
|**Supporto**     |  L'offerta deve includere un URL disponibile pubblicamente in cui i clienti possono ottenere supporto. Per le versioni di valutazione è necessario fornire supporto senza costi aggiuntivi durante il periodo di valutazione.       |  Valutazione, Transazione       |

### <a name="prerequisites-specific-to-trial-publishing"></a>Prerequisiti specifici per la pubblicazione a livello di valutazione

|**Requisito**  | **Dettagli**  |**Opzione di pubblicazione**  |
|---------|---------|---------|
|**Periodo di valutazione gratuita ed esperienza di valutazione**     |  Il cliente deve essere in grado di usare l'app gratuitamente per un periodo di tempo limitato.<br>In altre parole, al cliente non possono essere addebitati né i costi di licenza o di sottoscrizione per il prodotto né il costo del prodotto o del servizio proprietario di Microsoft sottostante. Poiché tutte le opzioni di valutazione vengono distribuite nella sottoscrizione del prodotto Microsoft dell'editore, l'ottimizzazione e la gestione dei costi di valutazione sono controllate esclusivamente dall'editore.<br>È possibile scegliere di offrire una versione di valutazione gratuita, una demo interattiva o una test drive. Indipendentemente dall'opzione selezionata, per la valutazione gratuita è necessario concedere al cliente un periodo minimo di tempo per provare l'app senza costi aggiuntivi.<br> Per iniziare a creare una test drive, visitare il sito cloudmarketplace@microsoft.com. Nota: le esperienze di valutazione SaaS di Azure Marketplace SaaS devono consentire agli utenti di accedere alle credenziali di lavoro personali di Active Directory. [Altre informazioni.](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences) |   Versione di valutazione      | 
| **Soluzione chiavi in mano, facilmente configurabile**    |  L'app deve essere semplice e rapida da configurare e installare.       |  Versione di valutazione       |
|**Disponibilità/Tempo di attività**    |    L'app o la piattaforma SaaS deve avere un tempo di attività pari ad almeno il 99,9%.     |    Versione di valutazione     |
|**Azure Active Directory**    |    L'offerta deve consentire l'accesso Single Sign-On federato di Azure Active Directory (SSO federato di AAD) con il consenso abilitato.      |  Versione di valutazione|

### <a name="prerequisites-specific-to-transact-publishing"></a>Prerequisiti specifici per la pubblicazione a livello di transazione


|**Requisito**  |**Dettagli** |**Opzione di pubblicazione**  |
|---------|---------|---------|
|**Fatturazione e misurazione**    |  La macchina virtuale deve supportare una fatturazione mensile di tipo Bring Your Own License o basata sull'utilizzo.       |    Transazione    |
|**Disco rigido virtuale (VHD) compatibile con Azure**     |   Le macchine virtuali devono essere compilate in [Windows] (https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation) o [Linux] (https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)    |   Transazione      |

### <a name="prerequisites-specific-to-consulting-services-publishing"></a>Prerequisiti specifici per la pubblicazione di servizi di consulenza


|**Requisiti** |**Dettagli**  |**Opzione di pubblicazione**  |
|---------|---------|---------|
|**Caratteristiche delle offerte di servizi**     | Il servizio di consulenza deve essere offerto con un ambito, una durata e un prezzo prestabiliti (o gratuitamente) e deve essere principalmente orientato alla prevendita. Deve inoltre essere limitato a un singolo cliente e fornito presso la sua sede.        |    Elenco     |
|**Requisiti dei partner per i servizi di consulenza**    |   **Solo per AppSource**.  Dynamics 365 for Customer Engagement: [competenza Silver o Gold di Cloud Customer Relationship Management](https://partner.microsoft.com/en-us/membership/cloud-customer-relationship-management-competency). Dynamics 365 for Finance and Operations, Enterprise Edition: competenza Silver o Gold di [Enterprise Resource Planning] (https://partner.microsoft.com/en-us/membership/enterprise-resource-planning-competency) e un fatturato minimo di 25.000 USD in Cloud Operations negli ultimi 12 mesi. Dynamics 365 for Finance and Operations, Business Edition: svolgere la funzione di [Cloud Services Provider (CSP)](https://partner.microsoft.com/en-us/cloud-solution-provider) o [Digital Partner of Record (DPOR)](https://partner.microsoft.com/en-us/membership/digital-partner-of-record) per almeno un cliente. Power BI: soddisfare i criteri di [Partner di soluzioni](file:///C:/Users/ellacroi/Downloads/BI%20Partner%20Program%20Overview%20&%20Incentives.pdf). PowerApps: avere pubblicato una soluzione in [Showcase dei partner] (https://powerapps.microsoft.com/en-us/partner-showcase/) |    Elenco     |

## <a name="using-azure-active-directory-to-enable-trials"></a>Uso di Azure Active Directory per abilitare l'accesso alle versioni di valutazione
Azure Active Directory (AAD) è un servizio di gestione delle identità cloud che consente di eseguire l'autenticazione con un account Microsoft aziendale o dell'istituto di istruzione tramite i protocolli standard OAuth e OpenID Connect. Per altre informazioni su AAD, visitare [questa pagina](https://www.microsoft.com/en-us/cloud-platform/azure-active-directory-features). 

Microsoft usa AAD per autenticare tutti gli utenti di Marketplace. Pertanto, quando un utente autenticato fa clic su un'inserzione relativa a una versione di valutazione in Marketplace e viene reindirizzato all'ambiente di valutazione, l'editore può consentire all'utente di accedere direttamente a tale ambiente senza richiedere un altro passaggio di accesso. Il [token che l'app riceve da AAD in fase di autenticazione] (https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens) include informazioni rilevanti sull'utente che l'editore può usare per creare un account utente nell'app, in modo da automatizzare l'esperienza di provisioning e aumentare così le probabilità che la versione di valutazione venga convertita in offerta a pagamento. 

Uso di AAD per abilitare l'autenticazione con un solo clic all'app o alla versione di valutazione:

- Consente di ottimizzare l'esperienza di accesso dei clienti da Marketplace alla versione di valutazione 
- Gestisce l'aspetto di un'esperienza di prodotto anche quando l'utente viene reindirizzato da Marketplace al dominio o all'ambiente di valutazione dell'editore
- Riduce le probabilità di abbandono al momento del reindirizzamento poiché non è richiesto un altro passaggio di accesso
- Facilita la distribuzione per l'ampia popolazione di utenti AAD

### <a name="certifying-your-azure-active-directory-integration-for-marketplace"></a>Certificazione dell'integrazione di Azure Active Directory per Marketplace

Per le applicazioni multi-tenant:

Se AAD è attualmente supportato dall'editore

- Registrare l'applicazione nel portale di Azure
- Abilitare la funzionalità per il supporto multi-tenancy in AAD per ottenere l'esperienza di valutazione con un solo clic
- Fare clic [qui](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications) per altre informazioni

Se l'editore non ha familiarità con l'accesso SSO federato di AAD

- Registrare l'applicazione nel portale di Azure
- Sviluppare SSO con AAD tramite [OpenID Connect] (https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code) o [OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code)
- Abilitare la funzionalità per il supporto multi-tenancy in AAD per ottenere l'esperienza di valutazione con un solo clic
- Fare clic [qui](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified) per altre informazioni

Applicazioni a tenant singolo:

Per le applicazioni a tenant singolo sono disponibili più opzioni:

- Aggiungere utenti alla directory come utenti guest tramite [Azure B2B] (https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- Effettuare manualmente il provisioning delle versioni di valutazione per i clienti tramite l'opzione "Desidero essere contattato"
- Sviluppare una test drive per ogni cliente
- Creare un'app demo di esempio multi-tenant con SSO

##<a name="publishing-processes-by-product-for-office-dynamics-and-power-bi"></a>Processi di pubblicazione per prodotto per Office, Dynamics e Power BI
Per le app AppSource che estendono Office, Dynamics e Power BI, è possibile acquisire altre informazioni sui requisiti specifici nella documentazione del prodotto in questa sezione. 


|Prodotto |Informazioni di pubblicazione  |
|---------|---------|
|Office 365     |    Esaminare il [processo e le linee guida di pubblicazione]( https://docs.microsoft.com/en-us/office/dev/store/submit-to-the-office-store).     |
|Dynamics 365 for Finance and Operations  |   Quando si esegue la compilazione per Enterprise Edition, esaminare il [processo e le linee guida di pubblicazione](https://docs.microsoft.com/en-us/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source)      |
|Dynamics 365 for Customer Engagement |Esaminare il [processo e le linee guida di pubblicazione](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/developer/publish-app-appsource) |
|Power BI   |     Esaminare il [processo e le linee guida di pubblicazione]( https://docs.microsoft.com/en-us/power-bi/developer/office-store)    |
|Cortana Intelligence     |    Informazioni su [Cortana in AppSource](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide)     |
|Offerte di consulenza di AppSource     |  Esaminare le [linee guida e le informazioni su come inviare l'offerta.]( https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf)    |



## <a name="cloud-partner-portal-pre-publishing-checklist-for-azure-marketplace"></a>Elenco di controllo preliminare alla pubblicazione nel portale Cloud Partner per Azure Marketplace

Prima di iniziare la procedura di pubblicazione, è utile conoscere i componenti necessari per creare un'offerta. Le tabelle seguenti illustrano gli elementi necessari per completare il flusso di lavoro per la creazione di un'offerta. 

### <a name="storefront-details"></a>Dati da visualizzare nella vetrina


|Elemento necessario per la pubblicazione  |Per questo tipo di offerta  |
|---------|---------|
|**Nome dell'offerta (200 caratteri) e descrizione (2000 caratteri)**    |  Tutti        |
|**ID MPN e competenze**   |  Servizi di consulenza       |
|**Disponibilità per paese/area geografica**   | Tutti        |
|**Durata dell'impegno**     |   Servizi di consulenza      |
|**Settori, categorie e parole chiave di ricerca applicabili**     |  Tutti       |
|**Logo dell'azienda (48x48, 216x216)**     |  Servizi di consulenza       |
|**Video di presentazione del prodotto (facoltativo)**  |  Tutti       |
|**Screenshot (al massimo 5, 1280x720)**   |    Tutti     |
|**Documenti di marketing (al massimo 3)**    |  Tutti       |
|**Clienti potenziali destinatari**    |   Tutti      |

### <a name="contacts"></a>Contatti


|Elemento necessario per la pubblicazione  |Per questo tipo di offerta  |
|---------|---------|
|**Informazioni di contatto (supporto, progettazione, commerciale)**    |    Tutti     |

### <a name="technical-info"></a>Informazioni tecniche


|Elemento necessario per la pubblicazione  |Per questo tipo di offerta |
|---------|---------|
|**URL della versione di valutazione**     |  Tutti i tipi di offerta di valutazione       |
|**Lingue supportate**    |   Tutti i tipi di offerta di valutazione      |
|**Numero di versione e data di rilascio dell'app**    |   Tutti i tipi di offerta di valutazione      |
|**URL del supporto**    |   Tutti i tipi di offerta di valutazione, Macchine virtuali      |
|**URL dell'informativa sulla privacy e delle condizioni per l'utilizzo**     |    Tutti     |

### <a name="test-drive"></a>Test drive


|Elemento necessario per la pubblicazione  |Per questo tipo di offerta  |
|---------|---------|
|**Descrizione e durata**     |  Solo per test drive       |
|**Manuale dell'utente**     |   Solo per test drive      |
|**Video della test drive (al massimo 1)**     |  Solo per test drive       |
|**Disponibilità della test drive per paese/area geografica**    |   Solo per test drive      |
|**Nome del gruppo di risorse di Azure**   |         |
|**ID della sottoscrizione di Azure**     |  Solo per test drive       |
|**ID del tenant di Azure AD**   |    Solo per test drive     |
|**ID dell'app di Azure AD**  |  Solo per test drive       |
|**Chiave dell'app di Azure AD**     |   Solo per test drive      |

### <a name="storefrontmarketplace"></a>Vetrina/Marketplace


|Elemento necessario per la pubblicazione  |Per questo tipo di offerta  |
|---------|---------|
|**Titolo (al massimo 50 caratteri)**    |  Transazione ' Macchine virtuali, App di Azure (Modelli di soluzioni e App gestite)       |
|**Riepilogo (al massimo 200 caratteri)**    |  Transazione ' Macchine virtuali, App di Azure (Modelli di soluzioni e App gestite)       |
|**Riepilogo esteso (al massimo 256 caratteri)**     |   Transazione ' Macchine virtuali, App di Azure (Modelli di soluzioni e App gestite)      |
|**Descrizione basata su HTML (al massimo 3000 caratteri)**    |  Transazione ' Macchine virtuali, App di Azure (Modelli di soluzioni e App gestite)       |
|**Logo dell'azienda (40x40, 90x90, 115x115, 255x115, 815x290)**    |  Transazione ' Macchine virtuali, App di Azure (Modelli di soluzioni e App gestite)       |

### <a name="sku"></a>SKU


|Elemento necessario per la pubblicazione  |Per questo tipo di offerta  |
|---------|---------|
|**Numero di versione**     |    Transazione ' App di Azure (Modelli di soluzioni e App gestite)     |
|**File del pacchetto contenente tutti file modello e createUIDefinitionFile**   |Transazione ' App di Azure (Modelli di soluzioni e App gestite)         |
|**Dati del sistema operativo**    |   Transazione ' Macchine virtuali      |
|**Porte e protocolli in uso**    |  Transazione ' Macchine virtuali       |
|**Versione del disco e URL SAS per ogni VHD in uso**   |  Transazione ' Macchine virtuali       |

## <a name="become-a-publisher"></a>Diventare un editore

In questa sezione sono illustrati i passaggi per diventare un editore in Azure Marketplace e AppSource e per ottenere l'accesso al portale Cloud Partner che si userà per creare, pubblicare e gestire l'offerta. 

### <a name="process-overview"></a>Panoramica del processo


|Passaggi per la registrazione in Marketplace  |Tempo  |DESCRIZIONE  |
|---------|---------|---------|
|Creare un ID Microsoft     |   15 minuti      |   I partner devono avere un ID Microsoft che viene usato per identificarli. L'ID Microsoft verrà usato per accedere al portale Cloud Partner.       |
|Inviare il modulo di candidatura a Marketplace     |  1-3 giorni       |  I partner devono inviare il modulo di candidatura per iniziare il processo di approvazione per Marketplace. Dopo l'invio del modulo, il team che si occupa dell'onboarding in Marketplace esaminerà l'applicazione e convaliderà la richiesta.       |
|Eseguire la registrazione in Developer Center     |    5-10 giorni     | La registrazione in Microsoft Developer Center è necessaria per consentire a Microsoft di verificare che il partner sia una persona giuridica valida con un ID fiscale valido per il paese in cui è registrato. Developer Center assegnerà al partner la qualifica di Sviluppatore Microsoft registrato e gli fornirà l'accesso al programma per sviluppatori di Azure. <br><br>*Nota: se non si è completato il modulo di candidatura a Marketplace, verrà chiesto di pagare una tariffa di registrazione pari a 99 USD. Per evitare di pagare questa tariffa, completare il modulo di candidatura a Marketplace in modo da ricevere un codice promozionale tramite posta elettronica.*  |
|Accedere al portale Cloud Partner     |  15 minuti       |   Dopo aver ricevuto dal team di Marketplace la comunicazione che la sua candidatura è stata approvata, il partner può accedere al [portale Cloud Partner](https://cloudpartner.azure.com/). Per accedere al proprio profilo di pubblicazione nel portale Cloud Partner, il partner deve usare l'ID Microsoft specificato nel modulo di candidatura. Una volta registrato in Developer Center, il partner dovrà associare l'account di Developer Center al proprio profilo di pubblicazione di Azure Marketplace per pubblicare le offerte.      |

#### <a name="create-a-microsoft-id"></a>Creare un ID Microsoft

Nel corso dell'intera procedura di pubblicazione in Marketplace, si userà una indirizzo di posta elettronica che identifica l'account di Marketplace. Questo indirizzo deve essere registrato come ID Microsoft e verrà usato sia per [Microsoft Developer Center](https://developer.microsoft.com/) sia per il [portale Cloud Partner](https://cloudpartner.azure.com/). È sufficiente avere un solo account con ID Microsoft per le offerte di Azure Marketplace e AppSource, ma si sconsiglia fortemente di condividere tale account con altri servizi o offerte.

L'indirizzo di posta elettronica selezionato deve essere preferibilmente incluso nel dominio aziendale e controllato dal team IT. Vedere le sezioni in [Linee guida e procedure](#Guidelines-and-How-Tos), incluse le linee guida per creare un ID Microsoft per gestire un account nel marketplace e le linee guida relative agli ID Microsoft nei domini federati di AAD per indicazioni precedenti alla creazione di un ID. 

#### <a name="submit-the-marketplace-nomination-form"></a>Inviare il modulo di candidatura a Marketplace
Nel corso della procedura di onboarding in Marketplace, è necessario inviare un modulo di candidatura in cui specificare le informazioni sull'applicazione o sul servizio offerto, i dati relativi alla propria azienda e il livello di supporto che si prevede di offrire.  
Dopo l'invio del modulo, il team di Marketplace esaminerà l'applicazione e convaliderà la richiesta. Al termine della verifica, si riceverà tramite posta elettronica una notifica in cui sono specificati i passaggi successivi da completare per diventare un partner approvato nel portale Cloud Partner. Inviare la propria candidatura a uno degli indirizzi seguenti:

Candidatura per Azure Marketplace: http://aka.ms/listonazuremarketplace   
Candidatura per AppSource: http://aka.ms/listonappsource

#### <a name="register-in-the-developer-center"></a>Eseguire la registrazione in Developer Center

[Microsoft Developer Center](https://developer.microsoft.com/) viene usato per registrare le informazioni della propria azienda. La persona che effettua la registrazione deve essere un rappresentante valido dell'azienda e deve specificare le informazioni personali per convalidare la propria identità. La persona che esegue la registrazione deve usare un ID Microsoft condiviso in ambito aziendale, che deve essere usato anche nel [portale Cloud Partner](https://cloudpartner.azure.com/). 

>[!IMPORTANT]
>Assicurarsi che l'azienda non possieda già un account Microsoft Developer Center prima di provare a crearne uno.

Durante il processo, Microsoft raccoglierà informazioni sull'indirizzo dell'azienda, sul conto bancario e sui dati fiscali. Tali informazioni possono generalmente essere ottenute da contatti finanziari o aziendali. Per avanzare attraverso le diverse fasi di creazione e distribuzione dell'offerta è inoltre necessario completare i componenti seguenti del profilo di pubblicazione:


|**Profilo di pubblicazione**  |**Avvio configurazione profilo**  |**Staging**  |**Inserzione e valutazione**  |**Transazione**
|---------|---------|---------|---------|---------|
|**Registrazione dell'azienda**     | **Obbligatorio**        |  **Obbligatorio**       | **Obbligatorio**        |  **Obbligatorio**       |
|**ID del profilo fiscale**   |    Facoltativo     |    Facoltativo     |  Facoltativo       | **Obbligatorio**      |
|**Conto bancario**     |   Facoltativo      |    Facoltativo     |  Facoltativo       |  **Obbligatorio**      |

Per istruzioni dettagliate su questa procedura, vedere la sezione "Istruzioni su come eseguire la registrazione in Developer Center" più avanti in questa guida. 

#### <a name="log-in-to-the-cloud-partner-portal"></a>Accedere al portale Cloud Partner

Dopo che si è ricevuta la comunicazione che la candidatura è stata approvata dal team di Marketplace e si è completata la registrazione a [Microsoft Developer Center](https://dev.windows.com), verrà creato un account per consentire l'accesso al [portale Cloud Partner](https://cloudpartner.azure.com). Nel messaggio di posta elettronica di approvazione della candidatura saranno incluse le credenziali di accesso iniziali. 

Usare l'account di Marketplace (ID Microsoft) per accedere al profilo di pubblicazione. Una volta eseguito l'accesso al portale Cloud Partner, l'ultimo passaggio consiste nell'associare l'account di Developer Center al rispettivo profilo di pubblicazione di Marketplace per pubblicare le offerte. Questa operazione può essere eseguita nel profilo di pubblicazione all'interno del portale Cloud Partner tramite il pulsante nella parte inferiore della schermata.

Per informazioni dettagliate su come usare il portale Cloud Partner, fare riferimento al menu [Learn](https://cloudpartner.azure.com/#Learn) (Formazione) nel portale e quindi fare clic nella sezione relativa alla documentazione. 


## <a name="getting-support"></a>Ottenere assistenza

La sezione seguente include l'elenco delle opzioni di supporto per Azure Marketplace:

**Domande generali su Azure Marketplace:**
|Canale di supporto |DESCRIZIONE |
|---------|---------|
|Posta elettronica: cloudmarketplace@microsoft.com     |  Lista di distribuzione del supporto per l'onboarding. Viene usata per gestire le richieste di onboarding e per organizzare sessioni per l'individuazione di clienti e la progettazione dell'architettura con i partner.        |

**Supporto per la pubblicazione in Azure Marketplace:**

|Canale di supporto  |DESCRIZIONE  |
|---------|---------|
|Posta elettronica: 
azurecertified@microsoft.com |   Fornisce il supporto ai partner per la pubblicazione di applicazioni in Azure Marketplace. Basato sull'orario di ufficio nel fuso orario del Pacifico.      |
|Posta elettronica:
AzureMarketOnboard@microsoft.com |   Fornisce il supporto per la procedura di invio del modulo di candidatura a Marketplace. Basato sull'orario di ufficio nel fuso orario del Pacifico.      |
|Posta elettronica: 
Amp-testdrive@microsoft.com |   Fornisce l'accesso onboarding a Test drive. Basato sull'orario di ufficio nel fuso orario del Pacifico. | 

**Supporto per il portale di Azure Marketplace:**

|Canale di supporto  |DESCRIZIONE  |
|---------|---------|
|Posta elettronica: [supporto](https://go.microsoft.com/fwlink/?linkid=844975)    |   Supporto per il portale di pubblicazione in Marketplace. Disponibile 24 ore su 24, 7 giorni su 7.        |

**Supporto tecnico**


|Canale di supporto  |DESCRIZIONE  |
|---------|---------|
|Slack: [Aderire a Marketplace su Slack](https://join.marketplace.azure.com)    |   Ambiente Slack per il supporto tecnico dei partner. Attualmente più di 350 partner usano questo ambiente.        |
|Forum MSDN: [Marketplace](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=DataMarket)     | Forum di Microsoft Developer Network.         |
|StackOverflow: [Azure](https://stackoverflow.com/questions/tagged/azure)     |    Il sito Web di StackOverflow include più sezioni che forniscono un ambiente per ottenere soluzioni e porre domande su qualsiasi argomento relativo ad Azure e AMP:<ul><li>StackOverflow: [Azure Marketplace](https://stackoverflow.com/questions/tagged/azure-marketplace)</li><li>StackOverflow: [Azure Resource Manager](https://stackoverflow.com/questions/tagged/azure-resource-manager)</li><li>StackOverflow: [Macchine virtuali di Azure](https://stackoverflow.com/questions/tagged/azure-virtual-machine)</li></ul> |


**Risorse di marketing**

|Canale di supporto  |DESCRIZIONE  |
|---------|---------|
|Posta elettronica: cosell@microsoft.com    |  Fornisce il supporto per i processi di onboarding e per le domande sul programma di co-selling. Basato sul fuso orario del Pacifico.        |
|Posta elettronica: gtm@microsoft.com    |  Fornisce il supporto per informazioni sui vantaggi del programma Go-To-Market. Basato sull'orario di ufficio nel fuso orario del Pacifico.        |
|Posta elettronica: CEBrand@Microsoft.com     |  Fornisce il supporto per informazioni sull'utilizzo del marchio e dei logo di Azure.       |

## <a name="guidelines-and-how-tos"></a>Linee guida e procedure dettagliate

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-an-azure-marketplace-account"></a>Linee guida per la creazione di un ID Microsoft per gestire un account di Azure Marketplace

Quando si crea un account aziendale, seguire queste linee guida se più di una persona dovrà accedere all'account tramite la registrazione con l'account di Microsoft che ha aperto l'account.

>[!IMPORTANT]
>Per consentire a più utenti di accedere all'account di Dev Center, è consigliabile usare Azure Active Directory per assegnare ruoli a singoli utenti, che possono accedere all'account usando le proprie credenziali di Azure AD. Per altre informazioni, vedere [Linee guida relative agli ID Microsoft nei domini federati di AAD](#guidance-with-aad-federated-domains). Creare l'account di Microsoft usando un indirizzo di posta elettronica appartenente al dominio aziendale, ma non a un singolo utente, ad esempio windowsapps@fabrikam.com.

- Limitare l'accesso a questo account Microsoft al minor numero possibile di sviluppatori.
- Impostare una lista di distribuzione di posta elettronica aziendale che includa tutti gli utenti che hanno necessità di accedere all'account per sviluppatori e aggiungere questo indirizzo email alle informazioni di sicurezza. In questo modo tutti i dipendenti nella lista riceveranno i codici di sicurezza quando necessario e potranno gestire le informazioni di sicurezza dell'account Microsoft. Se non è possibile impostare una lista di distribuzione, il proprietario del singolo account di posta elettronica dovrà accedere e condividere il codice di sicurezza quando richiesto (ad esempio quando viene aggiunta una nuova informazione di sicurezza all'account o quando si deve eseguire l'accesso da un nuovo dispositivo).
- Aggiungere un numero di telefono aziendale che non richieda un'estensione e che sia accessibile ai membri principali del team.
- In generale, gli sviluppatori devono usare dispositivi affidabili per accedere all'account per gli sviluppatori dell'azienda. Tutti i principali membri del team devono avere accesso a questi dispositivi affidabili. Questo riduce la necessità di inviare i codici di sicurezza quando si accede all'account.
- Se si desidera consentire l'accesso all'account a un computer non affidabile, limitare tale accesso a un massimo di cinque sviluppatori. In teoria, questi sviluppatori devono accedere all'account da computer che condividono la stessa area geografica e lo stesso percorso di rete.
- Verificare spesso le informazioni di sicurezza dell'azienda all'indirizzo [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) per accertarsi che siano aggiornate.

L'accesso all'account sviluppatore deve avvenire principalmente da PC affidabili. Questo è fondamentale poiché è previsto un limite al numero di codici generati per ogni account, ogni settimana. Consente inoltre di eseguire un accesso il più trasparente possibile.
Per maggiori informazioni sulla sicurezza e le linee guida dell'account per sviluppatori, fare clic [qui](https://docs.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account).

### <a name="guidance-for-microsoft-ids-in-aad-federated-domain"></a>Linee guida relative agli ID Microsoft nei domini federati di AAD

L'account aziendale può essere federato tramite [Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/) e restituisce un errore se si prova a creare un ID Microsoft con un indirizzo di posta elettronica aziendale. Se viene restituito un errore, contattare il team IT aziendale per assicurarsi che il problema riguardi questo caso specifico. Si tratta di un problema noto che Microsoft è attualmente impegnata a risolvere. La soluzione alternativa è la seguente:

È consigliabile creare un nuovo indirizzo di posta elettronica nel dominio **@outlook.com**. A tale scopo, seguire questa procedura:

1. Andare a [https://signup.live.com/signup](https://signup.live.com/signup) e selezionare **Crea un nuovo indirizzo e-mail**.


2. Creare il nuovo indirizzo di posta elettronica e immettere una password. In questo modo verranno creati un nuovo ID Microsoft e una nuova cassetta di posta elettronica nel servizio outlook.com. Continuare il processo di registrazione finché non viene creato l'account.

>[!IMPORTANT]
>Assicurarsi che l'ID di posta elettronica o la lista di distribuzione (si consiglia l'uso di una lista di distribuzione per eliminare la dipendenza da una singola persona) che sarà usata per la registrazione in Dev Center sia prima registrata come un account Microsoft. In caso contrario, registrarsi mediante questo [collegamento](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1).

Inoltre, per la registrazione in Dev Center, non possono essere usati **ID di posta elettronica appartenenti al dominio Microsoft**.

Una volta creato l'ID Microsoft con questo account, accedere alla [cassetta postale](https://outlook.live.com/owa/) dell'account e creare una regola per inoltrare tutti i messaggi di posta elettronica presenti in questa cassetta postale all'indirizzo di posta elettronica creato nel dominio per gestire l'account di Azure Marketplace. Fare clic su questo [collegamento](https://support.office.com/en-us/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed) per informazioni su come creare una regola di inoltro di posta elettronica in outlook.com.

Al termine di quest'ultimo passaggio, tutte le comunicazioni o tutti i messaggi relativi all'ID Microsoft verranno inviati all'account di posta elettronica personale nel dominio. L'indirizzo di posta elettronica @outlook.com dovrà essere usato per eseguire l'autenticazione sia a Microsoft Developer Center sia al portale Cloud Partner.

### <a name="instructions-on-how-to-register-in-the-development-center"></a>Istruzioni su come eseguire la registrazione in Developer Center

1. Aprire una nuova sessione di navigazione in incognito su Chrome o InPrivate Browsing in Internet Explorer per assicurarsi di non essere connessi a un account personale.
2. Andare a [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) per registrarsi come venditore in Dev Center. Prima di procedere, leggere la seguente nota importante.

   ![Indirizzo di posta elettronica per Dev Center](./media/marketplace-publishers-guide/registerdevcenteremail.png)

3. Completare la "Procedura guidata per la protezione dell'account", che verificherà l'identità tramite il numero di telefono o l'indirizzo di posta elettronica.
4. Nella sezione "Informazioni sull'Account di registrazione" selezionare **il paese/l'area geografica dell'account** nel menu a discesa.

   ![Informazioni sull'account](./media/marketplace-publishers-guide/devcenterregistrationaccountinfo.png)
   
   >[!WARNING]
   >Paesi di origine della vendita: per vendere i propri servizi in Azure Marketplace, l'entità registrata deve trovarsi in uno dei paesi di origine della vendita approvati. Questa limitazione viene applicata per motivi legati ai proventi e alla tassazione. Per altre informazioni, vedere i [Criteri di partecipazione a Marketplace]https://azure.microsoft.com/support/legal/marketplace/participation-policies/.

5. Selezionare **Azienda** per "Tipo di account" e fare clic sul pulsante **Avanti**.

   >[!IMPORTANT]
   >Per comprendere meglio le caratteristiche dei tipi di account disponibili e individuare quello più adatto alle proprie esigenze, visitare la pagina [Tipi di account, aree geografiche e tariffe](https://docs.microsoft.com/en-us/windows/uwp/publish/account-types-locations-and-fees)

6. Immettere il **Nome visualizzato dell'editore**, in genere il nome della società.

   >[!TIP]
   >Il nome visualizzato dell'autore immesso in Dev Center non sarà mostrato in Azure Marketplace quando l'offerta sarà elencata. Deve tuttavia essere compilato per completare il processo di registrazione.

7. Immettere le **informazioni di contatto** per la verifica dell'account.

   >[!IMPORTANT]
   >È necessario fornire informazioni accurate, perché saranno usate nel processo di verifica dell'azienda per essere approvata in Developer Center.

8. Immettere le informazioni di contatto per il **responsabile approvazione aziendale**. Il responsabile approvazione aziendale è la persona che può verificare che si è autorizzati a creare un account in Dev Center per conto dell'organizzazione. Al termine, fare clic su **Avanti** per passare alla **"sezione dei pagamenti"**.

   ![Pagamento in Dev Center](./media/marketplace-publishers-guide/devcenterregistrationpayment.png)

9. Immettere le informazioni per il pagamento dell'account. Se si ha un codice promozionale che copre il costo della registrazione, è possibile immettere qui. In caso contrario, specificare le informazioni della carta di credito o di PayPal nei mercati supportati. Al termine, fare clic su **Avanti** per passare alla **schermata di verifica**.

   ![Pagamento in Dev Center](./media/marketplace-publishers-guide/devcenterregistrationpayment2.png)

10. Esaminare le informazioni dell'account e verificare che tutto sia corretto. Leggere e accettare le condizioni del Contratto Microsoft Marketplace per Editori. Selezionare la casella per indicare di aver letto e accettato queste condizioni.
11. Fare clic su **Fine** per confermare la registrazione. Verrà inviato un messaggio di conferma all'indirizzo di posta elettronica.
12. Se si prevede di pubblicare solo offerte gratuite, fare clic su **Go to the [Cloud Partner Portal](https://cloudpartner.azure.com)** (Vai al portale Cloud Partner).

Se si prevede di pubblicare offerte commerciali (TRANSAZIONE), ad esempio per una macchina virtuale con un modello di fatturazione oraria, fare clic sul collegamento **Update your account information** (Aggiorna le informazioni sull'account) per completare le informazioni fiscali e bancarie nel proprio account di Developer Center.

Se si preferisce aggiornare le informazioni fiscali e bancarie in un secondo momento, passare alla sezione successiva. 

>[!IMPORTANT]
>In caso di offerte commerciali (TRANSAZIONE), non è possibile passare le offerte alla produzione senza completare le informazioni fiscali e bancarie.

#### <a name="add-tax-and-banking-information"></a>Aggiungere le informazioni fiscali e bancarie

Per pubblicare offerte commerciali per l'acquisto, è necessario aggiungere anche informazioni sui pagamenti e fiscali e inviarle per la convalida al Developer Center. Se invece si prevede di pubblicare solo offerte gratuite o BYOL, non è necessario aggiungere queste informazioni. È possibile aggiungerle in un secondo momento, ma questo richiede del tempo per convalidare le informazioni fiscali. Se si sa che si offriranno offerte commerciali per l'acquisto, è consigliabile aggiungerle non appena possibile.

**Informazioni bancarie**
1. Accedere a Microsoft Developer Center con l'account Microsoft.
2.  Fare clic su **Account proventi** nel menu a sinistra, quindi in **Scegli il metodo di pagamento** fare clic su **Conto bancario** o **PayPal**.

   >    [!IMPORTANT]
   >    Se si dispone di offerte commerciali che i clienti acquistano nel Marketplace, questo è l'account in cui si riceveranno i pagamenti per tali acquisti.

3. Immettere le informazioni di pagamento e fare clic su Salva quando si è soddisfatti.

   >    [!IMPORTANT]
   >    Se è necessario aggiornare o modificare l'account di pagamento, seguire la stessa procedura precedente, sostituendo le informazioni attuali con quelle nuove. La modifica dell'account per il pagamento può ritardare i pagamenti per un massimo di un ciclo di pagamento. Questo ritardo si verifica perché è necessario verificare la modifica dell'account, come è stato fatto per la configurazione iniziale dell'account di pagamento. Dopo aver verificato l'account, verrà comunque pagato per l'intero importo. Ii pagamenti in scadenza per il ciclo pagamento corrente saranno aggiunti a quello successivo.

4. Fare clic su **Avanti**. 

**Informazioni fiscali**

1. Accedere a [Microsoft Developer Center](https://dev.windows.com) con l'account Microsoft, se necessario.
2. Fare clic su **Profilo fiscale** nel menu a sinistra.
3. Nella pagina **Configura modulo per la tassazione** selezionare il paese o l'area geografica in cui ha la residenza permanente, quindi selezionare il paese o l'area geografica della cittadinanza principale. Fare clic su **Avanti**.
4. Immettere i dettagli fiscali e quindi fare clic su **Avanti**.

   >    [!WARNING]
   >    Non sarà possibile eseguire il push in produzione delle offerte commerciali senza completare le informazioni fiscali e del conto bancario nell'account Microsoft Developer Center.

In caso di problemi con la registrazione in Developer Center, inoltrare un ticket di supporto come indicato di seguito:

1. Andare al collegamento per il supporto tecnico https://developer.microsoft.com/windows/support
2. Nella sezione **Contattaci** fare clic sul pulsante **Invia una richiesta di assistenza**, come illustrato nella schermata seguente.
3. Scegliere "Help with Dev Center" (Aiuto con Dev Center) come **Tipo di problema** e "Publish and manage apps" (Pubblicare e gestire le app) come **Categoria**. Fare quindi clic sul pulsante "Start email" (Inizia messaggio di posta elettronica).

   ![Problema di Dev Center](./media/marketplace-publishers-guide/devcentersubmitincident.png)

4. Viene visualizzata una pagina di accesso. Usare qualsiasi account Microsoft per effettuare l'accesso. Se non si ha un account Microsoft, crearne uno seguendo questo collegamento. 
5. Immettere i dettagli del problema e inviare il ticket facendo clic sul pulsante **Invia**.