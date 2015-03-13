<properties 
	pageTitle="Data factory di Azure - Domande frequenti" 
	description="Domande frequenti su Data factory di Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Data factory di Azure - Domande frequenti

**D: Che cos'è Data factory di Azure?**

Data factory è un servizio completamente gestito per gli sviluppatori per comporre i servizi di archiviazione, trasferimento ed elaborazione dei dati in pipeline di dati a disponibilità elevata e a tolleranza di errore. Data factory funziona tramite archiviazione dei dati locale e nel cloud. Una pipeline è un set di input di dati, le attività di elaborazione e output di dati e viene definita con semplici script JSON e attivata tramite comandi di PowerShell. Una volta attivato, Data factory organizza e pianifica l'esecuzione delle pipeline in HDInsight (Hadoop) con opzioni per la gestione automatica dei cluster per conto dell'utente. Data factory consente anche la gestione visiva e il monitoraggio attraverso il portale di anteprima di Azure, per monitorare tutte le pipeline con informazioni complete su operatività e integrità del servizio in un unico dashboard.
 
**D: Quali esigenze del cliente soddisfa Data factory?**

Data factory di Azure bilancia la possibilità di sfruttare archiviazioni dati diversificate, servizi di elaborazione e trasferimento attraverso tradizionali risorse di archiviazione relazionali parallelamente ai dati non strutturati con le funzionalità di controllo e monitoraggio di un servizio completamente gestito.

**D: A chi si rivolge Data factory?**


- Agli sviluppatori di dati, che sono responsabili della creazione di servizi di integrazione tra Hadoop e altri sistemi:
	- Devono stare al passo con un panorama in continua evoluzione e favorire l'integrazione
	- Devono scrivere codice personalizzato per la produzione di informazioni, costoso e di difficile manutenzione, non ad elevata disponibilità né a tolleranza di errore

- Ai professionisti IT, che mirano a integrare dati sempre più diversificati nella propria infrastruttura IT:
	- Devono analizzare tutti i dati aziendali per definire una visione completa dell'attività
	- Devono gestire le risorse di calcolo e archiviazione per il bilanciamento dei costi e la scalabilità in locale e nel cloud
	- Devono aggiungere rapidamente diverse origini ed elaborazioni per soddisfare nuove esigenze aziendali, pur mantenendo la visibilità in tutte le risorse di calcolo e di archiviazione

**D: Dove posso trovare informazioni dettagliate sui prezzi di Data factory di Azure?**

Per saperne di più, vedere la [pagina relativa ai prezzi di Data factory][adf-pricing-details].  

**D: Come posso iniziare a usare Data factory di Azure?**

- Per una panoramica di Data factory di Azure, vedere [Introduzione a Data factory di Azure][adf-introduction].
- Per una rapida esercitazione, vedere [Introduzione a Data factory di Azure][adfgetstarted].
- Per una documentazione completa, vedere la [documentazione di Data factory di Azure][adf-documentation-landingpage].
 
**D: Quali sono le origini dati e le attività supportate?**

- **Origini dati supportate:** Archiviazione di Azure (BLOB e tabelle), SQL Server, Database SQL di Azure. 
- **Attività supportate:**: Attività di copia (da locale al cloud e dal cloud a locale), attività HDInsight (trasformazioni Pig e Hive) e attività C# personalizzate.
  
**D: In che modo i clienti accedono a Data factory?**

I clienti possono accedere a Data factory tramite il [portale di anteprima di Azure][azure-preview-portal].

**D: In quali paesi è disponibile Data factory?**

In anteprima pubblica, Data factory è disponibile solo negli Stati Uniti occidentali.  I servizi di calcolo e di archiviazione usati dalle istanze di Data factory possono essere disponibili in altri paesi.
 
**D: Quali sono i limiti sul numero di Data factory/pipeline/attività/set di dati?** 


- Numero di Data factory all'interno di una sottoscrizione: 50
- Numero di pipeline all'interno di un'istanza di Data factory: 100
- Numero di attività all'interno di una pipeline: 10
- Numero di set di dati all'interno di un'istanza di Data factory: 100

**D: Quali aree sono supportate dall'attività di copia?**

L'attività di copia supporta la copia dei dati nelle aree seguenti: Stati Uniti orientali 2, Stati Uniti occidentali, Europa settentrionale, Europa occidentale e Asia sud-orientale.

In altre regioni è supportata anche la copia di dati, usando una delle cinque aree sopra citate per il routing dei dati.  Il consumo dell'operazione di copia viene misurato in base all'area geografica attraverso la quale i dati vengono reindirizzati.

Area geografica di destinazione della copia | Area geografica usata per il routing
-------------------------- | -----------------------
Stati Uniti orientali | Stati Uniti orientali 2
Stati Uniti centrali | Stati Uniti centrali 2
Stati Uniti centro-settentrionali | Stati Uniti orientali 2
Stati Uniti centro-meridionali | Stati Uniti occidentali
Asia orientale | Asia sud-orientale
Giappone orientale | Stati Uniti occidentali
Giappone occidentale | Stati Uniti occidentali
Brasile meridionale | Stati Uniti orientali 2

**D: Quando si usa un cluster HDInsight, quali sono le aree supportate da HDInsight?**

Vedere la sezione Disponibilità a livello geografico nell'articolo [Dettagli prezzi - HDInsight][hdinsight-supported-regions].

**D: Quale area geografica viene usata per un cluster HDInsight su richiesta?**

Il cluster HDInsight su richiesta viene creato nella stessa area geografica in cui è presente l'archivio specificato per l'utilizzo con il cluster.    

## Vedere anche
[Introduzione a Data factory di Azure][adf-introduction]
[Introduzione a Data factory di Azure][adfgetstarted]
[Guida alla risoluzione dei problemi di Data factory][adf-troubleshoot]


[adfgetstarted]: ../data-factory-get-started
[adf-introduction]: ../data-factory-introduction
[adf-troubleshoot]: ../data-factory-troubleshoot
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/

<!--HONumber=35.2-->

<!--HONumber=46--> 
