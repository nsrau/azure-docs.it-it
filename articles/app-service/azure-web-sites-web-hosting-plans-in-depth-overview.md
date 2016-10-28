<properties
	pageTitle="Panoramica approfondita dei piani di servizio app di Azure | Microsoft Azure"
	description="Informazioni sui piani di servizio app per Azure App Service e sui vantaggi offerti all'esperienza di gestione."
	keywords="servizio app, servizio app di azure, scala, scalabile, piano di servizio app, costo del servizio app"
	services="app-service"
	documentationCenter=""
	authors="btardif"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/07/2016"
	ms.author="byvinyal"/>

# Panoramica approfondita dei piani di servizio app di Azure#

Un piano di servizio app rappresenta un set di funzionalità e capacità che è possibile condividere tra più app, tra cui app Web, app per dispositivi mobili, app per la logica o app per le API, nel [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). I piani supportano cinque piani tariffari: *Gratuito*, *Condiviso*, *Basic*, *Standard* e *Premium*. Ogni piano tariffario prevede una capacità e funzionalità specifiche. Le app di una stessa sottoscrizione e posizione geografica possono condividere un piano. Tutte le app che condividono un piano possono usare tutte le funzionalità e le caratteristiche offerte dal relativo livello. Tutte le app associate a un piano vengono eseguite sulle risorse definite dal piano.

Se il piano è configurato per usare due istanze "piccole" nel livello di servizio Standard, ad esempio, tutte le app associate al piano verranno eseguite in entrambe le istanze e avranno accesso alle funzionalità previste dal livello di servizio Standard. Le istanze del piano in cui vengono eseguite le app sono completamente gestite e a disponibilità elevata.

Questo articolo illustra le principali caratteristiche, come livello e scalabilità, di un piano di servizio app e il modo in cui queste caratteristiche influiscono sulla gestione delle app.

## App e piani di servizio app

Un'app in un servizio app può essere associata a un solo piano per volta.

App e piani sono inclusi in un gruppo di risorse. Un gruppo di risorse funge da confine del ciclo di vita per ogni risorsa al suo interno. È possibile usare i gruppi di risorse per gestire contemporaneamente tutte le parti di un'applicazione.

Dato che un unico gruppo di risorse può includere più piani di servizio app, è possibile allocare app diverse a risorse fisiche diverse. Si possono ad esempio separare le risorse tra ambienti di sviluppo, test e produzione. Ciò potrebbe essere utile in uno scenario in cui si desidera allocare un piano con il relativo set di risorse dedicato alle app di produzione e un secondo piano agli ambienti di sviluppo e test. In questo modo, l'esecuzione di un test di carico su una nuova versione delle app non userà le stesse risorse delle app di produzione, che devono rispondere alle richieste dei clienti reali.

Quando in un unico gruppo di risorse sono inclusi più piani, è anche possibile definire un'applicazione che copre più aree geografiche. Un'app a disponibilità elevata in esecuzione in due aree includerà, ad esempio, due piani, uno per ogni area, e un'app associata a ogni piano. In una situazione di questo tipo, tutte le copie dell'app saranno associate a un unico gruppo di risorse. Una singola visualizzazione di un gruppo di risorse con più piani e più app semplifica la gestione, il controllo e la visualizzazione dell'integrità dell'applicazione.

## Confronto tra creazione di un nuovo piano di servizio app e uso di un piano esistente

Quando si crea una nuova app, è consigliabile prendere in considerazione la creazione di un nuovo gruppo di risorse. D'altra parte, se l'app che si sta per creare è un componente di un'applicazione più grande, l'app deve essere creata nel gruppo di risorse allocato per tale applicazione più grande.

Sia che la nuova app sia completamente nuova o faccia parte di un'applicazione più grande, si può scegliere di usare un piano di servizio app esistente per l'hosting o di crearne uno nuovo. Questa scelta dipende principalmente dalla capacità e dal carico previsto.

Se la nuova app userà molte risorse e prevede fattori di scalabilità diversi rispetto alle altre app ospitate in un piano esistente, è consigliabile isolarla in un piano specifico.

Quando si crea un nuovo piano, è possibile allocare un nuovo set di risorse per l'app e ottenere maggiore controllo sull'allocazione delle risorse perché ogni piano ha un set di istanze specifico.

La possibilità di spostare le app tra i piani consente di modificare la modalità di allocazione delle risorse nell'applicazione più grande.

Infine, se si desidera creare una nuova app in un'area diversa, in cui non è presente un piano, è necessario creare un nuovo piano nell'area per potervi ospitare l'app.

## Creare un piano di servizio app

