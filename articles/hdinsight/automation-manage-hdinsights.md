<properties
	pageTitle="Gestire Azure HDInsight usando Automazione di Azure"
	description="Informazioni su come è possibile usare il servizio Automazione di Azure per gestire Azure HDInsight."
	services="HDInsight, automation"
	documentationCenter=""
	authors="elcooper"
	manager="eamono"
	editor=""/>

<tags
	ms.service="HDInsight"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="elcooper"/>



#Gestione di Azure HDInsight usando Automazione di Azure
Questa guida costituisce un'introduzione al servizio Automazione di Azure e illustra come usare questo componente per semplificare la gestione di cluster, nonché automatizzare attività comuni in Azure HDInsight.

## Informazioni su Automazione di Azure
[Automazione di Azure](http://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud e del centro dati tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute frequentemente, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie alla pianificazione delle attività di gestione del cloud da eseguire automaticamente in Automazione di Azure.


## In che modo Automazione di Azure può rappresentare un aiuto nella gestione di Azure HDInsight?

È possibile gestire HDInsight in Automazione di Azure usando i [cmdlet di Azure HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx) disponibili negli [strumenti di Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). I cmdlet sono predefiniti in Automazione di Azure, per consentire l'esecuzione di tutte le attività di gestione di HDInsight all'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

I cmdlet di Azure HDInsight consentono di automatizzare attività quali il provisioning di cluster HDInsight in Linux o Windows, ridimensionamento di cluster, gestione di cluster e invio di processi MapReduce. Queste sono solo alcune delle numerose attività che è possibile automatizzare con PowerShell in Automazione di Azure.


## Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e come può essere usato per gestire Azure HDInsight, consultare il collegamento seguente per altre informazioni su Automazione di Azure.

* Vedere l'[esercitazione introduttiva](../automation-create-runbook-from-samples.md) di Automazione di Azure.
* Visualizzare gli esempi nello [Script Center](http://aka.ms/scriptcentergallery).  

 

<!---HONumber=Sept15_HO2-->