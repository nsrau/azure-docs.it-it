<properties urlDisplayName="Check Job Progress" pageTitle="Come controllare lo stato dei processi in Servizi multimediali - Azure" metaKeywords="" description="Informazioni su come usare il codice del gestore eventi per tenere traccia dello stato dei processi e inviare aggiornamenti di stato. Negli esempi di codice, scritti in C#, viene usato Media Services SDK per .NET." metaCanonical="" services="media-services" documentationCenter="" title="Procedura: Controllare lo stato dei processi" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Procedura: Controllare lo stato dei processi

Questo articolo fa parte di una serie di articoli dedicati alla programmazione in Servizi multimediali di Azure. L'argomento precedente è [Procedura: Codificare un asset][Procedura: Codificare un asset].

Quando si esegue un processo, spesso è necessario monitorarne l'avanzamento. Nell'esempio di codice seguente viene definito il gestore dell'evento StateChanged, che monitora l'avanzamento del processo e ne indica lo stato aggiornato. Il codice consente inoltre di definire il metodo helper LogJobStop, che registra i dettagli degli errori.

    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);

        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("********************");
                Console.WriteLine("Job is finished.");
                Console.WriteLine("Please wait while local tasks or downloads complete...");
                Console.WriteLine("********************");
                Console.WriteLine();
                Console.WriteLine();
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
            case JobState.Error:
                // Cast sender as a job.
                IJob job = (IJob)sender;
                // Display or log error details as needed.
                LogJobStop(job.Id);
                break;
            default:
                break;
        }
    }

    private static void LogJobStop(string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = GetJob(jobId);

        builder.AppendLine("\nThe job stopped due to cancellation or an error.");
        builder.AppendLine("***************************");
        builder.AppendLine("Job ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job State: " + job.State.ToString());
        builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
        builder.AppendLine("Media Services account name: " + _accountName);
        builder.AppendLine("Media Services account location: " + _accountLocation);
        // Log job errors if they exist.  
        if (job.State == JobState.Error)
        {
            builder.Append("Error Details: \n");
            foreach (ITask task in job.Tasks)
            {
                foreach (ErrorDetail detail in task.ErrorDetails)
                {
                    builder.AppendLine("  Task Id: " + task.Id);
                    builder.AppendLine("    Error Code: " + detail.Code);
                    builder.AppendLine("    Error Message: " + detail.Message + "\n");
                }
            }
        }
        builder.AppendLine("***************************\n");
        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobStop-{JobId}.txt
        string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }

    private static string JobIdAsFileName(string jobID)
    {
        return jobID.Replace(":", "_");
    }

## Passaggi successivi

Dopo avere creato un processo e averne verificato l'avanzamento, il passaggio successivo consiste nel proteggere gli asset. Per altre informazioni, vedere la [procedura per proteggere un asset con Servizi multimediali di Azure][procedura per proteggere un asset con Servizi multimediali di Azure].

  [Procedura: Codificare un asset]: http://go.microsoft.com/fwlink/?LinkID=301753&clcid=0x409
  [procedura per proteggere un asset con Servizi multimediali di Azure]: http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409
