---
title: 'Avvio rapido: Creare un indice di ricerca di Azure in Java'
description: Viene illustrato come creare un indice, caricare i dati ed eseguire query usando Java e le API REST di ricerca di Azure.
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018, seo-java-july2019
ms.openlocfilehash: 7deb9d2cf16aa82de7ce4ea163652c2936819063
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533245"
---
# <a name="quickstart-create-an-azure-search-index-in-java"></a>Avvio rapido: Creare un indice di ricerca di Azure in Java
> [!div class="op_single_selector"]
> * [Portale](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Informazioni su come compilare un'applicazione di ricerca Java personalizzata che utilizza Ricerca di Azure per l’esperienza di ricerca. L'esercitazione utilizza l’ [API REST del servizio Ricerca di Azure](https://msdn.microsoft.com/library/dn798935.aspx) per costruire gli oggetti e le operazioni utilizzati in questo esercizio.

Per eseguire questo esempio, è necessario un servizio di Ricerca di Azure, a cui è possibile iscriversi nel [portale di Azure](https://portal.azure.com). Per istruzioni dettagliate, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md) .

Per compilare e testare questo esempio è stato utilizzato il seguente software:

* [Eclipse IDE per sviluppatori Java EE](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers). Assicurarsi di scaricare la versione EE: uno dei passaggi di verifica richiede una funzionalità presente solo in questa edizione.
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat 8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>Informazioni sui dati
L’applicazione di esempio usa i dati dei [servizi geologici degli Stati Uniti (USGS)](https://geonames.usgs.gov/domestic/download_data.htm)con il filtro dello stato del Rhode Island per ridurre la dimensione del set di dati. Tali dati saranno utilizzati per compilare un'applicazione di ricerca che restituisce gli edifici di riferimento quali ospedali e scuole nonché caratteristiche geologiche come fiumi, laghi e vette.

In questa applicazione, il programma **SearchServlet. Java** compila e carica l'indice usando un costrutto dell'[indicizzatore](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando il set di dati di USGS filtrato da un database SQL di Azure. Nel codice del programma vengono fornite credenziali predefinite e la connessione all'origine dati online. In termini di accesso ai dati, non è necessaria alcuna ulteriore configurazione.

> [!NOTE]
> A questo set di dati è stato applicato un filtro per restare sotto il limite di 10.000 documenti del livello di prezzo gratuito. Se si utilizza il livello standard, questo limite non viene applicato ed è possibile modificare il codice per l'utilizzo di un set di dati più grande. Per ulteriori informazioni sulla capacità per ogni livello di prezzo, vedere [Limiti e vincoli](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>Informazioni sui file di programma
Nell'elenco seguente vengono descritti i file che sono rilevanti per questo esempio.

* Search.jsp: fornisce l'interfaccia utente
* SearchServlet.java: fornisce i metodi (simile a un controller in MVC)
* SearchServiceClient.java: gestisce le richieste HTTP
* SearchServiceHelper.java: classe di supporto che fornisce metodi statici
* Document.java: fornisce il modello di dati
* config.properties: Imposta l'URL del servizio di ricerca e`api-key`
* pom.xml: dipendenza Maven

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Trovare il nome del servizio `api-key` e del servizio di ricerca di Azure
Per tutte le chiamate API REST in ricerca di Azure è necessario fornire l'URL del `api-key`servizio e un. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra di spostamento selezionare **servizio di ricerca** per elencare tutti i servizi di ricerca di Azure di cui è stato effettuato il provisioning per la sottoscrizione.
3. Selezionare il servizio che si vuole usare.
4. Nel dashboard del servizio saranno presenti i riquadri per le informazioni essenziali, nonché l'icona della chiave per l'accesso alle chiavi di amministrazione.
   
      ![Screenshot che illustra come accedere alle chiavi di amministrazione dal dashboard del servizio][3]
5. Copiare l'URL del servizio e una chiave di amministrazione. Sarà necessario utilizzarli in seguito, quando vengono aggiunti al file **config.properties** .

## <a name="download-the-sample-files"></a>Scaricare i file di esempio
1. Passare a [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) su GitHub.
2. Selezionare **download zip**, salvare il file con estensione zip su disco e quindi estrarre tutti i file in esso contenuti. È consigliabile estrarre i file nell'area di lavoro Java per trovare più facilmente il progetto in un secondo momento.
3. I file di esempio sono di sola lettura. Fare clic con il pulsante destro del mouse sulle proprietà della cartella e deselezionare l'attributo di sola lettura.

Tutte le successive modifiche e le istruzioni di esecuzione verranno effettuate sui file in questa cartella.  

## <a name="import-project"></a>Importare il progetto
1. In Eclipse selezionare **file** > **Importa** > generaleprogetti > **esistenti nell'area di lavoro**.
   
    ![Screenshot che illustra come importare un progetto esistente][4]
2. In **Select root directory**, passare alla cartella contenente i file di esempio. Selezionare la cartella che contiene la cartella .project. Il progetto verrà visualizzato nell’elenco **Projects** come elemento selezionato.
   
    ![Screenshot che mostra l'elenco progetti nella finestra Importa progetti][12]
3. Selezionare **Fine**.
4. Utilizzare **Project Explorer** per visualizzare e modificare i file. Se non è già aperto, selezionare **finestra** > **Mostra** > **Esplora progetti** o usare il collegamento per aprirlo.

## <a name="configure-the-service-url-and-api-key"></a>Configurare l'URL del servizio e`api-key`
1. In **Esplora progetti**fare doppio clic su **config. Properties** per modificare le impostazioni di configurazione che contengono il nome `api-key`del server e.
2. Fare riferimento ai passaggi descritti in precedenza in questo articolo, in cui è stato trovato `api-key` l'URL del servizio e nella [portale di Azure](https://portal.azure.com), per ottenere i valori che verranno immessi in **config. Properties**.
3. In **config. Properties**sostituire "API Key" con il `api-key` per il servizio. Successivamente, il nome del servizio (il primo componente dell'URL https://servicename.search.windows.net) sostituisce "service name" nello stesso file.
   
    ![Screenshot che illustra come sostituire la chiave API][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configurare gli ambienti di progetto, compilazione e runtime
1. In Eclipse fare clic con il pulsante destro del mouse sul progetto > **Proprietà** > **Facet progetto** in Esplora progetti.
2. Selezionare **Dynamic Web Module**, **Java** e **JavaScript**.
   
    ![Screenshot che illustra come selezionare i facet di progetto per il progetto][6]
3. Selezionare **Applica**.
4. Selezionare **Finestra** > **Preferenze** > **Server** > **Runtime Environments (Ambienti di runtime)**  > **Aggiungi..** .
5. Espandere Apache e selezionare la versione del server Apache Tomcat installata in precedenza. In questo sistema è installata la versione 8.
   
    ![Screenshot che mostra la posizione in cui è possibile selezionare la versione di Apache Tomcat nella finestra ambiente di runtime][7]
6. Nella pagina successiva, specificare la directory di installazione di Tomcat. In un computer Windows, sarà probabilmente C:\Programmi\Microsoft Files\Apache Software Foundation\Tomcat *versione*.
7. Selezionare **Fine**.
8. Selezionare **Finestra** > **Preferenze** > **Java** > **Installed JREs (JRE installati)**  > **Aggiungi**.
9. In **Add JRE** (Aggiungi JRE) selezionare **Standard VM (VM standard)** .
10. Selezionare **Avanti**.
11. In definizione JRE, in Home page di JRE, selezionare **directory**.
12. Passare a **Programmi** > **Java** e selezionare il JDK installato in precedenza. È importante selezionare JDK come JRE.
13. In jres (JRE installati selezionare **JDK**. Il risultato finale dovrebbe essere simile a quello riportato nella schermata seguente.
    
    ![Screenshot che illustra come selezionare JDK come JRE installato][9]
14. Facoltativamente, selezionare **Finestra** > **Web Browser** > **Internet Explorer** per aprire l'applicazione in una finestra del browser esterno. L’utilizzo di un browser esterno offre una migliore esperienza di applicazione Web.
    
    ![Screenshot che illustra come selezionare Internet Explorer come finestra di esplorazione esterna][8]

La configurazione del dispositivo è stata completata. A questo punto, compilare ed eseguire il progetto.

## <a name="build-the-project"></a>Compilare il progetto
1. In Esplora progetti fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Esegui come** > **compilazione Maven** per configurare il progetto.
   
    ![Screenshot che illustra come selezionare la compilazione Maven nella finestra Esplora progetti][10]
2. In modifica configurazione, in obiettivi, immettere "Pulisci installazione" e quindi selezionare **Esegui**.

I messaggi di stato vengono visualizzati nella finestra della console. Un messaggio di compilazione completata indica che il progetto è stato compilato senza errori.

## <a name="run-the-app"></a>Esecuzione dell'app
In questo ultimo passaggio, verrà eseguita l'applicazione in un ambiente di runtime del server locale.

Se ancora non è stato specificato un ambiente di runtime del server in Eclipse, è necessario eseguire innanzitutto tale operazione.

1. In Project Explorer espandere **WebContent**.
2. Fare clic con il pulsante destro del mouse su **Search.jsp** > **Esegui come** > **Esegui come controllo server**. Selezionare il server Apache Tomcat, quindi selezionare **Esegui**.

> [!TIP]
> Se è stata utilizzata un'area di lavoro non predefinita per archiviare il progetto, è necessario modificare la **configurazione di esecuzione** in modo che punti al percorso del progetto per evitare un errore di avvio del server. In Esplora progetti fare clic con il pulsante destro del mouse su **Search.jsp** > **Esegui come** > **Configurazione di esecuzione**. Selezionare il server Apache Tomcat. Selezionare **argomenti**. Selezionare **area di lavoro** o **file System** per impostare la cartella che contiene il progetto.
> 
> 

Quando si esegue l'applicazione viene visualizzata una finestra del browser con una casella di ricerca per l'immissione di termini.

Attendere circa un minuto prima di selezionare **Cerca** per concedere al servizio il tempo necessario per creare e caricare l'indice. Se si verifica un errore HTTP 404, è necessario attendere un po' più a lungo prima di riprovare.

## <a name="search-on-usgs-data"></a>Eseguire ricerche sui dati dei servizi geologici degli Stati Uniti
Il set di dati dei servizi geologici degli Stati Uniti include i dati relativi allo stato del Rhode Island. Se si seleziona **Cerca** in una casella di ricerca vuota, si otterranno le prime 50 voci, che corrisponde all'impostazione predefinita.

L’immissione di un termine di ricerca fornirà al motore di ricerca un elemento con cui continuare. Provare a immettere un nome locale. "Roger Williams" è stato il primo governatore del Rhode Island. Numerosi parchi, edifici e scuole prendono il suo nome.

![Screenshot che illustra come eseguire ricerche nei dati di USGS][11]

È inoltre possibile tentare con uno dei termini seguenti:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Passaggi successivi
Questa è la prima esercitazione di Ricerca di Azure basata su Java e sul set di dati dei servizi geologici degli Stati Uniti. Nel corso del tempo, l’esercitazione sarà ampliata per illustrare le funzionalità di ricerca aggiuntive che potrebbero essere utili nelle soluzioni personalizzate.

Se si dispone già delle nozioni di base di Ricerca di Azure, è possibile usare questo esempio come base per ulteriore sperimentazione, ad esempio ampliando la [pagina di ricerca](search-pagination-page-layout.md) o implementando l'[esplorazione basata su facet](search-faceted-navigation.md). È inoltre possibile migliorare la pagina dei risultati della ricerca aggiungendo conteggi e raggruppando i documenti in modo che gli utenti possano sfogliare i risultati.

Novità in Ricerca di Azure È consigliabile provare altre esercitazioni per acquisire consapevolezza di ciò che è possibile creare. Visitare la [pagina della documentazione](https://azure.microsoft.com/documentation/services/search/) per trovare ulteriori risorse. 

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
