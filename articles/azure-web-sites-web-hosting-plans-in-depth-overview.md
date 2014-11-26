<properties title="Azure Websites Web Hosting Plans In-Depth Overview" pageTitle="Azure Websites Web Hosting Plans In-Depth Overview - Windows Azure feature guide" description="Learn how Web Hosting Plans for Azure Websites work, and how they benefit your management experience." metaKeywords="Azure Web Sites, Azure Websites, WHP, Web Hosting Plan, Web Hosting Plans, Resource Groups" services="web-sites" solutions="web" documentationCenter="Infrastructure" authors="Byron Tardif and Yochay Kiryaty" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Byron="" Tardif="" and="" Yochay="" Kiryaty" />
</br>

# Informazioni dettagliate sui piani di hosting Web di Siti Web di Azure

</br>
I piani di hosting Web rappresentano un set di funzionalità e capacità che è possibile condividere tra tutti i siti Web in uso. I piani di hosting Web supportano i 4 livelli di prezzo di Siti Web di Azure, ad esempio Gratuito, Condiviso, Basic e Standard. Ogni livello prevede funzionalità e capacità specifiche. I siti nella stessa sottoscrizione, gruppo di risorse e area geografica possono condividere un piano di hosting Web. Tutti i siti che condividono un piano di hosting possono usare le funzionalità e le caratteristiche definite dal livello del piano di hosting Web. Tutti i siti Web associati a un piano di hosting Web specifico vengono eseguiti nelle risorse definite dal piano di hosting Web. Se ad esempio il piano di hosting Web è configurato per l'uso di due macchine virtuali di dimensione "piccola", tutti i siti associati a tale piano di hosting Web verranno eseguiti in entrambe le macchine virtuali. Come sempre in Siti Web di Azure, le macchine virtuali in cui vengono eseguiti i siti prevedono gestione completa e disponibilità elevata.
</br>
Questo articolo descrive le caratteristiche principali, ad esempio livello e scalabilità di un piano di hosting Web e come queste caratteristiche influiscono sulla gestione dei siti Web.
</br>

## Siti Web, piani di hosting Web e gruppi di risorse

</br>
Un sito Web può essere associato a un solo piano di hosting Web. Un piano di hosting Web è a sua volta associato a un gruppo di risorse. Il gruppo di risorse è un nuovo concetto di Azure che funge da limite del ciclo di vita per ogni risorsa contenuta al suo interno. I gruppi di risorse consentono di gestire contemporaneamente tutte le parti di un'applicazione.
</br>
In un gruppo di risorse possono essere presenti più piani di hosting Web e ogni piano di hosting Web dispone di un set di funzionalità e caratteristiche specifiche che possono essere usate dai siti Web ad esso associati. L'immagine seguente illustra questa relazione:
</br>
</br>
![Resource Groups and Web Hosting Plans][Resource Groups and Web Hosting Plans]
</br>
</br>
La possibilità di avere più piani di hosting Web in un unico gruppo di risorse consente di allocare siti diversi a risorse diverse, in particolare le macchine virtuali in cui vengono eseguiti i siti Web. Questa possibilità consente ad esempio di separare le risorse dei siti di sviluppo e test e di quelli di produzione, per allocare un piano di hosting Web e il relativo set di risorse dedicate per i siti di produzione e un secondo piano di hosting Web per i siti di sviluppo e test.
</br>
Grazie alla disponibilità di più piani di hosting Web in un singolo gruppo di risorse è possibile definire un'applicazione che si estende su più aree geografiche. Ad esempio, un sito Web a disponibilità elevata in esecuzione in due aree geografiche includerà due siti Web, uno per ogni area geografica, e un sito Web associato a ogni piano di hosting Web. In questo caso, tutti i siti saranno associati a un singolo gruppo di risorse, che costituisce un'applicazione. Una visualizzazione centralizzata di un gruppo di risorse con più piani di hosting Web e più siti semplifica la gestione, il controllo e la visualizzazione dell'integrità dei siti Web. Oltre a gestire le risorse dei siti Web e i rispettivi siti per un'applicazione specifica, è possibile associare qualsiasi risorsa di Azure correlata, ad esempio database SQL di Azure e Progetti del team.
</br>

