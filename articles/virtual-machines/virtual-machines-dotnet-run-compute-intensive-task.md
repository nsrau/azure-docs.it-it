<properties
	pageTitle="Eseguire un'app .NET a elevato utilizzo di calcolo in una macchina virtuale | Microsoft Azure"
	description="Informazioni su come distribuire ed eseguire un'app .NET a elevato utilizzo di calcolo in una macchina virtuale di Azure e usare le code del service bus di Azure per monitorare lo stato in remoto."
	services="virtual-machines"
	documentationCenter=".net"
	authors="wadepickett"
	manager="wpickett"
	editor="mollybos"
	tags=“azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/25/2015"
	ms.author="wpickett"/>

# Come eseguire un'attività a elevato utilizzo di calcolo in .NET su una macchina virtuale Azure

Con Azure è possibile usare una macchina virtuale per gestire attività a elevato utilizzo di calcolo. Ad esempio, una macchina virtuale può gestire attività e fornire risultati a computer client o ad applicazioni mobili. Dopo aver completato questa esercitazione, si potrà creare una macchina virtuale su cui si esegue un'applicazione .NET a elevato utilizzo di calcolo che può essere monitorata da un'altra applicazione .NET.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Per questa esercitazione si presuppone che l'utente sia in grado di creare applicazioni console .NET. Non è richiesta alcuna conoscenza di Azure.

Si apprenderà come:

* Creare una macchina virtuale.
* Accedere in remoto alla macchina virtuale.
* Creare uno spazio dei nomi del bus di servizio di Azure.
* Creare un'applicazione .NET che esegua un'attività a elevato utilizzo di calcolo.
* Creare un'applicazione .NET che monitori l'avanzamento dell'attività a elevato utilizzo di calcolo.
* Eseguire le applicazioni .NET.
* Interrompere le applicazioni .NET.

In questa esercitazione verrà utilizzato il Problema del commesso viaggiatore per l'attività a elevato utilizzo di calcolo. Di seguito è riportato un esempio dell'applicazione .NET che esegue l'attività a elevato utilizzo di calcolo.

![Risolutore del Problema del commesso viaggiatore][solver_output]

Di seguito è riportato un esempio dell'applicazione .NET che esegue il monitoraggio dell'attività a elevato utilizzo di calcolo.

