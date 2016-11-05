---
title: 'Piattaforme di analisi: confronto tra Apache Storm e Analisi di flusso | Microsoft Docs'
description: Informazioni utili per scegliere una piattaforma di analisi cloud tramite confronto tra Apache Storm e Analisi di flusso. Comprendere le funzionalità e le differenze.
keywords: piattaforma di analisi, piattaforme di analisi, piattaforma di analisi cloud, confronto con Storm
services: stream-analytics
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2016
ms.author: jeffstok

---
# <a name="help-choosing-a-streaming-analytics-platform:-apache-storm-comparison-to-azure-stream-analytics"></a>Informazioni utili per scegliere una piattaforma di analisi di flusso: confronto tra Apache Storm e Analisi di flusso di Azure
Informazioni utili per scegliere una piattaforma di analisi cloud tramite confronto tra Apache Storm e Analisi di flusso di Azure. Comprendere le proposte di valore di Analisi di flusso e Apache Storm come servizio gestito in Azure HDInsight, per poter scegliere la soluzione ideale per i casi d'uso aziendali.

Entrambe le piattaforme di analisi offrono i vantaggi di una soluzione PaaS, tuttavia si differenziano per alcune importanti funzionalità specifiche. Di seguito sono riportate sia le funzionalità che le limitazioni di questi servizi, per facilitare la scelta della soluzione più adatta per il raggiungimento dei propri obiettivi.

## <a name="storm-comparison-to-stream-analytics:-general-features"></a>Confronto tra Storm e Analisi di flusso: caratteristiche generali
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analisi di flusso di Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Open Source</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
No, Analisi di flusso di Azure è un'offerta proprietaria Microsoft.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sì, Apache Storm è una tecnologia concessa in licenza da Apache.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Supportato da Microsoft</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sì </p>
            </td>
            <td width="246" valign="top">
                <p>
Sì </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Requisiti hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Non sono previsti requisiti hardware. Analisi di flusso di Azure è un servizio di Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Non sono previsti requisiti hardware. Apache Storm è un servizio di Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unità di livello superiore</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Con Analisi di flusso di Azure i clienti distribuiscono e monitorano i processi di streaming.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Con Apache Storm in HDInsight i clienti distribuiscono e monitorano un intero cluster, che può ospitare più processi di Storm nonché altri carichi di lavoro (incluso il batch).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prezzo</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Il prezzo di Analisi di flusso viene determinato dal volume dei dati elaborati e dal numero di unità di streaming (per ogni ora di esecuzione del processo) necessarie.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">Altre informazioni sui prezzi sono disponibili qui.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Per Apache Storm in HDInsight, l'unità di acquisto è basata su cluster e l'addebito avviene in base al tempo di esecuzione del cluster, indipendentemente dai processi distribuiti.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">Altre informazioni sui prezzi sono disponibili qui.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Creazione nelle due piattaforme di analisi ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analisi di flusso di Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funzionalità: DSL SQL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sì, è disponibile un semplice supporto facile da usare per il linguaggio SQL.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
No, gli utenti devono scrivere il codice in Java C# o usare le API Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funzionalità: operatori temporali</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sono supportati i join temporali e le aggregazioni finestra predefiniti.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Gli operatori temporali devono essere implementati dall'utente.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Esperienza di sviluppo</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Esperienza di creazione e debug interattiva tramite il portale di Azure sui dati di esempio.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
L'esperienza di sviluppo, debug e monitoraggio passa attraverso Visual Studio per gli utenti .NET, mentre gli sviluppatori Java e di altri linguaggi possono usare l'IDE preferito.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Supporto per il debug</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Analisi di flusso consente di eseguire il debug tramite lo stato dei processi di base e i log operazioni, ma attualmente non è possibile stabilire in dettaglio cosa/quanto includere nei log.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sono disponibili log dettagliati a scopo di debug. Per visualizzare i log, l'utente può usare visual Studio oppure può accedervi effettuando una connessione RDP al cluster.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Supporto per le funzioni definite dall'utente (UDF)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Attualmente non è previsto alcun supporto per le funzioni UDF.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Le funzioni UDF possono essere scritte in C#, in Java o in un altro linguaggio.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Codice personalizzato estendibile</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Non è previsto alcun supporto per il codice estendibile in Analisi di flusso.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sì, è possibile scrivere codice personalizzato in C#, Java o altri linguaggi supportati in Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Origini dati e output ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analisi di flusso di Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Origini dati di input</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Le origini di input supportate sono Hub eventi di Azure e BLOB di Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sono disponibili connettori per Hub eventi, Bus di servizio, Kafka e così via. I connettori non supportati possono essere implementati tramite codice personalizzato.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formati di dati di input</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
I formati di input supportati sono Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualsiasi formato può essere implementato tramite codice personalizzato.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Output</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Un processo di streaming potrebbe avere più output. Output supportati: Hub eventi di Azure, Archivio BLOB di Azure, Tabelle di Azure, Azure SQL DB e PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Supporto per più output in una topologia. Ogni output può avere una logica personalizzata per l'elaborazione downstream. Storm include connettori predefiniti per PowerBI, Hub eventi di Azure, Archivio Blob di Azure, Azure DocumentDB, SQL e HBase. I connettori non supportati possono essere implementati tramite codice personalizzato.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formati di codifica dati</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Analisi di flusso richiede di utilizzare il formato dati UTF-8.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualsiasi formato di codifica dati può essere implementato tramite codice personalizzato.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Gestione e operazioni ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analisi di flusso di Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modello di distribuzione dei processi</strong>
                </p>
                <p>
                    - <strong>Portale di Azure</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