## Casi in cui è necessario creare un nuovo gruppo di risorse e un nuovo piano di hosting Web

</br>
Durante la creazione di un nuovo sito Web, valutare la possibilità di creare un nuovo gruppo di risorse quando il sito Web che si sta per creare rappresenta una nuova applicazione Web. In questo caso, creare un nuovo gruppo di risorse, un piano di hosting Web associato e i siti Web è la scelta ideale. Quando si crea un nuovo sito Web di questo tipo nel nuovo portale di anteprima di Azure tramite la raccolta o la nuova opzione Sito Web + SQL, per impostazione predefinita il portale crea un nuovo gruppo di risorse e un piano di hosting Web per il nuovo sito. Se necessario, è tuttavia possibile sovrascrivere queste impostazioni predefinite.
</br>
</br>
![Creating a new Web Hosting Plan][Creating a new Web Hosting Plan]
</br>
</br>
Sarà sempre possibile aggiungere un nuovo sito Web o qualsiasi altra risorsa a un gruppo di risorse esistente. Quando si crea un nuovo sito Web dal contesto di un gruppo di risorse esistente, la Creazione guidata Nuovo sito Web sceglie per impostazione predefinita la risorsa e il piano di hosting Web esistente. Anche in questo caso, se necessario, è possibile modificare queste impostazioni predefinite. Quando si aggiunge un nuovo sito Web a un gruppo di risorse esistente, è possibile scegliere di aggiungere il sito a un piano di hosting esistente (opzione predefinita nel nuovo portale di anteprima di Azure) oppure è possibile creare un nuovo piano di hosting Web a cui aggiungere il sito.
</br>
La creazione di un nuovo piano di hosting consente di allocare un nuovo set di risorse ai siti Web e offre maggiore controllo sull'allocazione delle risorse, in quanto ogni piano di hosting Web dispone di un set di macchine virtuali dedicato. Poiché è possibile spostare i siti Web tra i piani di hosting Web, supponendo che i piani di hosting Web si trovino nelle stesse aree geografiche, la decisione su creare o meno un nuovo piano di hosting Web è meno importante. Se un determinato sito Web inizia a consumare un numero eccessivo di risorse o se è necessario separare alcuni siti Web, è possibile creare un nuovo piano di hosting Web e spostare i siti Web al suo interno.
</br>
Se si vuole creare un nuovo sito Web in un'area geografica diversa in cui non sono presenti piani di hosting Web esistenti, sarà necessario crearne uno nuovo in quell'area per potervi associare un sito Web.
</br>
È tuttavia importante ricordare che non è possibile spostare piani di hosting Web o siti Web tra gruppi di risorse. Inoltre, non è possibile spostare un sito Web tra due piani di hosting Web che si trovano in due aree geografiche diverse.
</br>

## Gruppi di risorse esistenti nel portale di anteprima di Azure