![Client del Problema del commesso viaggiatore][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Per creare una macchina virtuale

1. Accedere al [Portale di Azure](https://manage.windowsazure.com).
2. Fare clic su **New**.
3. Fare clic su **Virtual machine**.
4. Fare clic su **Quick Create**.
5. Nella schermata **Create a virtual machine**, immettere un valore per **DNS name**.
6. Nell'elenco a discesa **Immagine** selezionare un'immagine, ad esempio **Windows Server 2012 R2**.
7. Immettere un nome per l'amministratore nel campo **User Name**. Prendere nota di questo nome e della password che verrà immessa successivamente perché verranno utilizzati per l'accesso in remoto alla macchina virtuale.
8. Immettere una password nel campo **Nuova password** e reimmetterla nel campo **Conferma**.
9. Dall'elenco a discesa **Percorso**, selezionare il percorso del data center per la macchina virtuale.
10. Fare clic su **Create virtual machine**. È possibile monitorare lo stato nella sezione **Macchine virtuali** del portale di Azure. Quando viene visualizzato lo stato **Active**, è possibile accedere alla macchina virtuale.

## Per accedere in remoto alla macchina virtuale

1. Accedere al [Portale di Azure](https://manage.windowsazure.com).
2. Fare clic su **Virtual machines**.
3. Fare clic sul nome della macchina virtuale a cui si desidera accedere.
4. Fare clic su **Connect**.
5. Rispondere ai prompt visualizzati per connettersi alla macchina virtuale. Quando vengono richiesti il nome e la password dell'amministratore, usare i valori specificati durante la creazione della macchina virtuale.

## Come creare uno spazio dei nomi del bus di servizio

Per iniziare a usare le code del bus di servizio in Azure, è innanzitutto necessario creare uno spazio dei nomi del servizio che fornisce un contenitore di ambito per fare riferimento alle risorse del bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi servizio:

1.  Accedere al [Portale di Azure](https://manage.windowsazure.com).
2.  Nel pannello di navigazione sinistro del portale di Azure fare clic su **Bus di servizio**.
3.  Nel riquadro inferiore del portale di Azure fare clic su **Crea**.

    ![Creazione di un bus di servizio][create_service_bus]
4.  Nella finestra di dialogo **Crea uno spazio dei nomi** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome, che deve essere univoco.

    ![Create a namespace dialog][create_namespace_dialog]
5.  Dopo avere verificato la disponibilità del nome dello spazio dei nomi, scegliere l'area in cui dovrà essere ospitato. Assicurarsi di usare la stessa area in cui è ospitata la macchina virtuale.

    > [AZURE.IMPORTANT]Selezionare la **stessa area** che si usa o si intende usare per la macchina virtuale. In questo modo sarà possibile ottenere prestazioni ottimali.

6. Se si dispone di più sottoscrizioni di Azure per l'account con cui è stato eseguito l'accesso, selezionare la sottoscrizione da utilizzare per lo spazio dei nomi. Se si dispone di una sola sottoscrizione per l'account con cui è stato eseguito l'accesso, l'elenco a discesa contenente le sottoscrizioni non verrà visualizzato.
7. Fare clic sul segno di spunta. A questo punto, lo spazio dei nomi servizio verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di effettuare il provisioning delle risorse per lo spazio dei nomi creato.

	![Click create screenshot][click_create]

Lo spazio dei nomi creato verrà quindi visualizzato nel portale di Azure e sarà necessario attendere qualche istante affinché venga attivato. Prima di continuare, attendere che lo stato sia **Active**.

## Recupero delle credenziali di gestione predefinite per lo spazio dei nomi

Per poter eseguire le operazioni di gestione, ad esempio creare una coda, nel nuovo spazio dei nomi, è necessario ottenere le credenziali di gestione per lo spazio dei nomi.

1.  Nel riquadro di navigazione sinistro fare clic su **Bus di servizio** per visualizzare l'elenco degli spazi dei nomi disponibili: ![Schermata relativa agli spazi dei nomi disponibili][available_namespaces]
2.  Selezionare lo spazio dei nomi appena creato nell'elenco: ![Schermata relativa all'elenco degli spazi dei nomi][namespace_list]
3. Fare clic su **Informazioni di connessione**. ![Pulsante Access Key][access_key_button]
4.  Nella finestra di dialogo, individuare la voce **Stringa di connessione**. Prendere nota di questo valore in quanto dovrà essere usato in seguito nell’esercitazione per eseguire operazioni con lo spazio dei nomi.

## Come creare un'applicazione .NET che esegua un'attività a elevato utilizzo di calcolo

1. Sul computer di sviluppo (che non deve essere la macchina virtuale creata dall'utente) scaricare [Azure SDK per .NET](http://azure.microsoft.com/develop/net/).
2. Creare un'applicazione console .NET con il progetto denominato TSPSolver. Verificare che il framework di destinazione sia impostato per .**NET Framework 4** o versione successiva (non **.NET Framework 4 Client Profile**). Il framework di destinazione può essere impostato dopo aver creato un progetto nel modo seguente: nel menu di Visual Studio, fare clic su **Progetti**, scegliere **Proprietà**, quindi fare clic sulla scheda **Applicazione** e impostare il valore per **Framework di destinazione**.
3. Aggiungere la libreria Microsoft ServiceBus. In Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse su **TSPSolver**, fare clic su **Aggiungi riferimento**, fare clic sulla scheda **Sfoglia**, passare ad Azure .NET SDK (ad esempio, C:\\Programmi\\Microsoft SDKs\\Azure.NET SDK\\v2.5\\ToolsRef) e selezionare **Microsoft.ServiceBus.dll** come riferimento.
4. Aggiungere la libreria System Runtime Serialization. In Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse su **TSPSolver**, fare clic su **Aggiungi riferimento**, fare clic sulla scheda **.NET** e selezionare **System.Runtime.Serialization** come riferimento.
5. Utilizzare il codice di esempio fornito alla fine di questa sezione per i contenuti del file Program.cs.
6. Modificare il segnaposto **your\_connection\_string** per usare la **stringa di connessione** del bus di servizio.
7. Compilare l'applicazione. Verrà creato TSPSolver.exe nella cartella bin del progetto (bin\\release o bin\\debug, a seconda che la destinazione sia una build di rilascio o di debug). Questo eseguibile e Microsoft.ServiceBus.dll verranno copiati nella macchina virtuale in seguito.

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;

	namespace TSPSolver
	{
	    class Program
	    {
	        // Value specifying how often to provide an update to the console.
	        private static long loopCheck = 100000000;
	        private static long nTimes = 0, nLoops = 0;

	        private static double[,] distances;
	        private static String[] cityNames;
	        private static int[] bestOrder;
	        private static double minDistance;

	        private static NamespaceManager namespaceManager;
	        private static QueueClient queueClient;
	        private static String queueName = "TSPQueue";

	        private static void BuildDistances(String fileLocation, int numCities)
	        {

	            try
	            {
	                StreamReader sr = new StreamReader(fileLocation);
	                String[] sep1 = { ", " };

	                double[,] cityLocs = new double[numCities, 2];

	                for (int i = 0; i < numCities; i++)
	                {
	                    String[] line = sr.ReadLine().Split(sep1, StringSplitOptions.None);
	                    cityNames[i] = line[0];
	                    cityLocs[i, 0] = Convert.ToDouble(line[1]);
	                    cityLocs[i, 1] = Convert.ToDouble(line[2]);
	                }
	                sr.Close();

	                for (int i = 0; i < numCities; i++)
	                {
	                    for (int j = i; j < numCities; j++)
	                    {
	                        distances[i, j] = hypot(Math.Abs(cityLocs[i, 0] - cityLocs[j, 0]), Math.Abs(cityLocs[i, 1] - cityLocs[j, 1]));
	                        distances[j, i] = distances[i, j];
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static double hypot(double x, double y)
	        {
	            return Math.Sqrt(x * x + y * y);
	        }

	        private static void permutation(List<int> startCities, double distSoFar, List<int> restCities)
	        {
	            try
	            {

	                nTimes++;
	                if (nTimes == loopCheck)
	                {
	                    nLoops++;
	                    nTimes = 0;
	                    DateTime dateTime = DateTime.Now;
	                    Console.Write("Current time is {0}.", dateTime);
	                    Console.WriteLine(" Completed {0} iterations of size of {1}.", nLoops, loopCheck);
	                }

	                if ((restCities.Count == 1) && ((minDistance == -1) || (distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 1]] < minDistance)))
	                {
	                    startCities.Add(restCities[0]);
	                    newBestDistance(startCities, distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 2]]);
	                    startCities.Remove(startCities[startCities.Count - 1]);
	                }
	                else
	                {
	                    for (int i = 0; i < restCities.Count; i++)
	                    {
	                        startCities.Add(restCities[0]);
	                        restCities.Remove(restCities[0]);
	                        permutation(startCities, distSoFar + distances[startCities[startCities.Count - 1], startCities[startCities.Count - 2]], restCities);
	                        restCities.Add(startCities[startCities.Count - 1]);
	                        startCities.Remove(startCities[startCities.Count - 1]);
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static void newBestDistance(List<int> cities, double distance)
	        {
	            try
	            {
	                minDistance = distance;
	                String cityList = "Shortest distance is " + minDistance + ", with route: ";

	                for (int i = 0; i < bestOrder.Length; i++)
	                {
	                    bestOrder[i] = cities[i];
	                    cityList += cityNames[bestOrder[i]];
	                    if (i != bestOrder.Length - 1)
	                        cityList += ", ";
	                }
	                Console.WriteLine(cityList);
	                queueClient.Send(new BrokeredMessage(cityList));
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        static void Main(string[] args)
	        {
	            try
	            {

                  String connectionString = @"your_connection_string";

	                int numCities = 10; // Use as the default, if no value is specified
	                // at the command line.
	                if (args.Count() != 0)
	                {

	                    if (args[0].ToLower().CompareTo("createqueue") == 0)
	                    {
	                        // No processing to occur other than creating the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.CreateQueue(queueName);
	                        Console.WriteLine("Queue named {0} was created.", queueName);
	                        Environment.Exit(0);
	                    }

	                    if (args[0].ToLower().CompareTo("deletequeue") == 0)
	                    {
	                        // No processing to occur other than deleting the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.DeleteQueue("TSPQueue");
	                        Console.WriteLine("Queue named {0} was deleted.", queueName);
	                        Environment.Exit(0);
	                    }

	                    // Neither creating or deleting a queue.
	                    // Assume the value passed in is the number of cities to solve.
	                    numCities = Convert.ToInt32(args[0]);
	                }

	                Console.WriteLine("Running for {0} cities.", numCities);

	                queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                List<int> startCities = new List<int>();
	                List<int> restCities = new List<int>();

	                startCities.Add(0);
	                for (int i = 1; i < numCities; i++)
	                {
	                    restCities.Add(i);
	                }
	                distances = new double[numCities, numCities];
	                cityNames = new String[numCities];
	                BuildDistances(@"c:\tsp\cities.txt", numCities);
	                minDistance = -1;
	                bestOrder = new int[numCities];
	                permutation(startCities, 0, restCities);
	                Console.WriteLine("Final solution found!");
	                queueClient.Send(new BrokeredMessage("Complete"));

	                queueClient.Close();
	                Environment.Exit(0);

	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}



## Come creare un'applicazione .NET che monitori l'avanzamento dell'attività a elevato utilizzo di calcolo

1. Sul computer di sviluppo, creare un'applicazione console .NET usando il nome di progetto TSPClient. Verificare che il framework di destinazione sia impostato per .**NET Framework 4** o versione successiva (non **.NET Framework 4 Client Profile**). Il framework di destinazione può essere impostato dopo aver creato un progetto nel modo seguente: nel menu di Visual Studio, fare clic su **Progetti**, scegliere **Proprietà**, quindi fare clic sulla scheda **Applicazione** e impostare il valore per **Framework di destinazione**.
2. Aggiungere la libreria Microsoft ServiceBus. In Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse su **TSPClient**, fare clic su **Aggiungi riferimento**, fare clic sulla scheda **Sfoglia**, passare ad Azure .NET SDK (ad esempio, C:\\Programmi\\Microsoft SDKs\\Azure.NET SDK\\v2.5\\ToolsRef) e selezionare **Microsoft.ServiceBus.dll** come riferimento.
3. Aggiungere la libreria System Runtime Serialization. In Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse su **TSPClient**, fare clic su **Aggiungi riferimento**, quindi fare clic sulla scheda **.NET** e selezionare **System.Runtime.Serialization** come riferimento.
4. Utilizzare il codice di esempio fornito alla fine di questa sezione per i contenuti del file Program.cs.
5. Modificare il segnaposto **your\_connection\_string** per usare la **stringa di connessione** del bus di servizio.
6. Compilare l'applicazione. Verrà creato TSPClient.exe nella cartella bin del progetto (bin\\release o bin\\debug, a seconda che la destinazione sia una build di rilascio o di debug). È possibile eseguire questo codice dal computer di sviluppo oppure copiare questo eseguibile e Microsoft.ServiceBus.dll in un computer sul quale verrà eseguita l'applicazione client (non è necessario che vena eseguita sulla macchina virtuale)

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;
	using System.Threading; // For Thread.Sleep

	namespace TSPClient
	{
	    class Program
	    {

	        static void Main(string[] args)
	        {

	            try
	            {

	                Console.WriteLine("Starting at {0}", DateTime.Now);

									String connectionString = @"your_connection_string";

	                QueueClient queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                BrokeredMessage message;

	                int waitMinutes = 3;  // Use as the default, if no value
	                // is specified at command line.

	                if (0 != args.Length)
	                {
	                    waitMinutes = Convert.ToInt16(args[0]);
	                }

	                String waitString;
	                waitString = (waitMinutes == 1) ? "minute" : waitMinutes.ToString() + " minutes";

	                while (true)
	                {
	                    message = queueClient.Receive();

	                    if (message != null)
	                    {
	                        try
	                        {
	                            string str = message.GetBody<string>();
	                            Console.WriteLine(str);

	                            // Remove message from queue.
	                            message.Complete();

	                            if ("Complete" == str)
	                            {
	                                Console.WriteLine("Finished at {0}.", DateTime.Now);
	                                break;
	                            }
	                        }
	                        catch (Exception e)
	                        {
	                            // Indicates a problem. Unlock the message in the queue.
	                            message.Abandon();
	                            throw e;
	                        }
	                    }
	                    else
	                    {
	                        // The queue is empty.
	                        Console.WriteLine("Queue is empty. Sleeping for another {0}.", waitString);
	                        System.Threading.Thread.Sleep(60000 * waitMinutes);
	                    }
	                }
	                queueClient.Close();
	                Environment.Exit(0);
	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}

## Come eseguire le applicazioni .NET

Eseguire l'applicazione a elevato utilizzo di calcolo innanzitutto per creare la coda, quindi per risolvere il Problema del commesso viaggiatore, che consentirà di aggiungere l'attuale route migliore alla coda del bus di servizio. Mentre l'applicazione a elevato utilizzo di calcolo è in esecuzione (oppure in seguito), eseguire il client in modo da visualizzare i risultati ottenuti dalla coda del bus di servizio.

### Come eseguire l'applicazione a elevato utilizzo di calcolo

1. Accedere alla macchina virtuale.
2. Creare una cartella denominata c:\\TSP. Si tratta della cartella in cui verrà eseguita l'applicazione.
3. Copiare TSPSolver.exe e Microsoft.ServiceBus.dll, entrambi disponibili nella cartella bin del progetto TSPSolver, in c:\\TSP.
4. Creare un file denominato c:\\TSP\\cities.txt con il seguente contenuto.

		City_1, 1002.81, -1841.35
		City_2, -953.55, -229.6
		City_3, -1363.11, -1027.72
		City_4, -1884.47, -1616.16
		City_5, 1603.08, -1030.03
		City_6, -1555.58, 218.58
		City_7, 578.8, -12.87
		City_8, 1350.76, 77.79
		City_9, 293.36, -1820.01
		City_10, 1883.14, 1637.28
		City_11, -1271.41, -1670.5
		City_12, 1475.99, 225.35
		City_13, 1250.78, 379.98
		City_14, 1305.77, 569.75
		City_15, 230.77, 231.58
		City_16, -822.63, -544.68
		City_17, -817.54, -81.92
		City_18, 303.99, -1823.43
		City_19, 239.95, 1007.91
		City_20, -1302.92, 150.39
		City_21, -116.11, 1933.01
		City_22, 382.64, 835.09
		City_23, -580.28, 1040.04
		City_24, 205.55, -264.23
		City_25, -238.81, -576.48
		City_26, -1722.9, -909.65
		City_27, 445.22, 1427.28
		City_28, 513.17, 1828.72
		City_29, 1750.68, -1668.1
		City_30, 1705.09, -309.35
		City_31, -167.34, 1003.76
		City_32, -1162.85, -1674.33
		City_33, 1490.32, 821.04
		City_34, 1208.32, 1523.3
		City_35, 18.04, 1857.11
		City_36, 1852.46, 1647.75
		City_37, -167.44, -336.39
		City_38, 115.4, 0.2
		City_39, -66.96, 917.73
		City_40, 915.96, 474.1
		City_41, 140.03, 725.22
		City_42, -1582.68, 1608.88
		City_43, -567.51, 1253.83
		City_44, 1956.36, 830.92
		City_45, -233.38, 909.93
		City_46, -1750.45, 1940.76
		City_47, 405.81, 421.84
		City_48, 363.68, 768.21
		City_49, -120.3, -463.13
		City_50, 588.51, 679.33

5. Al prompt dei comandi passare alla directory c:\\TSP.
6. Sarà necessario creare la coda del bus di servizio prima di eseguire le permutazioni del risolutore TSP. Eseguire il comando seguente per creare la coda del bus di servizio.

        TSPSolver createqueue

7. Dopo aver creato la coda è possibile eseguire le permutazioni del risolutore TSP. Ad esempio, usare il comando seguente per eseguire il risolutore per 8 città.

        TSPSolver 8

 Se non si specifica un numero, il risolutore verrà eseguito per 10 città. Mano a mano che rileva le route attualmente più brevi, il risolutore le aggiunge alla coda.

Il risolutore eseguirà il comando finché non avrà terminato di esaminare tutte le route.

> [AZURE.NOTE]Maggiore è il numero specificato, più lunga sarà l'esecuzione del risolutore. Ad esempio, l'esecuzione per 14 città potrebbe richiedere diversi minuti, mentre l'esecuzione per 15 città potrebbe richiedere parecchie ore. Se si specificano 16 o più città potrebbero essere necessari diversi giorni di runtime (settimane, mesi e anni). Ciò è dovuto al rapido aumento del numero di permutazioni valutate dal risolutore di pari passo con l'aumento del numero di città.

### Come eseguire l'applicazione client di monitoraggio
1. Accedere al computer dal quale si intende eseguire l'applicazione client, che non deve essere necessariamente lo stesso computer su cui è in esecuzione l'applicazione TSPSolver.
2. Creare una cartella in cui eseguire l'applicazione, ad esempio c:\\TSP.
3. Copiare TSPClient.exe e Microsoft.ServiceBus.dll, entrambi disponibili nella cartella bin del progetto TSPClient, nella cartella c:\\TSP.
4. Al prompt dei comandi passare alla directory c:\\TSP.
5. Eseguire il comando indicato di seguito.

        TSPClient

    Facoltativamente, specificare il numero di minuti di sospensione della verifica della coda passando un argomento della riga di comando. Il periodo di sospensione predefinito per la verifica della coda corrisponde a 3 minuti. Questo intervallo viene utilizzato se non si passa un argomento della riga di comando a TSPClient. Se si desidera usare un valore differente per l'intervallo di sospensione, ad esempio un minuto, eseguire il comando indicato di seguito.

	    TSPClient 1

    Il comando verrà eseguito sul client finché non si riceverà il messaggio di coda completa. Si noti che se si eseguono più occorrenze del risolutore senza eseguire il client, può essere necessario eseguire il client più volte per svuotare completamente la coda. In alternativa, è possibile eliminare la coda e crearla di nuovo. Per eliminare la coda, eseguire il comando TSPSolver (non TSPClient) seguente.

        TSPSolver deletequeue

## Come interrompere le applicazioni .NET

Per uscire dalle applicazioni risolutore e client e terminare prima del normale completamento, premere i tasti Ctrl+C.

## Alternativa alla creazione e all'eliminazione della coda con TSPSolver
Per creare o eliminare la coda, è possibile utilizzare il [portale di Azure](https://manage.windowsazure.com) in sostituzione di TSPSolver. Visitare la sezione relativa al bus di servizio del portale di Azure per accedere alle interfacce utente per la creare o eliminare una coda, nonché per recuperare la stringa di connessione, l'autorità emittente e la chiave di accesso. È inoltre possibile visualizzare un dashboard delle code del bus di servizio, che consente di visualizzare le metriche per i messaggi in ingresso e in uscita.

[solver_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPSolver.png
[client_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPClient.png
[create_service_bus]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ServiceBusCreateNew.png
[create_namespace_dialog]: ./media/virtual-machines-dotnet-run-compute-intensive-task/CreateNameSpaceDialog.png
[available_namespaces]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AvailableNamespaces.png
[click_create]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ClickCreate.png
[namespace_list]: ./media/virtual-machines-dotnet-run-compute-intensive-task/NamespaceList.png
[access_key_button]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AccessKey.png

<!---HONumber=Oct15_HO3-->