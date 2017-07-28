---
title: 'Piattaforme di analisi: confronto tra Apache Storm e Analisi di flusso | Microsoft Docs'
description: "Informazioni utili per scegliere una piattaforma di analisi cloud tramite confronto tra Apache Storm e Analisi di flusso. Comprendere le funzionalità e le differenze."
keywords: piattaforma di analisi, piattaforme di analisi, piattaforma di analisi cloud, confronto con Storm
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 4c0c7c185943eb292d816e2047de930245a3e1e1
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>Scegliere una piattaforma di analisi di flusso: confronto tra Apache Storm e Analisi di flusso di Azure
Azure offre diverse soluzioni per l'analisi dei flussi di dati: [Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/) e [Apache Storm in Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-storm/). Entrambe le piattaforme di analisi offrono i vantaggi di una soluzione PaaS. Tuttavia, le piattaforme presentano differenze significative nelle loro funzionalità, nonché nel modo in cui vengono configurate e gestite. 

Questo articolo fornisce un confronto delle funzionalità che consente di scegliere tra Apache Storm e Analisi di flusso di Azure come piattaforma di analisi cloud. 

## <a name="general-features"></a>Funzionalità generali

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
                    <strong>Open source?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
No. Analisi di flusso di Azure è un'offerta proprietaria Microsoft.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sì. Apache Storm è una tecnologia concessa in licenza da Apache.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Supporto tecnico Microsoft?</strong>
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
Nessuno. Analisi di flusso di Azure è un servizio di Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nessuno. Apache Storm è un servizio di Azure.
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
Gli utenti distribuiscono e monitorano i processi di streaming.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Gli utenti distribuiscono e monitorano un intero cluster, che può ospitare più processi di Storm nonché altri carichi di lavoro (incluso il batch).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prezzi</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Il prezzo viene determinato dal volume dei dati elaborati e dal numero di unità di streaming necessarie per ogni ora di esecuzione del processo. 
                </p>
                    <p>Per altre informazioni, vedere <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Prezzi di Analisi di flusso</a>.</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
L'unità di acquisto è basata su cluster e l'addebito avviene in base al tempo di esecuzione del cluster, indipendentemente dai processi distribuiti.
                </p>
                <p>
Per altre informazioni, vedere <a href="http://azure.microsoft.com/pricing/details/hdinsight/">Prezzi di HDInsight</a>.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>Creazione

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
                    <strong>Funzionalità: DSL SQL?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sì. Analisi di flusso di Azure fornisce un linguaggio simile a SQL per la creazione di trasformazioni.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
No. Gli utenti devono scrivere il codice in Java o C# o usare le API Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funzionalità: operatori temporali?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Per impostazione predefinita sono supportati i join temporali e le aggregazioni finestra.
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
Gli utenti possono creare, eseguire il debug e monitorare i processi tramite il portale di Azure usando i dati di esempio derivati da Live Stream.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Gli utenti che utilizzano .NET possono sviluppare, eseguire il debug e monitorare tramite Visual Studio. Gli utenti che utilizzano Java o altri linguaggi possono usare l'IDE preferito.
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
Lo stato dei processi di base e i log delle operazioni sono disponibili per facilitare il debug. Analisi di flusso di Azure attualmente non consente agli utenti di specificare il tipo o la quantità di contenuto inclusi nei log (ad esempio, la modalità dettagliata).
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sono disponibili log dettagliati. Gli utenti possono accedere ai log in Visual Studio o accedendo al cluster e accedendo direttamente ai log.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Supporto per funzioni definite dall'utente (UDF)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Le query supportano UDF JavaScript. Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">Integrazione UDF di JavaScript</a>.
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
                    <strong>Estensibilità tramite codice personalizzato?</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
No. Non è previsto alcun supporto per il codice estendibile in Analisi di flusso.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sì. Gli utenti possono scrivere codice personalizzato in C#, Java o qualsiasi altro linguaggio supportato in Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>Origini dati (input) e output ##

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
                <p>Hub eventi di Azure e Archiviazione BLOB di Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
I connettori sono disponibili per l'Hub eventi di Azure, Bus di servizio di Microsoft Azure, Kafka e altro ancora. Gli utenti possono creare connettori aggiuntivi con codice personalizzato.
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
Avro, JSON, CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
Gli utenti possono implementare tutti i formati utilizzando codice personalizzato.
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
Un processo di streaming può avere più output. Output supportati: Hub eventi di Azure, Archiviazione BLOB di Azure, Archiviazione tabelle di Azure, Azure SQL DB e Power BI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm supporta più output in una topologia e ogni output può avere una logica personalizzata per l'elaborazione downstream. Storm include connettori predefiniti per Power BI, Hub eventi di Azure, Archiviazione BLOB di Azure, Azure Cosmos DB, SQL e HBase. Gli utenti possono creare connettori aggiuntivi con codice personalizzato.    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formati di codifica dei dati</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dati devono essere formattati usando UTF-8.
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
Gli utenti possono implementare tutti i formati di codifica dei dati usando codice personalizzato.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Gestione e operazioni ##

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
            </td>
            <td width="204" valign="top">
                <p>
Portale di Azure, PowerShell e API REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Portale di Azure, PowerShell, Visual Studio e API REST.
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
Il monitoraggio viene implementato tramite il portale di Azure e le API REST. Gli utenti possono inoltre configurare gli avvisi di Azure.
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
La scalabilità è determinata dal numero di unità di streaming (SUs) per ogni processo. Ogni unità di streaming elabora fino a 1 MB al secondo, con un massimo di 50 unità. Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">Ridimensionare i processi di Analisi di flusso di Azure per aumentare la velocità effettiva dell'elaborazione dei flussi di dati</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
La scalabilità è determinata dal numero di nodi del cluster HDInsight Storm. Il limite superiore al numero di nodi è definito dalla quota di Azure dell'utente.
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
Gli utenti possono aumentare l'elaborazione dati o ottimizzare i costi aumentando o diminuendo il numero di unità di streaming, con un limite superiore di 1 GB al secondo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Gli utenti possono scalare le dimensioni cluster per aumentarle o diminuirle.
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
                <ul>
                <li>Tempo di attività per il contratto di servizio del 99,9%</li>
                <li>Ripristino automatico in caso di errori</li>
                <li>Ripristino predefinito degli operatori temporali con stato</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
Tempo di attività per il contratto di servizio del 99,9% del cluster Storm. 
                </p>
                <p>
Apache Storm è una piattaforma di streaming a tolleranza di errore. Tuttavia, è responsabilità dell'utente assicurare che i processi di streaming vengano eseguiti senza interruzioni.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Funzionalità avanzate ##

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
Criteri configurabili predefiniti per riordinare e rilasciare gli eventi o modificare l'ora degli eventi.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Gli utenti devono deve implementare la logica per gestire questo scenario.
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
Dati di riferimento sono disponibili dall'archiviazione BLOB di Azure con un massimo di 100 MB di cache in memoria. I dati di riferimento vengono aggiornati dal servizio.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nessun limite alle dimensioni dei dati. I connettori sono disponibili per HBase, Azure Cosmos DB, SQL Server e Azure. Gli utenti possono creare connettori aggiuntivi con codice personalizzato. I dati di riferimento devono essere aggiornati con un codice personalizzato.
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
I modelli di Azure Machine Learning pubblicati possono essere configurati come funzioni durante la creazione dei processi. Per altre informazioni, vedere <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">Scalabilità per le funzioni di Machine Learning</a>.
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