</br>
Se in Siti Web di Azure sono già presenti siti Web esistenti, si sarà notato che tutti i siti Web vengono ora visualizzati nel portale di anteprima di Azure. Per visualizzare tutti i siti Web in un elenco semplice, fare clic sul pulsante **sfoglia** nel riquadro di spostamento sinistro e selezionare **Siti Web**:
</br>
</br>
![See all your website as a flat list][See all your website as a flat list]
</br>
</br>
Per visualizzare tutti i gruppi di risorse creati automaticamente, fare clic sul pulsante **sfoglia** nel riquadro di spostamento sinistro e selezionare **Gruppi di risorse**:
</br>
</br>
![See all the resource groups that have been created][See all the resource groups that have been created]
</br>
</br>
Sarà anche possibile notare che è presente un gruppo di risorse predefinito generato automaticamente in ogni area geografica in cui sono in esecuzione i siti Web. Il nome del gruppo di risorse generato automaticamente per i siti Web è *Web-Predefinito-<location name>* dove il nome della località rappresenta un'area geografica di Azure, ad esempio *Web-Predefinito-StatiUnitiOccidentali*. In ogni gruppo di risorse sono presenti tutti i siti esistenti per l'area geografica del gruppo. Ogni sito che è stato creato in passato o che verrà creato in futuro nel Portale di Azure completo o nel portale di anteprima di Azure sarà disponibile in entrambi i portali.
</br>
Poiché ogni sito Web deve essere associato a un piano di hosting Web, sono disponibili piani di hosting Web predefiniti per i siti esistenti in base alla convenzione seguente, in ogni area geografica:
</br>

-   Tutti i siti Web del piano **Gratuito** sono associati a un piano di hosting Web **Predefinito** con il relativo livello di prezzo impostato su **Gratuito**.
    </br>
-   Tutti i siti Web del piano **Condiviso** sono associati a un piano di hosting Web **Predefinito** con il relativo livello di prezzo impostato su **Condiviso.**
    </br>
