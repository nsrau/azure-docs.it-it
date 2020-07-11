---
title: Elenco di URL safe per desktop virtuali Windows-Azure
description: Elenco di URL che è necessario sbloccare per assicurarsi che la distribuzione del desktop virtuale di Windows funzioni come previsto.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9b1bdfc326ff217e68785d823b4af046af3241b7
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225180"
---
# <a name="safe-url-list"></a>Elenco URL sicuro

È necessario sbloccare determinati URL in modo che la distribuzione del desktop virtuale di Windows funzioni correttamente. In questo articolo vengono elencati questi URL, in modo che sia possibile stabilire quali sono quelli sicuri.

## <a name="virtual-machines"></a>Macchine virtuali

Le macchine virtuali di Azure create per Desktop virtuale Windows devono avere l'accesso agli URL seguenti:

|Indirizzo|Porta TCP in uscita|Scopo|Tag del servizio|
|---|---|---|---|
|*.wvd.microsoft.com|443|Traffico del servizio|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aggiornamenti dello stack dell'agente e di SXS|AzureCloud|
|*.core.windows.net|443|Traffico dell'agente|AzureCloud|
|*.servicebus.windows.net|443|Traffico dell'agente|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Traffico dell'agente|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Attivazione di Windows|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Supporto del portale di Azure|AzureCloud|

>[!IMPORTANT]
>Desktop virtuale Windows supporta ora il tag FQDN. Per altre informazioni, vedere [Usare Firewall di Azure per proteggere le distribuzioni di Desktop virtuale Windows](../firewall/protect-windows-virtual-desktop.md).
>
>Per evitare problemi del servizio, è consigliabile usare tag FQDN o del servizio invece degli URL. Gli URL e i tag elencati corrispondono solo a risorse e siti di Desktop virtuale Windows. Gli URL di altri servizi, come Azure Active Directory, non sono inclusi.

La tabella seguente elenca gli URL facoltativi accessibili alle macchine virtuali di Azure:

|Indirizzo|Porta TCP in uscita|Scopo|Tag del servizio|
|---|---|---|---|
|*.microsoftonline.com|443|Autenticazione a Microsoft Online Services|Nessuno|
|*.events.data.microsoft.com|443|Servizio di telemetria|Nessuno|
|www.msftconnecttest.com|443|Rileva se il sistema operativo è connesso a Internet|Nessuno|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Nessuno|
|login.windows.net|443|Accedere a Microsoft Online Services, Microsoft 365|nessuno|
|*.sfx.ms|443|Aggiornamenti per il software client di OneDrive|nessuno|
|*.digicert.com|443|Verifica della revoca del certificato|Nessuno|

>[!NOTE]
>Desktop virtuale di Windows attualmente non dispone di un elenco di intervalli di indirizzi IP che è possibile sbloccare per consentire il traffico di rete. Al momento è supportato solo l'sblocco di URL specifici.
>
>Per un elenco di URL correlati a Office sicuri, inclusi gli URL richiesti per Azure Active Directory, vedere [URL e intervalli di indirizzi IP per office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>È necessario usare il carattere jolly (*) per gli URL che coinvolgono il traffico del servizio. Se si preferisce non usare * per il traffico correlato ad agenti, ecco come trovare gli URL senza caratteri jolly:
>
>1. Registrare le macchine virtuali nel pool di host di Desktop virtuale Windows.
>2. Aprire il **Visualizzatore eventi**, quindi passare a **Windows Logs**  >  **Application**  >  **Wvd-Agent** e cercare l'ID evento 3702.
>3. Inserire nell'elenco elementi consentiti gli URL trovati nell'evento con ID 3702. Gli URL nell'evento con ID 3702 sono specifici dell'area. È necessario ripetere il processo di sblocco con gli URL pertinenti per ogni area in cui si vogliono distribuire le macchine virtuali.

## <a name="remote-desktop-clients"></a>Client Desktop remoto

Tutti i client Desktop remoto utilizzati devono avere accesso agli URL seguenti:

|Indirizzo|Porta TCP in uscita|Scopo|Client|
|---|---|---|---|
|*.wvd.microsoft.com|443|Traffico del servizio|Tutti|
|*.servicebus.windows.net|443|Dati per la risoluzione dei problemi|Tutti|
|go.microsoft.com|443|FWLink Microsoft|Tutti|
|aka.ms|443|Abbreviazione URL Microsoft|Tutti|
|docs.microsoft.com|443|Documentazione|Tutti|
|privacy.microsoft.com|443|Informativa sulla privacy|Tutti|
|query.prod.cms.rt.microsoft.com|443|Aggiornamenti client|Desktop di Windows|

>[!IMPORTANT]
>L'apertura di questi URL è essenziale per un'esperienza client affidabile. Il blocco dell'accesso a questi URL non è supportato e influirà sulle funzionalità del servizio. Questi URL corrispondono solo a siti e risorse del client e non includono gli URL di altri servizi come Azure Active Directory.