<properties
   pageTitle="Panoramica della cache locale del servizio app di Azure"
   description="Questo articolo descrive come abilitare, ridimensionare ed eseguire una query sullo stato della funzionalità di cache locale del servizio app di Azure"
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords="Destinato all'uso esclusivo di champ SEO. Separare i termini con virgole. Verificare il proprio champ SEO prima di modificare il contenuto in questo articolo contenente questi termini."/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# Panoramica della cache locale del servizio app di Azure

Il contenuto delle applicazioni Web di Azure è memorizzato nell'archiviazione di Azure e presentato in modo permanente come condivisione di contenuto. Tale progettazione è finalizzata al funzionamento con un'ampia gamma di applicazioni e ha i seguenti attributi:

* Il contenuto è condiviso tra più istanze di macchina virtuale dell'applicazione Web. 
* Il contenuto è durevole e può essere modificato dalle applicazioni Web in esecuzione. 
* I file di log e file dei dati di diagnostica sono disponibili nella stessa cartella del contenuto condivisa. 
* La pubblicazione di nuovo contenuto aggiorna direttamente la cartella del contenuto e può essere visualizzata immediatamente tramite il sito Web SCM e l'app Web in esecuzione (in genere alcune tecnologie, ad esempio ASP.NET, riavviano l'applicazione Web in presenza di alcune modifiche di file per visualizzare il contenuto più recente). 

Mentre molte applicazioni Web usano una o tutte queste funzionalità, alcune applicazioni Web richiedono solo un archivio dei contenuti di sola lettura ad alte prestazioni da cui possono essere eseguite con disponibilità elevata. Queste applicazioni possono trarre vantaggio da una copia specifica dell'istanza di macchina virtuale del contenuto chiamata "Cache locale". La _cache locale_ offre una visualizzazione di ruolo Web del contenuto. Il contenuto è costituito da una cache di scrittura con rimozione del contenuto di archiviazione creata in modo asincrono all'avvio del sito. Quando la cache è pronta, nel sito viene impostata l'esecuzione nel contenuto memorizzato nella cache. Le applicazioni Web eseguite nella cache locale presentano i vantaggi seguenti:

* Non si verificano latenze durante l'accesso al contenuto nell'archiviazione di Azure 
* Non vengono influenzati dai server che eseguono gli aggiornamenti pianificati di condivisione del contenuto o da tempi di inattività non pianificati e altre interruzioni dell'archiviazione di Azure. 
* Le modifiche alla condivisione dell'archiviazione causano un numero ridotto di riavvii dell'applicazione. 

## Come viene modificato il comportamento del servizio app dalla cache locale

* La cache locale è una copia delle cartelle /site e /siteextensions dell'applicazione Web e viene creata nell'istanza locale della macchina virtuale all'avvio dell'applicazione Web. La dimensione della cache locale di ogni applicazione Web è limitata a 300 MB per impostazione predefinita ma può essere aumentata fino a 1 GB. 
* Sebbene la cache locale sia di lettura/scrittura, le modifiche vengono ignorate durante il riavvio dell'applicazione Web o lo spostamento di macchine virtuali da parte dell'applicazione. Non usare la cache locale per le applicazioni che salvano in modo permanente dati cruciali nell'archivio del contenuto. 
* Le applicazioni Web possono continuare a scrivere i file di log e dati di diagnostica. I file di log e i dati vengono tuttavia archiviati in locale nella macchina virtuale e vengono quindi copiati periodicamente nell'archivio del contenuto condiviso. La copia nell'archivio del contenuto condiviso è una soluzione senza garanzia e i writeback potrebbero essere persi a causa di un arresto improvviso dell'istanza di una macchina virtuale. 
* La struttura delle cartelle LogFiles e Data delle app Web che usano la cache locale è stata modificata. Le cartelle "LogFiles" e "Data" includono ora sottocartelle che seguono il modello di denominazione "identificatore univoco" + timestamp. Ogni sottocartella corrisponde a un'istanza di macchina virtuale in cui l'applicazione Web è in esecuzione o è stata eseguita.  
* Le modifiche di pubblicazione nell'applicazione Web tramite uno dei meccanismi di pubblicazione verranno pubblicate nell'archivio del contenuto condiviso. Si tratta di un comportamento previsto da progettazione finalizzato a rendere durevole il contenuto pubblicato. Per aggiornare la cache locale dell'applicazione Web, è necessario riavviarla. Può sembrare una misura eccessiva? Di seguito viene descritto come semplificare il ciclo di vita.
* D:\\Home punterà alla cache locale. D:\\local continuerà a puntare all'archivio temporaneo specifico della macchina virtuale. 
* La visualizzazione del contenuto predefinita del sito SCM continuerà a essere quella dell'archivio del contenuto condiviso. Per esaminare la cartella della cache locale è possibile passare a https://[site-name].scm.azurewebsites.net/VFS/LocalSiteRoot/LocalCache

## Come abilitare la cache locale nel servizio app di Azure

La cache locale viene configurata mediante una combinazione di impostazioni di app riservate. Le impostazioni di app possono essere configurate usando:

* [Portale di Azure](#Configure-Local-Cache-Portal)
* [Gestione risorse di Azure](#Configure-Local-Cache-ARM)

### Procedura: Configurare la cache locale tramite il portale di Azure
<a name="Configure-Local-Cache-Portal"></a>

La cache locale viene abilitata per la singola applicazione Web usando un'AppSetting. `WEBSITE_LOCAL_CACHE_OPTION` = `Always`

![Impostazioni delle app del portale di Azure: cache locale](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### Procedura: Configurare la cache locale tramite Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300" 
    }
}

...
```

## Come modificare le dimensioni della cache locale del servizio app?

Per impostazione predefinita la dimensione della cache locale è **300 MB**. Sono incluse le cartelle Site e SiteExtensions copiate dall'archivio del contenuto nonché eventuali log e cartelle di dati creati in locale. Per aumentare questo limite, usare l'AppSetting `WEBSITE_LOCAL_CACHE_SIZEINMB`. La dimensione può essere aumentata fino a **1 GB** (1000 MB) per ogni applicazione Web.

## Procedure consigliate per l'uso della cache locale del servizio app

Si consiglia di usare la cache locale insieme alla funzionalità degli [ambienti di gestione temporanea](../app-service-web/web-sites-staged-publishing.md).

* Aggiungere l'Appsetting _permanente_ `WEBSITE_LOCAL_CACHE_OPTION` con il valore `Always` nello **slot di produzione**. Se si usa `WEBSITE_LOCAL_CACHE_SIZEINMB`, aggiungerlo anche come impostazione permanente nello slot di produzione. 
* Creare uno slot di gestione temporanea e pubblicare nello slot di gestione temporanea. Lo slot di gestione temporanea in genere non usa la cache locale per abilitare un semplice ciclo di vita di compilazione-distribuzione-test per la gestione temporanea pur usufruendo dei vantaggi della cache locale per lo slot di produzione. 
*	Testare il sito nello slot di gestione temporanea.  
*	Al termine, eseguire un'[operazione di scambio](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots) tra lo slot di **gestione temporanea** e lo slot di **produzione**.  
*	Le impostazioni permanenti sono specificate per nome e sono permanenti in uno slot. Di conseguenza, quando lo slot di gestione temporanea viene scambiato in slot di produzione, lo slot erediterà le impostazioni app della cache locale. Il nuovo slot di produzione scambiato verrà eseguito nella cache locale dopo alcuni minuti e verrà riscaldato durante il riscaldamento dello slot successivo allo scambio. Pertanto, quando lo scambio di slot è stato completato, lo slot di produzione verrà eseguito nella cache locale.

## Domande frequenti

### Come è possibile stabilire se la cache locale può essere applicata all'applicazione Web? 

Se l'applicazione Web richiede un archivio del contenuto a prestazioni elevate e affidabile e non usa l'archivio del contenuto per scrivere dati critici in fase di esecuzione e ha una dimensione totale inferiore a 1 GB, è possibile usare la cache locale. Per determinare la dimensione totale delle cartelle del sito e delle estensioni del sito è possibile usare l'estensione del sito relativa all'utilizzo del disco delle app Web di Azure.
 
### Come è possibile abilitare la cache locale? 

Vedere la sezione sulle procedure consigliate per l'uso della cache locale.
 
### Come è possibile verificare se il sito usa la cache locale? 

Se viene usata la funzionalità della cache locale con gli ambienti di gestione temporanea, l'operazione di scambio non verrà completata fino a quando viene eseguito il riscaldamento della cache locale. Per verificare se il sito viene eseguito nella cache locale, è possibile controllare la variabile di ambiente del processo di lavoro `WEBSITE_LOCALCACHE_READY`. Seguire le istruzioni seguenti per accedere alla variabile di ambiente del processo di lavoro in più istanze.
 
### Ho appena pubblicato nuove modifiche, ma sembra che non siano presenti nell'applicazione Web. Perché? 
Se l'applicazione Web usa la cache locale, è necessario riavviare il sito per visualizzare le ultime modifiche. Se non si desidera eseguire l'operazione in un sito di produzione, vedere le opzioni di slot descritte sopra.
 
### Dove si trovano i log? 

Se si usa la cache locale, i log e le cartelle di dati sono leggermente diversi. Tuttavia, la struttura delle sottocartelle rimane la stessa ad eccezione del fatto che le cartelle sono annidate in una sottocartella con formato "identificatore macchina virtuale univoco" + timestamp.
 
### Ho abilitato la cache locale ma l'applicazione Web viene ancora riavviata. Perché? Pensavo che la cache locale fosse utile in caso di riavvii frequenti dell'app. 

La cache locale consente di evitare i riavvii dell'applicazione Web correlati all'archiviazione. Tuttavia, l'applicazione Web può comunque essere sottoposta a riavvii durante gli aggiornamenti di infrastruttura pianificati della macchina virtuale. In generale, il numero di riavvii dell'applicazione con la cache locale abilitata dovrebbe essere inferiore.

<!---HONumber=AcomDC_0309_2016-->