-   Tutti i siti Web del piano **Standard** sono associati a un piano di hosting Web Predefinito con il relativo livello di prezzo impostato su **Standard**.
    </br>
    Il nome di questo piano di hosting Web è **DefaultServerFarm**. Questo nome è stato scelto per il supporto di un'API legacy. Il nome **ServerFarm** può creare confusione perché fa riferimento a un **Piano di hosting Web**, ma è importante notare che si tratta di un piano di hosting Web e non di un'entità a sé stante.
    </br>

    ## Domande frequenti sui piani di hosting Web

    </br>
    **Domanda**: Come si crea un piano di hosting Web?
    </br>
    **Risposta**: Un piano di hosting Web è un contenitore e, in quanto tale, non è possibile creare un piano di hosting Web vuoto. Tuttavia, un nuovo piano di hosting Web viene creato in modo esplicito durante la creazione del sito.
    </br>
    Per eseguire questa operazione nell'interfaccia utente del nuovo **Portale di anteprima di Azure**, fare clic su **NUOVO** e selezionare **Sito Web**. Verrà aperto il pannello di creazione del sito Web. L'immagine seguente mostra l'icona **NUOVO** nella parte inferiore sinistra e la seconda immagine illustra il pannello di creazione **Sito Web**, il pannello **Piano di hosting Web** e il pannello **Livello di prezzo**:
    </br>
    </br>
    ![Create a new website][Create a new website]
    </br>
    </br>
    ![Website, Web Hosting Plan and pricing tier blades][Website, Web Hosting Plan and pricing tier blades]
    </br>
    </br>
    Per questo esempio è stato scelto di creare un nuovo sito Web denominato **contosomarketing** e di posizionarlo nel nuovo piano di hosting Web denominato **contoso**. Il livello di prezzo selezionato per questo piano di hosting Web è **Standard piccolo**. Per altre informazioni sui livelli di prezzo del piano di hosting Web e sulle relative funzionalità e opzioni di prezzo e scalabilità incluse in ognuno di essi, vedere le [informazioni specifiche relative ai piani di hosting Web per Siti Web di Azure][informazioni specifiche relative ai piani di hosting Web per Siti Web di Azure].
    </br>
    Si noti inoltre che è possibile creare un piano di hosting Web anche nel portale di Azure esistente. Questa opzione è disponibile nella procedura guidata **creazione rapida** quando si seleziona **Crea nuovo piano di hosting Web** nel menu a discesa **PIANO DI HOSTING WEB**:
    </br>
    </br>
    ![Create new web hosting plan in the existing portal][Create new web hosting plan in the existing portal]
    </br>
    </br>
    Per questo esempio verrà creato un nuovo sito denominato **northwind** e si sceglierà di creare un nuovo piano di hosting Web. Il risultato di questa operazione sarà un nuovo piano di hosting Web denominato **default0** che conterrà il sito Web **northwind**. Tutti i piani di hosting Web creati tramite questa esperienza seguono questa convenzione di denominazione e non è possibile rinominarli dopo che sono stati creati. Inoltre, i piani di hosting Web creati tramite questo processo verranno creati nel livello di prezzo **Gratuito**.
    </br>
    **Domanda**: Come si assegna un sito a un **Piano di hosting Web**?
    </br>
    **Risposta**: I siti vengono assegnati a un piano di hosting Web durante il processo di creazione del sito. Per eseguire questa operazione nell'interfaccia utente del nuovo **Portale di anteprima di Azure**, fare clic su **NUOVO** e selezionare **Sito Web**:
    </br>
    </br>
    ![Create a new website][1]
    </br>
    </br>
    Nel pannello di creazione del sito Web selezionare quindi il piano di hosting:
    </br>
    </br>
    ![Select a hosting plan][Select a hosting plan]
    </br>
    </br>
    È anche possibile creare un sito in un piano di hosting Web specifico tramite il portale di Azure esistente. È possibile eseguire questa operazione durante la procedura guidata **creazione rapida**. Dopo avere digitato l'URL del sito Web, usare l'elenco a discesa **PIANO DI HOSTING WEB** per selezionare un piano a cui aggiungere il sito:
    </br>
    </br>
    ![Select a hosting plan in the existing portal][Select a hosting plan in the existing portal]
    </br>
    </br>
    **Domanda**: Come è possibile spostare un sito in un piano di hosting Web diverso?
    </br>
    **Risposta**: È possibile spostare un sito in un altro piano di hosting Web nel portale di anteprima di Azure. È possibile spostare i siti Web da un piano di hosting Web all'altro nella stessa area geografica appartenente allo stesso gruppo di risorse.
    </br>
    Per spostare il sito in un altro piano, passare al pannello Sito Web del sito che si vuole spostare. Fare quindi clic su **Piano di hosting Web**:
    </br>
    </br>
    ![Choose a new or existing web hosting plan][Choose a new or existing web hosting plan]
    </br>
    </br>
    Verrà aperto il pannello Piano di hosting Web. A questo punto, è possibile selezionare un piano di hosting Web esistente o crearne uno nuovo. I piani in località geografiche o gruppi di risorse diverse sono visualizzati in grigio e non possono essere selezionati.
    </br>
    Si noti che ogni piano di hosting Web è associato a un livello di prezzo specifico. Quando si sposta un sito da un piano di hosting Web **Gratuito** a un piano di hosting Web **Standard**, il sito Web può sfruttare tutte le funzionalità e le risorse del livello Standard.
    </br>
    </br>
    **Domanda**: Come funziona la scalabilità di un piano di hosting Web?
    </br>
    **Risposta**: Sono disponibili due modi per ridimensionare un piano di hosting Web. Il primo consiste nell'aumentare il piano di hosting Web e tutti i siti associati a tale piano. Modificando il livello di prezzo di un piano di hosting Web, tutti i siti inclusi in tale piano saranno soggetti alle funzionalità e alle risorse definite da quel livello di prezzo specifico.
    </br>
    L'immagine seguente mostra il pannello **Piano di hosting Web** e il pannello **Livello di prezzo**. Se si fa clic sulla parte **Livello di prezzo** nel pannello **Piano di hosting Web**, il pannello **Livello di prezzo** si espande ed è possibile modificare il livello di prezzo per il piano:
    </br>
    </br>
    ![The Web Hosting Plan blade and the Pricing Tier][The Web Hosting Plan blade and the Pricing Tier]
    </br>
    </br>
    In alternativa, è possibile ridimensionare il piano aumentando il numero delle istanze incluse nel piano di hosting Web. L'immagine seguente mostra il pannello **Piano di hosting Web** e il pannello **Scalabilità**. Se si fa clic sull'area Scalabilità nel pannello **Piano di hosting Web**, questo si espande e consente di modificare il numero di istanze del piano:
    </br>
    </br>
    ![Changing the instance count of a hosting plan][Changing the instance count of a hosting plan]
    </br>
    </br>
    Poiché il piano di hosting Web nell'immagine precedente è configurato per l'uso del livello di prezzo **Standard**, l'opzione **Scalabilità automatica** è abilitata.
    </br>
    Per eseguire questa operazione nel portale di Azure completo, passare alla scheda **Scalabilità**, come mostrato di seguito:
    </br>
    </br>
    ![Changing the instance count of a hosting plan in the existing portal][Changing the instance count of a hosting plan in the existing portal]
    </br>
    </br>
    **Domanda**: Come è possibile eliminare un piano di hosting Web?
    </br>
    **Risposta**: Per eliminare un piano di hosting Web è prima necessario eliminare tutti i siti Web ad esso associati. Dopo avere eliminato tutti i siti Web in un piano di hosting Web, è possibile eliminare il piano dal pannello Piano di hosting Web:
    </br>
    </br>
    ![Deleting a web hosting plan][Deleting a web hosting plan]
    </br>
    </br>
    Nel portale di Azure completo con l'eliminazione dell'ultimo sito Web in un piano di hosting Web viene eliminato automaticamente il piano associato.
    </br>
    **Domanda**: Come è possibile monitorare un piano di hosting Web?
    </br>
    **Risposta**: È possibile monitorare i piani di hosting Web nelle parti Monitoraggio del pannello Piano di hosting Web:
    </br>
    </br>
    ![Monitoring a web hosting plan][Monitoring a web hosting plan]
    </br>
    </br>
    Per personalizzare i controlli di monitoraggio, fare clic con il pulsante destro del mouse sul controllo e scegliere **modifica query**:
    </br>
    </br>
    ![Editing the monitoring controls][Editing the monitoring controls]
    </br>
    </br>
    Le metriche esposte sono:
    </br>

