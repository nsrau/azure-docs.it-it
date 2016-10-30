<properties
   pageTitle="Cos'è Microsoft Power BI Embedded?"
   description="Power BI Embedded consente di integrare report di Power BI in applicazioni Web o applicazioni mobili. In questo modo non è necessario compilare soluzioni personalizzate per visualizzare i dati degli utenti."
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>


# <a name="what-is-microsoft-power-bi-embedded?"></a>Cos'è Microsoft Power BI Embedded?

**Power BI Embedded**consente di integrare i report di Power BI direttamente nelle applicazioni Web o per dispositivi mobili.

![](media\powerbi-embedded-whats-is\what-is.png)

Power BI Embedded è un **servizio di Azure** che consente ai fornitori di software indipendente e agli sviluppatori di app di sfruttare i vantaggi dell'esperienza dati di Power BI nelle proprie applicazioni. Gli sviluppatori creano applicazioni, ognuna delle quali ha utenti e set di funzionalità specifici. Queste app possono inoltre contenere alcuni elementi dati predefiniti, ad esempio grafici e report che ora possono essere supportati da Microsoft Power BI Embedded. Per usare l'app non è necessario che gli utenti abbiano un account Power BI. Possono continuare ad accedere all'applicazione come in precedenza, nonché visualizzare i report di Power BI e interagire con questi senza dover richiedere licenze aggiuntive.

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Licenza di Microsoft Power BI Embedded

Nel modello di utilizzo di **Microsoft Power BI Embedded** l'utente finale non è il responsabile della gestione delle licenze di Power BI.  I **rendering** sono in realtà acquistati dallo sviluppatore dell'app che impiega gli oggetti visivi e sono addebitati alla sottoscrizione che comprende tali risorse.

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Modello concettuale di Microsoft Power BI Embedded

![](media\powerbi-embedded-whats-is\model.png)

Come per qualsiasi altro servizio di Azure, il provisioning delle risorse di Power BI Embedded avviene tramite le [API di Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). In questo caso la risorsa di cui si effettua il provisioning è una **Raccolta di aree di lavoro di Power BI**.

## <a name="workspace-collection"></a>Raccolta di aree di lavoro

La **Raccolta di aree di lavoro** è il contenitore di Azure di primo livello per le risorse che contengono da zero a più **aree di lavoro**.  La **Area di lavoro** **aree di lavoro** has all of the standard Lazure properties, as well as the following:

-   **Chiavi di accesso**: chiavi usate per chiamare in modo sicuro le API di Power BI, descritte in una sezione successiva.
-   **Utenti** : utenti di Azure Active Directory (AAD) con diritti di amministratore per gestire la raccolta di aree di lavoro di Power BI tramite il portale di Azure o l'API di Azure Resource Manager.
-   **Area**: nell'ambito del provisioning di una **accolta di aree di lavoro** è possibile selezionare un'area in cui effettuare il provisioning. Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Area di lavoro

L' **Area di lavoro** è un contenitore di contenuto Power BI che può includere set di dati, report e dashboard. L' **Area di lavoro** è vuota quando viene creata. Nella versione di anteprima sarà necessario creare tutto il contenuto usando Power BI Desktop e caricarlo in una delle aree di lavoro usando le [API REST di Power BI](http://docs.powerbi.apiary.io/reference).

## <a name="using-workspace-collections-and-workspaces"></a>Uso delle raccolte di aree di lavoro e delle aree di lavoro
Le **raccolte di aree di lavoro** e le **aree di lavoro** sono contenitori di contenuto usate e organizzate nel modo più efficiente perché si adattino alla progettazione dell'applicazione che si sta compilando. I modi per organizzare il contenuto all'interno di questi elementi possono essere molteplici. È possibile scegliere di inserire tutto il contenuto all'interno di un'area di lavoro e di usare successivamente i token delle app per suddividere ulteriormente il contenuto tra i clienti. È possibile anche decidere di inserire tutti i clienti in aree di lavoro distinte in modo che rimangano separati. In alternativa è possibile organizzare gli utenti per area anziché per cliente. Questa progettazione flessibile consente di scegliere il modo migliore per organizzare il contenuto.

## <a name="cached-datasets"></a>Set di dati memorizzati nella cache

Nella versione di anteprima è possibile usare i set di dati memorizzati nella cache.  Non è tuttavia possibile aggiornare i dati memorizzati nella cache dopo averli caricati in **Microsoft Power BI Embedded**.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticazione e autorizzazione con token delle app

**Microsoft Power BI Embedded** incarica l'applicazione dell'utente affinché esegua tutte le operazioni necessarie per l'autenticazione e l'autorizzazione utente. Non è necessario che gli utenti finali siano clienti di Azure Active Directory.  Sarà invece l'applicazione ad autorizzare il rendering di un report di Power BI in **Microsoft Power BI Embedded** usando **token di autenticazione dell'applicazione**.  I **token dell'app** vengono creati nel momento in cui l'app intende eseguire il rendering di un report.  Vedere la sezione relativa ai [token delle app](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

I**token di autenticazione dell'applicazione (token dell'app)** vengono usati per eseguire l'autenticazione in **Microsoft Power BI Embedded**.  Esistono tre tipi di **token dell'app**:

1.  Token di provisioning: si usano per il provisioning di una nuova **area di lavoro** in una **raccolta di aree di lavoro**
2.  Token di sviluppo: si usano per chiamare direttamente le **API REST di Power BI**
3.  Token d'incorporamento: sono usati quando si eseguono chiamate per il rendering di un report nell'iframe incorporato

Questi token si usano nelle varie fasi di interazione con **Microsoft Power BI Embedded**.  I token sono progettati in modo che sia possibile delegare le autorizzazioni dalla propria app a Power BI. Per altre informazioni, vedere [Flusso dei token delle app](power-bi-embedded-app-token-flow.md).

## <a name="see-also"></a>Vedere anche
- [Scenari comuni di Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Introduzione a Microsoft Power BI Embedded](power-bi-embedded-get-started.md)



<!--HONumber=Oct16_HO2-->


