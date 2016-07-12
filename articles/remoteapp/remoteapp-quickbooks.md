<properties 
    pageTitle="Distribuire QuickBooks in Azure RemoteApp | Microsoft Azure" 
    description="Informazioni su come condividere QuickBooks con Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/27/2016" 
    ms.author="elizapo" />



# Come distribuire QuickBooks in Azure RemoteApp

Usare le informazioni seguenti per la condivisione di QuickBooks come app in Azure RemoteApp.


È possibile condividere QuickBooks 2015 Enterprise con Azure RemoteApp in una raccolta ibrida o cloud. Il file della società deve trovarsi in una macchina virtuale che esegue il server di database QuickBooks separato dai server di Azure RemoteApp. Non memorizzare mai il file della società nell'immagine di Azure RemoteApp. In caso contrario, potrebbe verificarsi la perdita dei dati. Solo QuickBooks Enterprise supporta l'hosting del file QuickBooks in una condivisione esterna con il server di database QuickBooks accessibile tramite rete standard di Windows.

> [AZURE.IMPORTANT] Il server di database QuickBooks che ospita il file della società deve risiedere in una macchina virtuale separata nell'ambito della stessa rete virtuale come raccolta di Azure RemoteApp.

## Passaggi per distribuire QuickBooks

1. Creare una macchina virtuale di Azure e installare QuickBooks e il server di database QuickBooks, quindi inserire il file della società in una macchina virtuale di Azure. Assicurarsi di configurare correttamente le regole del firewall.
2. Installare QuickBooks in un'[immagine personalizzata](remoteapp-imageoptions.md) e creare una [raccolta di Azure RemoteApp](remoteapp-collections.md), cloud o ibrida, esattamente nella stessa rete virtuale in cui risiede la macchina virtuale che ospita il server di database QuickBooks con i file della società.
3.	Rendere [pubblica](remoteapp-publish.md) l'app QuickBooks agli utenti.
4.	Avviare il client QuickBooks ospitato in Azure RemoteApp, passare tramite reti Windows standard alla macchina virtuale che ospita il server di database QuickBooks e aprire il file della società.

## Riferimenti di documentazione

- [Configurazioni supportate](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top) di QuickBooks
- [Opzioni di distribuzione](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/) di QuickBooks

È inoltre possibile consultare la presentazione Ignite relativa all'[amministrazione e alla gestione di Microsoft Azure RemoteApp](https://channel9.msdn.com/Events/Ignite/2015/BRK3868). Andare al minuto 1:02:45 per la parte su QuickBooks.

## Architettura di distribuzione

![QuickBooks + distribuzione di Azure RemoteApp](./media/remoteapp-quickbooks/ra-quickbooks.png)

<!---HONumber=AcomDC_0629_2016-->