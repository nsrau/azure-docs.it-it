---
title: Monitoraggio dell'integrità dei file nel Centro sicurezza di Azure
description: Informazioni su come confrontare le linee di base con Il monitoraggio dell'integrità dei file nel Centro sicurezza di Azure.Learn how to compare baselines with File Integrity Monitoring in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664401"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Confrontare le baseline con il monitoraggio dell'integrità dei file

Il monitoraggio dell'integrità dei file (FIM) informa l'utente quando si verificano modifiche alle aree sensibili delle risorse, in modo da poter analizzare e risolvere le attività non autorizzate. FIM monitora i file di Windows, i registri di Windows e i file Linux.

In questo argomento viene illustrato come abilitare FIM nei file e nei registri. Per altre informazioni su FIM, vedere [Monitoraggio dell'integrità dei](security-center-file-integrity-monitoring.md)file nel Centro sicurezza di Azure.For more information about FIM, see File Integrity Monitoring in Azure Security Center.

## <a name="why-use-fim"></a>Perché utilizzare FIM?

Il sistema operativo, le applicazioni e le configurazioni associate controllano il comportamento e lo stato di sicurezza delle risorse. Di conseguenza, gli utenti malintenzionati prendono di mira i file che controllano le risorse, al fine di superare il sistema operativo di una risorsa e/o eseguire attività senza essere rilevati.

Infatti, molti standard di conformità alle normative come PCI-DSS & ISO 17799 richiedono l'implementazione dei controlli FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Abilitare i controlli del Registro di sistema ricorsivi incorporati

Le impostazioni predefinite dell'hive del Registro di sistema FIM forniscono un modo pratico per monitorare le modifiche ricorsive all'interno delle aree di sicurezza comuni.  Ad esempio, un avversario può configurare uno script da eseguire in LOCAL_SYSTEM contesto configurando un'esecuzione all'avvio o all'arresto.  Per monitorare modifiche di questo tipo, attivare il controllo incorporato.  

![Registro](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> I controlli ricorsivi si applicano solo agli hive di sicurezza consigliati e non ai percorsi del Registro di sistema personalizzati.  

## <a name="adding-a-custom-registry-check"></a>Aggiunta di un controllo del Registro di sistema personalizzato

Le linee di base FIM iniziano identificando le caratteristiche di uno stato noto per il sistema operativo e l'applicazione di supporto.  Per questo esempio, ci concentreremo sulle configurazioni dei criteri password per Windows Server 2008 e versioni successive.


|Nome criterio                 | Impostazione del Registro di sistema|
|---------------------------------------|-------------|
|Controller di dominio: rifiuta cambi password account computer| MACHINE , System , CurrentControlSet , Servizi , Netlogon , Parametri , RefusePasswordChange|
|Membro di dominio: aggiunta crittografia o firma digitale ai dati del canale sicuro (sempre)|MACHINE, System, CurrentControlSet, Servizi, Netlogon, Parametri, RequireSignOrSeal|
|Membro di dominio: aggiunta crittografia o firma digitale ai dati del canale sicuro (quando possibile)|MACHINE, System, CurrentControlSet, Servizi, Netlogon, Parametri, Canale di protezione remoto|
|Membro di dominio: aggiunta firma digitale ai dati del canale sicuro (quando possibile)|MACHINE, System, CurrentControlSet, Servizi, Netlogon, Parametri, SignSecureChannel|
|Controller di dominio: disabilita cambi password account computer|MACHINE , System , CurrentControlSet , Servizi , Netlogon , Parametri , DisablePasswordChange|
|Controller di dominio: validità massima password account computer|MACHINE, System, CurrentControlSet, Servizi, Netlogon, Parametri, MaximumPasswordAge|
|Membro di dominio: richiedi chiave di sessione avanzata (Windows 2000 o versioni successive)|MACHINE|
|Sicurezza di rete: Limitare NTLM: autenticazione NTLM in questo dominioNetwork security: Restrict NTLM: NTLM authentication in this domain|MACHINE, System, CurrentControlSet, Servizi, Netlogon, Parametri, RestrictNTLMInDomain|
|Sicurezza di rete: limitazione di NTLM: aggiungi eccezioni dei server nel dominio|MACHINE, System, CurrentControlSet, Servizi, Netlogon, Parametri, DCAllowedNTLMServers|
|Sicurezza di rete: limitazione di NTLM: controlla autenticazione NTLM nel dominio|MACHINE , System , CurrentControlSet , Servizi , Netlogon , Parametri , AuditNTLMInDomain|

> [!NOTE]
> Per ulteriori informazioni sulle impostazioni del Registro di sistema supportate da varie versioni del sistema operativo, fare riferimento al foglio di [calcolo di riferimento Impostazioni Criteri di gruppo](https://www.microsoft.com/download/confirmation.aspx?id=25250).

*Per configurare FIM per il monitoraggio delle linee di base del Registro di sistema:*

1. Nella finestra **Aggiungi Registro di sistema** di Windows per il rilevamento delle modifiche, nella casella di testo Chiave del Registro di sistema di **Windows,** immettere la chiave del Registro di sistema.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Abilitare FIM in un Registro di sistemaEnable FIM on a registry](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Rilevamento delle modifiche apportate ai file di Windows

1. Nella finestra Aggiungi file di Windows per il **rilevamento delle** modifiche immettere nella casella di testo Immettere il **percorso,** immettere la cartella contenente i file di cui si desidera tenere traccia. Nell'esempio riportato nella figura seguente, **Contoso Web App** si trova nella cartella D: all'interno della struttura di cartelle **ContosWebApp.**  
1. Creare una voce di file di Windows personalizzata specificando un nome della classe di impostazione, abilitando la ricorsione e specificando la cartella superiore con un suffisso con caratteri jolly (-).

    ![Abilitare FIM su un file](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Recupero dei dati delle modificheRetrieving change data

I dati di Monitoraggio dell'integrità dei file si trovano all'interno del set di tabelle Azure Log Analytics /ConfigurationChange.  

 1. Impostare un intervallo di tempo per recuperare un riepilogo delle modifiche in base alla risorsa.
Nell'esempio seguente, stiamo recuperando tutte le modifiche negli ultimi quattordici giorni nelle categorie di registro e file:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Per visualizzare i dettagli delle modifiche del Registro di sistema:

    1. Rimuovi **file** dalla clausola **where,** 
    1. Rimuovere la riga di riepilogo e sostituirla con una clausola di ordinamento:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

I report possono essere esportati in formato CSV per l'archiviazione e/o incanalati in un report di Power BI.  

![Dati FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)