---
title: "Esercitazione: Creare un'app Entity Linking - C#"
titlesuffix: Azure Cognitive Services
description: Analizzare il testo e collegare entità denominate a voci pertinenti in una Knowledge Base tramite l'API Entity Linking.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: c3e7d27fa0e62c1dcc92e7eac7ddb56a1de5ba49
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857814"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>Esercitazione: Creare un'app Entity Linking con C#

Microsoft Entity Linking è uno strumento di elaborazione del linguaggio naturale per analizzare il testo e collegare entità denominate a voci pertinenti in una Knowledge Base. 

Questa esercitazione illustra il collegamento di entità tramite la libreria client Entity Linking come pacchetto NuGet. 

### <a name="Prerequisites">Prerequisiti</a>

- Visual Studio 2015
- Chiave API di Servizi cognitivi Microsoft
- Scaricare la libreria client e l'esempio
- Pacchetto NuGet di Microsoft Entity Linking

È possibile scaricare la libreria client dell'API Servizio di intelligence Entity Linking tramite [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). Il file ZIP scaricato deve essere estratto in una cartella a propria scelta. Molti utenti scelgono la cartella di Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Passaggio 1: Iscriversi a Servizio di intelligence Entity Linking e ottenere la chiave</a>
Prima di usare Servizio di intelligence Entity Linking, è necessario iscriversi per ottenere una chiave API. Vedere [Sottoscrizioni](https://www.microsoft.com/cognitive-services/en-us/sign-up). In questa esercitazione è possibile usare sia la chiave primaria sia quella secondaria.

### <a name="step-2-create-a-new-project-in-visual-studio"> Passaggio 2: Creare un nuovo progetto in Visual Studio</a>

Per iniziare, creare un nuovo progetto in Visual Studio. Innanzitutto, avviare Visual Studio 2015 dal menu Start. Creare quindi un nuovo progetto selezionando **Installati → Modelli → Visual C# → Universale di Windows → App vuota** per il modello di progetto:

 ![Creare un'app universale](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Passaggio 3: Aggiungere il pacchetto NuGet di Entity Linking al progetto</a>

Entity Linking di Servizi cognitivi è rilasciato come pacchetto NuGet.org e deve essere installato prima di poterlo usare.
Per aggiungerlo al progetto, passare alla scheda **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.

Nella finestra **Gestione pacchetti NuGet** selezionare prima di tutto NuGet.org come **Origine dei pacchetti** nell'angolo in alto a destra. Selezionare **Sfoglia** nell'angolo in alto a sinistra e nella casella di ricerca digitare "ProjectOxford.EntityLinking". Selezionare il pacchetto NuGet **Microsoft.ProjectOxford.EntityLinking** e fare clic su **Installa**.

Cercare quindi Newtonsoft.Json e installare. Se viene chiesto di rivedere le modifiche, fare clic su **OK**. Se vengono visualizzate le condizioni di licenza di EntityLinking, fare clic su **I Accept** (Accetto).

Entity Linking è ora installato come parte dell'applicazione. È possibile verificarlo controllando che sia presente il riferimento ** Microsoft.ProjectOxford.EntityLinking** come parte del progetto in Esplora soluzioni.

 ![Libreria di NuGet inclusa nel progetto](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Passaggio 4: Aggiungere blocco di testo di input e di output nel codice XAML dell'app</a>
Passare a ** MainPage.xaml ** in **Esplora soluzioni** e quindi fare doppio clic sul file che verrà aperto in una nuova finestra. Per praticità, è possibile fare doppio clic sul pulsante **XAML** nella scheda **Progettazione**. In questo modo verrà nascosta la **finestra di progettazione grafica** riservando tutto lo spazio alla visualizzazione del codice.

 ![Libreria di NuGet inclusa nel progetto](./Images/UWPMainPage.png)
 
In quanto servizio di testo, il modo migliore per visualizzare la funzionalità consiste nel creare un blocco di testo di input e di output. A tale scopo, aggiungere il codice XAML seguente in **Grid**. Questo codice aggiunge tre componenti, una casella di testo di input, un blocco di testo di output e un pulsante di avvio.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Passaggio 5: Procedere con l'aggiunta di Servizio di intelligence Entity Linking</a>
 
L'interfaccia utente è ora creata. Prima di usare il servizio Entity Linking, è necessario aggiungere il gestore button-Click. Aprire **MainPage.xaml** da **Esplora soluzioni**. Aggiungere un gestore button-Click alla fine del pulsante.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
Un gestore button-Click deve essere implementato nel codice. Aprire **MainPage.xaml.cs** da **Esplora soluzioni** per implementare il gestore button-Click. EntityLinkingServiceClient è un wrapper per recuperare le risposte di Entity Linking. L'argomento del costruttore di EntityLinkingServiceClient è la chiave di sottoscrizione di Servizi cognitivi. Incollare la chiave di sottoscrizione ottenuta nel **passaggio 1** per chiamare il servizio Entity Linking. 

Il codice di esempio seguente aggiunge "wikipediaId" nella risposta tramite il servizio Entity Linking. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Si è ora pronti per eseguire la prima app Entity Linking per l'elaborazione del linguaggio naturale. Premere il **tasto F5** per compilare e avviare l'applicazione. Incollare frammenti e paragrafi di testo nella casella di input. Fare clic sul pulsante "Get Results" (Ottieni risultati) e osservare le entità identificate nel blocco di output.
 
 ![Risultato di esempio UWP](./Images/DemoCodeResult.png)
 
### <a name="summary">Summary</a>
 
In questa esercitazione si è appreso come creare un'applicazione per sfruttare la libreria client di Servizio di intelligence Entity Linking con poche righe di codice C# e XAML. 

