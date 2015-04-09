<properties
	pageTitle="Informazioni di riferimento per l'esplorazione del portale di Azure"
	description="Apprendere le diverse esperienze utente per il servizio per app Web tra il portale di gestione e il portale di Azure"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="jaime-espinosa"/>

# Informazioni di riferimento per l'esplorazione del portale di Azure

Siti Web di Azure è ora denominato [servizio app per app Web](http://go.microsoft.com/fwlink/?LinkId=529714). È in corso l'aggiornamento di tutta la documentazione relativa per riflettere il cambio di denominazione e fornire istruzioni per il nuovo portale di Azure. Fino al completamento di tale processo, è possibile usare questo documento come guida per lavorare con le app Web nel nuovo portale di Azure.
 
## Il futuro del portale precedente

Sebbene le modifiche al marchio siano visibili anche nel portale precedente, è in corso la sostituzione di quest'ultimo con il nuovo portale di Azure. Il portale precedente verrà eventualmente eliminato e tutte le nuove attività correlate allo sviluppo verranno spostate e proseguiranno nel nuovo portale. Tutte le nuove funzionalità per app Web in arrivo verranno ospitate nel nuovo portale. Iniziare subito a usare il nuovo portale per sfruttare i vantaggi offerti dalle ultime e migliori app Web.

## Differenze del layout tra il portale precedente e il nuovo portale di Azure

Nel portale precedente tutti i servizi di Azure sono elencati nella parte sinistra. La navigazione nel portale precedente segue una struttura ad albero, in cui si inizia dal servizio e si naviga all'interno di ogni elemento. Questa struttura funziona bene quando si gestiscono componenti indipendenti. Le applicazioni create in Azure, tuttavia, sono una raccolta di servizi interconnessi e questa struttura ad albero non risulta ideale per lavorare con raccolte di servizi. 

Il nuovo portale semplifica la creazione di applicazioni end-to-end con componenti provenienti da più servizi. Il nuovo portale di Azure è organizzato in *journeys*. Un *journey* è una serie di *blades* (pannelli), ovvero contenitori per i diversi componenti. La configurazione della scalabilità automatica per un'app Web, ad esempio, è un *journey* che ospita diversi pannelli, come illustrato nell'esempio seguente: il pannello del **sito Web** (con il titolo non ancora aggiornato alla nuova terminologia), il pannello **Impostazioni** e il pannello **Scalabilità**. In questo esempio la scalabilità automatica viene configurata secondo l'utilizzo della CPU ed è pertanto presente anche un pannello **Percentuale CPU**. I componenti all'interno dei *blades* (pannelli) sono denominati *parts* (parti) e assomigliano a titoli. 

![](./media/app-service-web-app-preview-portal/AutoScaling.png)

## Esempio di navigazione: creare un'app Web

La creazione di app Web resta un'operazione semplicissima. La figura seguente mostra il portale precedente e il nuovo portale affiancati e dimostra che ben poco è cambiato quanto al numero di passaggi necessario per creare un'app Web e renderla operativa. 

![](./media/app-service-web-app-preview-portal/CreateWebApp.png)

Nel nuovo portale è possibile scegliere tra i tipi di app Web più comuni, comprese applicazioni popolari da raccolte come WordPress. Per un elenco completo delle applicazioni disponibili, visitare [Azure Marketplace].

Per creare un'app Web si specificano l'URL, il piano di servizio app e l'area nel nuovo portale nello stesso modo in cui lo si faceva nel portale precedente. 

![](./media/app-service-web-app-preview-portal/CreateWebAppSettings.png)

Nel nuovo portale è inoltre possibile definire altre impostazioni comuni. Ad esempio i [gruppi di risorse](azure-preview-portal-using-resource-groups.md), semplificano la visualizzazione e la gestione delle risorse di Azure correlate. 

## Esempio di navigazione: impostazioni e funzionalità

Tutte le impostazioni e funzionalità sono ora raggruppate logicamente in un unico riquadro, da cui è possibile avviare una navigazione.

![](./media/app-service-web-app-preview-portal/WebAppSettings.png)

Ad esempio, è possibile creare domini personalizzati facendo clic su **Domini personalizzati e SSL SSL** nel riquadro **Impostazioni**.

![](./media/app-service-web-app-preview-portal/ConfigureWebApp.png)

Per impostare una notifica di monitoraggio, fare clic su **Richieste ed errori** e quindi su **Aggiungi avviso**.

![](./media/app-service-web-app-preview-portal/Monitoring.png)

Per abilitare la diagnostica, fare clic su **Log di diagnostica** nel riquadro **Impostazioni**.

![](./media/app-service-web-app-preview-portal/Diagnostics.png)
 
Per configurare le impostazioni dell'applicazione, fare clic su **Impostazioni applicazione** nel riquadro **Impostazioni**. 

![](./media/app-service-web-app-preview-portal/AppSettingsPreview.png)

Oltre al nome del marchio, alcuni altri elementi nel portale sono stati rinominati o raggruppati in modo diverso per semplificarne l'individuazione. Ad esempio, quello riportato di seguito è un ritaglio di schermata della pagina corrispondente a quella delle impostazioni dell'applicazione (**Configura**) nel portale precedente.

![](./media/app-service-web-app-preview-portal/AppSettings.png)

## Altre risorse

[Piattaforma applicativa cloud di Azure](app-service-cloud-app-platform.md)

[Nuovo portale]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE] Se si vuole iniziare a usare il servizio app di Azure prima di iscriversi per ottenere un account Azure, andare alla [pagina di prova del servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app Web di base temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Novità
* Per informazioni su cosa è cambiato nel passaggio da Siti Web al servizio app, vedere: [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per informazioni su cosa è cambiato nel passaggio dal portale precedente a quello nuovo, vedere: [Informazioni di riferimento per la navigazione nel nuovo portale](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->