È possibile creare un piano di servizio app vuoto dalla funzionalità di esplorazione dei piani di servizio app o durante la creazione di un'app.

Nel [portale di Azure](https://portal.azure.com) fare clic su **Nuovo**, su **Web e dispositivi mobili** e quindi su **App Web**, **App per dispositivi mobili**, **App per le API** o **App per la logica**. ![Creare un'app nel portale di Azure.][createWebApp]

È quindi possibile selezionare o creare il piano di servizio app per la nuova app.

 ![Creare un piano di servizio app.][createASP]

Per creare un nuovo piano di servizio app, fare clic su **[+] Crea nuovo**, digitare il nome in **Piano di servizio app** e selezionare una **Località** appropriata. Fare clic su **Piano tariffario** e quindi selezionare un piano tariffario appropriato per il servizio. Selezionare **Visualizza tutto** per visualizzare più opzioni sui prezzi, ad esempio **Gratuito** e **Condiviso**. Dopo aver scelto il piano tariffario, fare clic sul pulsante **Seleziona**.

## Spostare un'app in un piano di servizio app diverso

È possibile spostare un'app in un piano di servizio app diverso nel [portale di Azure](https://portal.azure.com). Le app possono essere spostate tra i piani solo se i piani si trovano nello stesso gruppo di risorse e nella stessa area geografica.

Per spostare un'app in un altro piano, passare all'app che si vuole spostare. Nel menu **Impostazioni** fare clic su **Cambia il piano di servizio app**.

Verrà aperto il pannello di selezione **Piano di servizio app**. A questo punto, è possibile scegliere un piano esistente o crearne uno nuovo. Vengono visualizzati solo i piani validi, nello stesso gruppo di risorse e nella stessa località geografica.

![Pannello di selezione Piano di servizio app.][change]

Si noti che è previsto un piano tariffario diverso per ogni piano. Quando si sposta un sito dal livello gratuito al livello Standard, l'app può usare tutte le funzionalità e le risorse del livello Standard.

## Clonare un'app in un piano di servizio app diverso
Se si vuole spostare l'app in un'area diversa, in alternativa consiste nel clonarla. Con la clonazione si crea una copia dell'app in un piano di servizio app nuovo o esistente o nell'ambiente del servizio app di qualsiasi area.

 ![Clonare un'app.][appclone]

È possibile trovare **Clona app** nel menu **Strumenti**.

La clonazione presenta alcune limitazioni, illustrate nell'articolo [Clonazione di app del servizio app di Azure con il portale di Azure](../app-service-web/app-service-web-app-cloning-portal.md).

## Scalare un piano di servizio app

Sono disponibili tre modi per ridimensionare un piano:

- **Modificare il piano tariffario del piano**. Un piano con livello Basic, ad esempio, può essere convertito in un livello Standard o Premium. Tutte le app associate al piano potranno usare le funzionalità offerte dal nuovo livello di servizio.
- **Modificare le dimensioni delle istanze del piano**. Un piano con livello Basic che usa istanze piccole, ad esempio, può essere modificato per usare istanze grandi. Tutte le app associate al piano potranno usare le risorse di memoria e CPU aggiuntive offerte dalle maggiori dimensioni delle istanze.
- **Modificare il numero di istanze del piano**. Un piano Standard con tre istanze, ad esempio, può essere aumentato fino a 10 istanze. Un piano Premium può essere aumentato fino a 20 istanze (a seconda della disponibilità). Tutte le app associate al piano potranno usare le risorse di memoria e CPU aggiuntive offerte dal numero superiore di istanze.

È possibile modificare il piano tariffario e le dimensioni delle istanze facendo clic sulla voce **Aumenta prestazioni** nelle impostazioni dell'app o del piano di servizio app. Le modifiche verranno applicate al piano di servizio app e interesseranno tutte le app ospitate.

 ![Impostare i valori per aumentare le prestazioni di un'app.][pricingtier]

## Riepilogo

I piani di servizio app rappresentano un set di funzionalità e capacità che è possibile condividere tra le app. I piani di servizio app garantiscono la possibilità di allocare app specifiche a un set di risorse e ottimizzare ulteriormente l'utilizzo delle risorse di Azure. In questo modo, se si vuole risparmiare denaro sull'ambiente di test è possibile condividere un piano tra più app. È anche possibile ottimizzare la velocità effettiva nell'ambiente di produzione scalandolo in più aree e piani.

## Modifiche apportate

* Per una guida relativa al passaggio da Siti Web al servizio App, vedere [Servizio app di Azure e i servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png

<!---HONumber=AcomDC_0817_2016-->