 
<properties
	pageTitle="Gestione di Azure Backup utilizzando l'automazione di Azure" 
	description="Informazioni sull'utilizzo del servizio di automazione di Azure per gestire i Backup di Azure."
	services="backup, automation"
	documentationCenter=""
	authors="eamonoreilly"
	manager="jwinter"
	editor=""/>
 
<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2015"
	ms.author="eamono"/>
  
#Gestione di Azure Backup utilizzando l'automazione di Azure
 
In questa Guida verrà presentate per il servizio di automazione di Azure e come può essere utilizzato per semplificare la gestione di Azure Backup.
 
## Informazioni su Automazione di Azure
 
[Automazione di Azure](http://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività a esecuzione prolungata, manuali, soggette a errori e ripetute frequentemente per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione.
 
Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali in base alla crescita dell'organizzazione. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.
 
Il servizio permette di ridurre i costi operativi e di liberare risorse dello staff IT/DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.
 
 
## Come automazione di Azure consentono di gestire Backup di Azure?
 
Backup possono essere gestiti in automazione di Azure utilizzando i cmdlet di PowerShell sono disponibili nel [modulo Windows MSOnlineBackup](https://technet.microsoft.com/en-us/library/hh770400.aspx). Automazione di Azure può chiamare questi cmdlet di PowerShell in modo che è possibile eseguire tutte le attività di gestione di Backup all'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.
 
 
## Passaggi successivi
 
Dopo aver appreso le nozioni di base di automazione di Azure e come può essere utilizzato per gestire i Backup di Azure, utilizzare i collegamenti seguenti per ulteriori informazioni sull'automazione di Azure.
 
* Estrarre l'automazione di Azure [Guida introduttiva](http://go.microsoft.com/fwlink/?LinkId=390560)

<!---HONumber=GIT-SubDir--> 