La distribuzione viene implementata tramite il portale di Azure, PowerShell e le API REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
La distribuzione viene implementata tramite il portale di Azure, PowerShell, Visual Studio e le API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitoraggio (statistiche, contatori e così via)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Il monitoraggio viene implementato tramite il portale di Azure e le API REST.
                </p>
                <p>
L'utente può inoltre configurare gli avvisi di Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Il monitoraggio viene implementato tramite l'interfaccia utente di Storm e le API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Scalabilità</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Numero di unità di streaming per ogni processo. Ogni unità di streaming elabora fino a 1 MB/s. Numero massimo di 50 unità per impostazione predefinita. Chiamare per aumentare il limite.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Numero di nodi nel cluster di Storm in HDI. Nessun limite al numero di nodi. Il limite massimo è definito dalla quota di Azure. Chiamare per aumentare il limite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limiti di elaborazione dei dati</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Gli utenti possono aumentare o ridurre il numero di unità di streaming per migliorare l'elaborazione dei dati o ottimizzare i costi.
                </p>
                <p>
Aumento fino a 1 GB/s </p>
            </td>
            <td width="246" valign="top">
                <p>
L'utente può aumentare o ridurre le dimensioni del cluster in base alle proprie esigenze.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Arresto/ripresa</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Arrestare e riprendere dall'ultimo punto di arresto.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Arrestare e riprendere dall'ultimo punto di arresto in base al limite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Aggiornamento del servizio e del framework</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Applicazione di patch automatica senza tempi di inattività.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Applicazione di patch automatica senza tempi di inattività.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuità aziendale grazie a un servizio a disponibilità elevata con contratti di servizio garantiti</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Tempo di attività per il contratto di servizio del 99,9% </p>
                <p>
Ripristino automatico in caso di errori </p>
                <p>
Ripristino predefinito degli operatori temporali con stato.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Tempo di attività per il contratto di servizio del 99,9% del cluster Storm. Apache Storm è una piattaforma di streaming a tolleranza di errore, ma è responsabilità del cliente verificare che i processi di streaming vengano eseguiti senza interruzioni.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Funzionalità avanzate ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analisi di flusso di Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Gestione di eventi di arrivo in ritardo e con ordine non corretto</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Criteri configurabili predefiniti per riordinare ed eliminare gli eventi o modificare l'ora degli eventi.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
L'utente deve implementare la logica per gestire questo scenario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Dati di riferimento</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dati di riferimento disponibili nei BLOB di Azure con dimensione massima di 100 MB di cache di ricerca in memoria. L'aggiornamento dei dati di riferimento viene gestito dal servizio.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nessun limite alle dimensioni dei dati. Connettori disponibili per HBase, DocumentDB, SQL Server e Azure. I connettori non supportati possono essere implementati tramite codice personalizzato.
                </p>
                <p>
L'aggiornamento dei dati di riferimento deve essere gestito dal codice personalizzato.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integrazione con Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Configurando i modelli di Azure Machine Learning pubblicati come funzioni durante la creazione di processi ASA <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(anteprima privata)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponibile tramite i bolt di Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>



<!--HONumber=Oct16_HO2-->


