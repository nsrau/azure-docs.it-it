<properties 
   pageTitle="Configurazioni supportate per Operational Insights"
   description="Informazioni sulle configurazioni necessarie per Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" /> <tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/30/2015"
   ms.author="banders" />

# Configurazioni supportate per Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../includes/operational-insights-note-moms.md)]

Cosa occorre per usare Operational Insights? Leggere le informazioni seguenti per prepararsi all'uso di Operational Insights.


## Configurazione per System Center 2012 Operations Manager

È possibile usare Operational Insights come servizio collegato in Operations Manager in System Center 2012 R2 o System Center 2012 SP1 R2. Questo consente di usare la console operatore in Operations Manager per visualizzare gli avvisi e le informazioni di configurazione di Operational Insights. Quando si usa Operational Insights come servizio collegato in Operations Manager, l'agente comunica direttamente con il server di gestione, che a sua volta comunica con il servizio Operational Insights.

L'uso di Operational Insights come servizio collegato prevede i prerequisiti seguenti:


- L'integrazione tra System Center 2012 SP1 Operations Manager e Operational Insights richiede Management Pack aggiornati, inclusi in [Operational Insights Connector per Operations Manager](https://www.microsoft.com/it-it/download/details.aspx?id=38199). I Management Pack possono essere scaricati e installati da [Operational Insights Connector per Operations Manager](https://www.microsoft.com/it-it/download/details.aspx?id=38199).

- System Center 2012 SP1: aggiornamento cumulativo 6 di Operations Manager, anche se è preferibile l'aggiornamento cumulativo 7. Questo aggiornamento deve essere applicato al server di gestione, agli agenti e alla console operatore per lo scenario di Operational Insights come servizio collegato.

- System Center 2012 R2: aggiornamento cumulativo 2 di Operations Manager, anche se è preferibile l'aggiornamento cumulativo 3. Questo aggiornamento deve essere applicato al server di gestione, agli agenti e alla console operatore per lo scenario di Operational Insights come servizio collegato.

- Per visualizzare i dati relativi alla gestione della capacità, è necessario abilitare la connettività di Operations Manager con Virtual Machine Manager (VMM). Per informazioni aggiuntive sulla connessione tra i sistemi, vedere [Modalità di connessione di VMM con Operations Manager](https://technet.microsoft.com/it-it/library/hh882396.aspx).

Per le istruzioni sull'installazione e la configurazione, vedere [Visualizzazione degli avvisi di Operational Insights](http://go.microsoft.com/fwlink/?LinkID=293793).

Per visualizzare gli avvisi di Operational Insights relativi a SharePoint Server 2010, Lync Server 2013, Lync Server 2010 o System Center 2012 SP1 - Virtual Machine Manager è necessario configurare un account RunAs per questi carichi di lavoro. Vedere le informazioni seguenti:


- [Impostare l'account RunAs per SharePoint](operational-insights-run-as.md)

- [Impostare l'account RunAs per Lync Server](operational-insights-run-as.md)

- [Impostare l'account RunAs per Virtual Machine Manager (VMM)](operational-insights-run-as.md)

### Sistemi operativi di Operations Manager

Gli agenti di Operations Manager sono supportati in diversi computer. Per informazioni dettagliate sul supporto degli agenti, vedere l'articolo relativo ai [requisti di sistema: System Center 2012 R2 Operations Manager](https://technet.microsoft.com/library/dn249696.aspx).

### Requisiti software per Operations Manager

Per visualizzare i dati di gestione della capacità, è necessario abilitare la connettività Operations Manager con VMM. Per informazioni aggiuntive sulla connessione tra i sistemi, vedere [Modalità di connessione di VMM con Operations Manager](https://technet.microsoft.com/it-it/library/hh882396.aspx).

## Agenti con connessione diretta a Operational Insights

L'agente usato per la connessione diretta al servizio è Microsoft Monitoring Agent. I requisiti di sistema sono elencati nella pagina dell'[Area download Microsoft](https://www.microsoft.com/it-it/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True).

## Browser

Con Operational Insights è possibile usare uno qualsiasi dei browser seguenti:

- Windows Internet Explorer 11, Internet Explorer 10, Internet Explorer 9, Internet Explorer 8 o Internet Explorer 7

- Mozilla Firefox 3.5 o versione successiva

Indipendentemente dal browser usato, è necessario installare anche Microsoft Silverlight 4.

## Tecnologie che è possibile analizzare

Operational Insights Configuration Assessment analizza i carichi di lavoro seguenti:

- Windows Server 2012 e Microsoft Hyper-V Server 2012

- Windows Server 2008 e Windows Server 2008 R2, inclusi:
    - Active Directory
	- Host Hyper-V
	- Sistema operativo generale

- SQL Server 2012, SQL Server 2008 R2, SQL Server 2008
    - Motore di database di SQL Server

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 e Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 e Lync Server 2010

- System Center 2012 SP1 – Virtual Machine Manager

Per SQL Server sono supportate per l'analisi le edizioni seguenti a 32 bit e a 64 bit:

- SQL Server 2008 e 2008 R2 Enterprise

- SQL Server 2008 e 2008 R2 Standard

- SQL Server 2008 e 2008 R2 Workgroup

- SQL Server 2008 e 2008 R2 Web

- SQL Server 2008 e 2008 R2 Express

È inoltre supportata l'edizione a 32 bit di SQL Server eseguita nell'implementazione WOW64.


<!--HONumber=54-->