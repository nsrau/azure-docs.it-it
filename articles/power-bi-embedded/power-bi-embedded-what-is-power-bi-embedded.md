<properties
   pageTitle="Cos'è Microsoft Power BI Embedded"
   description="Power BI Embedded consente di integrare report di Power BI in applicazioni Web o applicazioni mobili. In questo modo non è necessario compilare soluzioni personalizzate per visualizzare i dati degli utenti."
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="jocaplan"/>


# Cos'è Microsoft Power BI Embedded

**Microsoft Power BI Embedded** consente di integrare report di Power BI in applicazioni Web o applicazioni mobili. In questo modo non è necessario compilare soluzioni personalizzate per visualizzare i dati degli utenti.

![](media\powerbi-embedded-whats-is\what-is.png)

**Microsoft Power BI Embedded** è un servizio di Azure che consentirà ai fornitori di software indipendente di vivere l'esperienza dati di Power BI nelle proprie applicazioni. I fornitori di software indipendente hanno applicazioni compilate con utenti e funzionalità specifiche. Queste app possono inoltre contenere alcuni elementi dati predefiniti, ad esempio grafici e report che ora possono essere supportati da **Microsoft Power BI Embedded**. Per poter usare l'applicazione, non è necessario che gli utenti abbiano un account Power BI. Possono continuare ad accedere all'applicazione come facevano prima e possono visualizzare e usare i report e i riquadri di Power BI senza richiedere altre licenze.

## Licenza di Microsoft Power BI Embedded

Nel modello di utilizzo di **Microsoft Power BI Embedded** non è l'utente finale il responsabile della gestione delle licenze di Power BI. I **rendering** sono in realtà acquistati dallo sviluppatore dell'app che impiega gli oggetti visivi e sono addebitati alla sottoscrizione che comprende tali risorse.

## Modello concettuale di Microsoft Power BI Embedded

![](media\powerbi-embedded-whats-is\model.png)

Come per qualsiasi altro servizio di Azure, il provisioning delle risorse di **Microsoft Power BI Embedded** avviene tramite le [API di Azure ARM](https://msdn.microsoft.com/library/mt712306.aspx). In questo caso la risorsa di cui si effettua il provisioning è una **raccolta di aree di lavoro di Power BI**.

## Raccolta di aree di lavoro

La **raccolta di aree di lavoro** è il contenitore di primo livello di Azure che contiene da zero a più **aree di lavoro**. La **raccolta di** **aree di lavoro** ha tutte le proprietà standard di Azure e gli elementi seguenti:

-	**Chiavi di accesso**: chiavi usate per chiamare in modo protetto le API Power BI. Sono descritte in una sezione successiva.
-	**Utenti** : gli utenti di Azure Active Directory (AAD) con diritti di amministratore per gestire la raccolta di aree di lavoro di Power BI tramite il portale di Azure o l'API di ARM.
-	**Area**: come componente di provisioning di una **raccolta di aree di lavoro**, è possibile selezionare un'area in cui effettuare il provisioning. Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

## Area di lavoro

L'**area di lavoro** è un contenitore di contenuto Power BI che può includere set di dati, report e dashboard. L'**area di lavoro** è vuota quando viene creata. Nella versione di anteprima sarà necessario creare tutto il contenuto usando Power BI Desktop e caricarlo in una delle aree di lavoro usando le [API REST di Power BI](http://docs.powerbi.apiary.io/reference).

## Uso delle raccolte di aree di lavoro e delle aree di lavoro
Le **raccolte di aree di lavoro** e le **aree di lavoro** sono contenitori di contenuto usate e organizzate nel miglior modo affinché si adattino al modello dell'applicazione che si sta compilando. I modi per organizzare il contenuto all'interno di questi elementi possono essere molteplici. È possibile scegliere di inserire tutto il contenuto all'interno di un'area di lavoro e di usare successivamente i token delle app per suddividere ulteriormente il contenuto tra i clienti. È possibile anche decidere di inserire tutti i clienti in aree di lavoro distinte in modo che rimangano separati. In alternativa è possibile organizzare gli utenti per area anziché per cliente. Questa progettazione flessibile consente di scegliere il modo migliore per organizzare il contenuto.
## Origini dati nella versione di anteprima

La versione di anteprima consentirà l'uso di una serie di origini dati limitata, come descritto di seguito:

### Query diretta

Nella versione di anteprima sarà garantito il supporto di connessione a sorgenti cloud tramite query diretta. Sarà quindi possibile connettersi alle origini dati per visualizzare i dati più recenti. È necessario che queste origini dati siano raggiungibili dal cloud e siano accessibili tramite autenticazione di base. Alcuni dei candidati ideali sono:

-	SQL Azure
-	SQL Azure DW
-	HD Insight Spark

## Set di dati memorizzati nella cache

Nella versione di anteprima è possibile usare i set di dati memorizzati nella cache. Non è tuttavia possibile aggiornare i dati memorizzati nella cache dopo averli caricati in **Microsoft Power BI Embedded**.

## Autenticazione e autorizzazione con token delle app

**Microsoft Power BI Embedded** incarica l'applicazione dell'utente affinché esegua tutte le operazioni necessarie per l'autenticazione e l'autorizzazione utente. Non è assolutamente necessario che gli utenti finali siano clienti di Azure Active Directory. Sarà invece l'applicazione ad autorizzare il rendering di un report di Power BI in **Microsoft Power BI Embedded** usando **token di autenticazione delle applicazioni**. I **token delle app** vengono creati nel momento in cui l'app intende eseguire il rendering di un report. Vedere la sezione relativa ai [token delle app](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

### Token di autenticazione delle applicazioni

I **token delle app** vengono usati per eseguire l'autenticazione in **Microsoft Power BI Embedded**. Esistono tre tipi di **token delle app**:

1.	Token di provisioning: sono usati durante il provisioning di una nuova **area di lavoro** in una **raccolta di aree di lavoro**
2.	Token di sviluppo: sono usati per chiamare direttamente le **API REST di Power BI**
3.	Token d'incorporamento: sono usati quando si eseguono chiamate per il rendering di un report nell'iframe incorporato

Questi token vengono usati nelle varie fasi di interazione con **Microsoft Power BI Embedded**. I token sono progettati in modo che sia possibile delegare le autorizzazioni dalla propria app a Power BI.

### Generazione dei token delle app

Gli SDK (Software Development Kit) disponibili nella versione di anteprima consentono di generare i token. È prima necessario chiamare uno dei metodi Create\_\_\_Token() Chiamare poi il metodo Generate() usando la chiave di accesso ottenuta dalla **raccolta di aree di lavoro**. I metodi di base per la creazione dei token sono definiti nella classe PowerBI.Security.PowerBIToken e sono i seguenti:

-	[CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx)
-	[CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx)
-	[CreateReportEmbedToken](https://msdn.microsoft.com/library/mt710366.aspx)

Per un esempio su come usare i metodi [CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx) e [CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx), vedere [Introduzione al codice di esempio di Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md).


## Vedere anche
- [Scenari comuni di Microsoft Power BI Embedded](power-bi-embedded-scenarios.md)
- [Introduzione alla versione di anteprima di Microsoft Power BI Embedded](power-bi-embedded-get-started.md)
- [Token delle app](power-bi-embedded-get-started-sample.md#key-flow)
- [API REST di Power BI](http://docs.powerbi.apiary.io/reference)
- [Aree di Azure](https://azure.microsoft.com/regions/)

<!---HONumber=AcomDC_0420_2016-->