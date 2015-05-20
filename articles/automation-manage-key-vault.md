<properties
	pageTitle="Gestire l'insieme di credenziali chiave usando Automazione di Azure"
	description="Informazioni su come è possibile usare il servizio Automazione di Azure per gestire l'insieme di credenziali chiave di Azure."
	services="Key-Vault, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2015"
	ms.author="csand"/>



#Gestione dell'insieme di credenziali chiave usando Automazione di Azure

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'utilizzo per semplificare la gestione delle chiavi e dei segreti nell'insieme di credenziali chiave di Azure.

## Informazioni su Automazione di Azure

[Automazione di Azure](http://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività manuali, ripetute frequentemente, a esecuzione prolungata e soggette a errori per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione dell'organizzazione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio consente di ridurre i costi operativi e di liberare risorse dello staff IT e DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.


## In che modo è possibile gestire l'insieme di credenziali chiave di Azure con Automazione di Azure?

L'insieme di credenziali chiave può essere gestito in Automazione di Azure usando i [cmdlet per l'insieme di credenziali delle chiavi di Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx) che sono disponibili negli [strumenti di Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). I cmdlet sono predefiniti in Automazione di Azure, per consentire l'esecuzione di tutte le attività di gestione dell'insieme di credenziali chiave dall'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

Con i cmdlet dell'insieme di credenziali chiave di Azure è possibile eseguire le attività di creazione o importazione di una chiave, creazione o aggiornamento di un segreto, aggiornamento degli attributi di una chiave, ottenimento di una chiave o di un segreto o eliminazione di una chiave o di un segreto.


## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e del modo in cui può essere usato per gestire l'insieme di credenziali chiave di Azure, usare i collegamenti seguenti per altre informazioni su Automazione di Azure.

* Vedere l'[esercitazione introduttiva](automation-create-runbook-from-samples.md) di Automazione di Azure.
* Vedere gli [script di PowerShell dell'insieme di credenziali chiave di Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-Key-Vault-Powershell-1349b091).

<!--HONumber=54-->