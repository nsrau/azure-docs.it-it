<properties 
    pageTitle="Polling di operazioni con esecuzione prolungata | Microsoft Azure" 
    description="Questo argomento descrive come eseguire il polling di operazioni con esecuzione prolungata." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>



#<a name="delivering-live-streaming-with-azure-media-services"></a>Distribuzione di Live Streaming con Servizi multimediali di Azure

##<a name="overview"></a>Overview

Servizi multimediali di Microsoft Azure offre API che inviano richieste a servizi multimediali per avviare operazioni, ad esempio creazione, avvio, arresto o eliminazione di un canale. Queste operazioni hanno un'esecuzione prolungata.

L'SDK di Servizi multimediali per .NET fornisce le API che inviano la richiesta e attendono il completamento dell'operazione (a livello interno, le API eseguono il polling dell'avanzamento dell'operazione a intervalli definiti). Ad esempio, quando si chiama channel.Start(), il metodo restituisce dopo l'avvio del canale. È possibile anche usare la versione asincrona: await channel.StartAsync(). Per informazioni sul modello asincrono basato su attività, vedere [TAP](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx). Le API che inviano una richiesta di operazione e quindi eseguono il polling dello stato fino al completamento dell'operazione vengono definite "metodi di polling". Tali metodi (in special modo la versione asincrona) sono consigliati per le applicazioni rich client e/o i servizi con stato.

In alcuni scenari è possibile che un'applicazione non possa attendere una richiesta HTTP con esecuzione prolungata e sia necessario eseguire il polling manuale dell'avanzamento dell'operazione. Un esempio tipico può essere un browser che interagisce con un servizio Web senza stato: quando il browser richiede di creare un canale, il servizio Web avvia un'operazione con esecuzione prolungata e restituisce l'ID operazione al browser. Il browser potrebbe quindi chiedere al servizio Web di ottenere lo stato dell'operazione in base all'ID. L'SDK di Servizi multimediali per .NET fornisce API che sono utili per questo scenario, definite "metodi di non polling",
che sono caratterizzati dal seguente criterio di denominazione: Send*NomeOperazione*Operation, ad esempio SendCreateOperation. I metodi Send*NomeOperazione*Operation restituiscono l'oggetto **IOperation**. L'oggetto restituito contiene informazioni che consentono di tenere traccia dell'operazione. I metodi Send*NomeOperazione*OperationAsync restituiscono **Task<IOperation>**.

Attualmente, le classi seguenti supportano i metodi non polling: **Channel**, **StreamingEndpoint** e **Program**.

Per eseguire il polling dello stato delle operazioni, usare il metodo **GetOperation** sulla classe **OperationBaseCollection**. Usare gli intervalli seguenti per controllare lo stato dell'operazione: per le operazioni **Channel** e **StreamingEndpoint**, usare 30 secondi; per le operazioni **Program**, usare 10 secondi.


##<a name="example"></a>Esempio

L'esempio seguente definisce una classe denominata **ChannelOperations**. Questa definizione di classe può essere un punto di partenza per la definizione di classe del servizio Web. Per esigenze di semplicità, negli esempi seguenti vengono usate le versioni non asincrone dei metodi.

Nell'esempio viene inoltre illustrato il modo in cui il client potrebbe usare questa classe.

###<a name="channeloperations-class-definition"></a>Definizione della classe ChannelOperations

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelPreview CreateChannelPreview()
        {
            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###<a name="the-client-code"></a>The client code

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##<a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Oct16_HO2-->


