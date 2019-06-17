---
title: Confrontare le linee di base con monitoraggio dell'integrità dei File nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come confrontare le linee di base con monitoraggio dell'integrità dei File nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358439"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Confrontare le linee di base usando il monitoraggio dell'integrità dei File (FIM)

Monitoraggio dell'integrità dei file (FIM) informa l'utente quando vengono apportate alle aree sensibili nelle risorse, in modo da poter analizzare e risolvere l'attività non autorizzate. FIM consente di monitorare i file di Windows, i registri di Windows e i file di Linux.

Questo argomento illustra come abilitare FIM per i file e registri di sistema. Per altre informazioni su FIM, vedere [monitoraggio dell'integrità dei File nel Centro sicurezza Azure](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Perché usare FIM?

Sistema operativo, applicazioni e configurazioni associate controllano lo stato di comportamento e la sicurezza delle risorse. Di conseguenza, gli utenti malintenzionati attaccano i file che controllano le risorse, per poter sorpassare del sistema operativo di una risorsa e/o eseguire attività senza essere rilevati.

In realtà, molti standard di conformità alle normative quali PCI-DSS e ISO 17799 richiedono l'implementazione di controlli FIM.  

## <a name="enable-built-in-recursive-registry-checks"></a>Abilita i controlli del Registro di sistema predefinite ricorsiva

Le impostazioni predefinite hive del Registro di sistema FIM forniscono un modo pratico per monitorare le modifiche ricorsivo all'interno di aree di sicurezza comuni.  Ad esempio, un utente malintenzionato può configurare uno script da eseguire nel contesto LOCAL_SYSTEM configurando un'esecuzione all'avvio o arresto.  Per monitorare le modifiche di questo tipo, abilitare il controllo predefinito.  

![Registro](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Verifica ricorsiva si applica solo a hive di sicurezza consigliate e non ai percorsi del Registro di sistema personalizzato.  

## <a name="adding-a-custom-registry-check"></a>Aggiunta di un controllo personalizzato del Registro di sistema

Linee di base di FIM per iniziare, che identifica le caratteristiche di uno stato noto per il sistema operativo e il supporto dell'applicazione.  Per questo esempio, esamineremo le configurazioni di criteri password per Windows Server 2008 e versioni successive.


|Nome criteri                 | Impostazione del Registro di sistema|
|---------------------------------------|-------------|
|Controller di dominio: Rifiuta password di account computer| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|Membro del dominio: Firma digitale crittografare o firmare i dati di canale sicuro (sempre)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|Membro del dominio: Firma digitale crittografare i dati del canale sicuro (quando possibile)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|Membro del dominio: Firma digitale ai protetto i dati del canale (quando possibile)|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|Membro del dominio: Disabilita cambi password account computer|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|Membro del dominio: Validità password account computer massimo|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|Membro del dominio: Richiedi chiave di sessione avanzata (Windows 2000 o versioni successive)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|Sicurezza di rete: Limitazione di NTLM:  Autenticazione NTLM nel dominio|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|Sicurezza di rete: Limitazione di NTLM: Aggiungi eccezioni dei server in questo dominio|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|Sicurezza di rete: Limitazione di NTLM: Autenticazione NTLM nel dominio di controllo|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Per altre informazioni sulle impostazioni del Registro di sistema supportate dalle varie versioni del sistema operativo, vedere la [impostazioni di criteri di gruppo fare riferimento a fogli di calcolo](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Per configurare FIM per il monitoraggio delle linee di base del Registro di sistema:*

1. Nel **Aggiungi Registro di sistema Windows per rilevamento modifiche** finestra, nelle **chiave del Registro di sistema Windows** testo casella, immettere la chiave del Registro di sistema.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Abilitare FIM in un registro di sistema](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Rilevamento delle modifiche ai file di Windows

1. Nel **Aggiungi File Windows per rilevamento modifiche** finestra, nelle **immettere il percorso** testo casella, immettere la cartella che contiene i file che si vuole tenere traccia. Nell'esempio nella figura riportata di seguito **App Web di Contoso** risiede nel D:\ unità all'interno di **ContosWebApp** struttura di cartelle.  
1. Creare una voce di file di Windows personalizzata fornendo un nome della classe di impostazione, l'abilitazione di ricorsione e specificando la cartella principale con un suffisso con caratteri jolly (*).

    ![Abilitare FIM in un file](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Il recupero dei dati delle modifiche

Monitoraggio dell'integrità dei dati si trovano all'interno di Analitica di Log di Azure di file / tabella ConfigurationChange set.  

 1. Impostare un intervallo di tempo per recuperare un riepilogo delle modifiche dalla risorsa.
Nell'esempio seguente vengono recuperati tutte le modifiche apportate negli ultimi giorni quattordici nelle categorie dei file e Registro di sistema:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Per visualizzare i dettagli delle modifiche del Registro di sistema:

    1. Rimuovere **file** dalle **in cui** clausola, 
    1. Rimuovere la riga di riepilogo e sostituirlo con una clausola di ordinamento:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

I report possono essere esportati in un file CSV per l'archiviazione e/o indirizzate a un report di Power BI.  

![Dati FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)