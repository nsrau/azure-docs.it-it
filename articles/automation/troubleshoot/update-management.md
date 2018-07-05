---
title: Risolvere gli errori con Gestione aggiornamenti
description: Informazioni su come risolvere i problemi con Gestione aggiornamenti
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064602"
---
# <a name="troubleshooting-issues-with-update-management"></a>Risoluzione dei problemi con Gestione aggiornamenti

Questo articolo illustra alcune procedure per la risoluzione dei problemi che si possono riscontrare quando si usa Gestione aggiornamenti

## <a name="windows"></a>Windows

Se si riscontrano problemi durante il tentativo di caricare la soluzione o una macchina virtuale, nel computer locale cercare nel log eventi di **Operations Manager**, in **Registri applicazioni e servizi**, gli eventi con ID **4502** e il messaggio di evento contenente **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

La sezione seguente evidenzia i messaggi di errore specifici e una possibile risoluzione per ognuno. Per altri problemi di onboarding, vedere la sezione relativa alla [risoluzione dei problemi di onboarding delle soluzioni](onboarding.md).

### <a name="machine-already-registered"></a>Scenario: Il computer è già registrato per un altro account

#### <a name="issue"></a>Problema

Viene visualizzato il messaggio di errore seguente:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Causa

Il computer è già caricato in un'altra area di lavoro per Gestione aggiornamenti.

#### <a name="resolution"></a>Risoluzione

Eseguire la pulizia degli elementi obsoleti nel computer [eliminando il gruppo di runbook ibridi](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) e riprovare.

### <a name="machine-unable-to-communicate"></a>Scenario: Il computer non è in grado di comunicare con il servizio

#### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Causa

Potrebbe trattarsi di un proxy, un gateway o un firewall che blocca la comunicazione di rete.

#### <a name="resolution"></a>Risoluzione

Esaminare le funzionalità di rete e verificare che le porte e gli indirizzi appropriati siano consentiti. Vedere i [requisiti di rete](../automation-hybrid-runbook-worker.md#network-planning) per un elenco di porte e indirizzi richiesti da Gestione aggiornamenti e i ruoli di lavoro ibrido per runbook.

### <a name="unable-to-create-selfsigned-cert"></a>Scenario: Impossibile creare un certificato autofirmato

#### <a name="issue"></a>Problema

Viene visualizzato uno dei seguenti messaggi di errore:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido per runbook non è stato in grado di generare un certificato autofirmato

#### <a name="resolution"></a>Risoluzione

Verificare che l'account di sistema abbia accesso in lettura alla cartella **C:\ProgramData\Microsoft\Crypto\RSA** e riprovare.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenario: Impossibile avviare le operazioni di aggiornamento

#### <a name="issue"></a>Problema

Le operazioni di aggiornamento non vengono avviate in un computer Linux.

#### <a name="cause"></a>Causa

Il ruolo di lavoro ibrido Linux non è integro.

#### <a name="resolution"></a>Risoluzione

Creare una copia del file di log seguente e conservarlo per la risoluzione dei problemi:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenario: L'operazione di aggiornamento viene avviata, ma si verificano errori

#### <a name="issue"></a>Problema

Un'operazione di aggiornamento viene avviata, ma rileva errori durante l'esecuzione.

#### <a name="cause"></a>Causa

Le possibili cause sono:

* La gestione pacchetti non è integra
* Pacchetti specifici possono interferire con l'applicazione di patch basata su cloud
* Altri motivi

#### <a name="resolution"></a>Risoluzione

Se si verificano errori durante un'operazione di aggiornamento dopo un normale avvio in Linux, controllare l'output del processo del computer interessato. È possibile trovare messaggi di errore specifici generati dalla gestione pacchetti del computer che possono essere esaminati per intervenire di conseguenza. Gestione aggiornamenti richiede che la gestione pacchetti sia integra affinché le distribuzioni degli aggiornamenti abbiano esito positivo.

In alcuni casi gli aggiornamenti dei pacchetti possono interferire con Gestione aggiornamenti e impedire il completamento della distribuzione di un aggiornamento. Se si riscontra questo problema, è necessario escludere questi pacchetti dalle future operazioni di aggiornamento oppure installarli manualmente.

Se non è possibile risolvere un problema di patch, creare una copia del seguente file di log e conservarla **prima** dell'inizio della successiva distribuzione degli aggiornamenti ai fini della risoluzione dei problemi:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Passaggi successivi

Se si non riscontra il problema o non si è in grado di risolverlo, visitare uno dei seguenti canali per ricevere assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.