-   Percentuale CPU
    </br>
-   Percentuale memoria
    </br>
-   Lunghezza coda disco
    </br>
-   Lunghezza coda HTTP.
    </br>
    Queste metriche rappresentano l'utilizzo medio tra istanze appartenenti a un piano di hosting Web. È possibile usare tutte queste metriche per impostare avvisi e regole di scalabilità automatica.
    </br>

    ## Punti in evidenza e conclusioni

    </br>
    I piani di hosting Web rappresentano un set di funzionalità e capacità che è possibile condividere tra tutti i siti Web in uso. Un piano di hosting Web garantisce la flessibilità per allocare siti specifici a un determinato set di risorse (macchine virtuali) e di ottimizzare ulteriormente l'allocazione e l'utilizzo delle risorse di Azure per i siti Web.

  [Resource Groups and Web Hosting Plans]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png
  [Creating a new Web Hosting Plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png
  [See all your website as a flat list]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png
  [See all the resource groups that have been created]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png
  [Create a new website]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png
  [Website, Web Hosting Plan and pricing tier blades]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png
  [informazioni specifiche relative ai piani di hosting Web per Siti Web di Azure]: http://go.microsoft.com/?linkid=9845586
  [Create new web hosting plan in the existing portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png
  [1]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png
  [Select a hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png
  [Select a hosting plan in the existing portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png
  [Choose a new or existing web hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png
  [The Web Hosting Plan blade and the Pricing Tier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png
  [Changing the instance count of a hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png
  [Changing the instance count of a hosting plan in the existing portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png
  [Deleting a web hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png
  [Monitoring a web hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png
  [Editing the monitoring controls]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png
