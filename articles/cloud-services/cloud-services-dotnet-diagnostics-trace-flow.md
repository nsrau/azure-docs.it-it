---
title: Tracciare il flusso in un'applicazione di Servizi cloud con Diagnostica di Azure | Documentazione Microsoft
description: Aggiungere messaggi di traccia a un'applicazione Azure per consentire operazioni di debug, misurazione delle prestazioni, monitoraggio, analisi del traffico e molto altro.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: jeconnoc
ms.openlocfilehash: f597bc760a3f3825416912642ee66a53dfb91696
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60336865"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Tracciare il flusso in un'applicazione di Servizi cloud con Diagnostica di Azure
Tracciare è una delle azioni a cui è possibile ricorrere per monitorare l'esecuzione di un'applicazione mentre è attiva. È possibile usare le classi [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) e [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) per registrare informazioni sull'esecuzione dell'applicazione ed eventuali errori in file di log, file di testo o altri dispositivi per un'analisi successiva. Per altre informazioni sulle funzionalità di traccia, vedere l'articolo sulle modalità per [tracciare e instrumentare applicazioni](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Usare istruzioni e opzioni di traccia
Per implementare funzionalità di traccia in un'applicazione di Servizi cloud, è possibile aggiungere [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) alla configurazione dell'applicazione ed effettuare chiamate a System.Diagnostics.Trace o System.Diagnostics.Debug nel codice dell'applicazione. Usare il file di configurazione *app.config* per i ruoli di lavoro e *web.config* per i ruoli Web. Quando si crea un nuovo servizio ospitato usando un modello di Visual Studio, Diagnostica di Azure viene automaticamente aggiunto al progetto e DiagnosticMonitorTraceListener viene aggiunto al file di configurazione appropriato per i ruoli aggiunti.

Per informazioni sul posizionamento delle istruzioni di traccia, vedere [come: Aggiungere istruzioni di traccia al codice dell'applicazione](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Inserendo [opzioni di traccia](/dotnet/framework/debug-trace-profile/trace-switches) nel codice, è possibile controllare se la traccia viene eseguita e con quale copertura. In questo modo, è possibile monitorare lo stato dell'applicazione in un ambiente di produzione. Questo aspetto è particolarmente importante in un'applicazione aziendale che usa più componenti in esecuzione su più computer. Per ulteriori informazioni, consultare [Come Configurare opzioni di traccia](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurare il listener di traccia in un'applicazione Azure
Quando si usano le classi Traccia, Debug e TraceSource, è necessario impostare dei "listener" per raccogliere e registrare i messaggi inviati. I listener raccolgono, archiviano e indirizzano i messaggi di traccia, quindi indirizzano l'output di traccia su una destinazione appropriata, ad esempio un log, una finestra o un file di testo. Diagnostica di Azure usa la classe [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)).

Prima di completare la procedura seguente, è necessario inizializzare il monitor di diagnostica di Azure. A tale scopo, vedere [Abilitazione di Diagnostica in Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Se si usano i modelli disponibili in Visual Studio, la configurazione del listener viene aggiunta automaticamente.

### <a name="add-a-trace-listener"></a>Aggiungere un listener di traccia
1. Aprire il file web.config o app.config, in base al ruolo selezionato.
2. Aggiungere il codice seguente al file. Modificare l'attributo Version impostando il numero di versione dell'assembly a cui si fa riferimento. La versione dell'assembly non cambia necessariamente con ogni versione di Azure SDK a meno che non vengano resi disponibili aggiornamenti.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Accertarsi di avere un riferimento progetto all'assembly Microsoft.WindowsAzure.Diagnostics. Aggiornare il numero di versione nel file xml precedente in base alla versione dell'assembly di riferimento Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Salvare il file di configurazione.

Per altre informazioni sui listener, vedere l'articolo sui [listener di traccia](/dotnet/framework/debug-trace-profile/trace-listeners).

Dopo aver completato i passaggi necessari per aggiungere il listener, è possibile aggiungere istruzioni di traccia al codice.

### <a name="to-add-trace-statement-to-your-code"></a>Per aggiungere un'istruzione di traccia al codice
1. Aprire un file di origine per l'applicazione, Ad esempio, il \<RoleName > file con estensione cs per il ruolo web o il ruolo di lavoro.
2. Aggiungere l'istruzione using seguente, se non è già presente:
    ```
        using System.Diagnostics;
    ```
3. Aggiungere istruzioni di traccia nei punti in cui si desidera acquisire informazioni sullo stato dell'applicazione. È possibile usare vari metodi per formattare l'output dell'istruzione di traccia. Per ulteriori informazioni, consultare [Come Aggiungere istruzioni di traccia al codice dell'applicazione](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Salvare il file di origine.

