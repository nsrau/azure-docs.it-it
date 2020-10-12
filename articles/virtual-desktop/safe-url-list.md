---
title: Elenco di URL safe per desktop virtuali Windows-Azure
description: Elenco di URL che è necessario sbloccare per assicurarsi che la distribuzione del desktop virtuale di Windows funzioni come previsto.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f9f68d3734cd7de83a2ddd376caefa410c619d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291110"
---
# <a name="safe-url-list"></a>Elenco degli URL sicuri

È necessario sbloccare determinati URL in modo che la distribuzione del desktop virtuale di Windows funzioni correttamente. In questo articolo vengono elencati questi URL, in modo che sia possibile stabilire quali sono quelli sicuri.

## <a name="virtual-machines"></a>Macchine virtuali

Le macchine virtuali di Azure create per Desktop virtuale Windows devono avere l'accesso agli URL seguenti:

|Indirizzo|Porta TCP in uscita|Scopo|Tag del servizio|
|---|---|---|---|
|*.wvd.microsoft.com|443|Traffico del servizio|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aggiornamenti dello stack dell'agente e di SXS|AzureCloud|
|*.core.windows.net|443|Traffico dell'agente|AzureCloud|
|*.servicebus.windows.net|443|Traffico dell'agente|AzureCloud|
|gcs.prod.monitoring.core.windows.net|443|Traffico dell'agente|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Attivazione di Windows|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Supporto del portale di Azure|AzureCloud|
| 169.254.169.254 | 80 | [Endpoint del servizio metadati dell'istanza di Azure](../virtual-machines/windows/instance-metadata-service.md) | N/D |
| 168.63.129.16 | 80 | [Monitoraggio dello stato dell'host sessione](../virtual-network/security-overview.md#azure-platform-considerations) | N/D |

>[!IMPORTANT]
>Desktop virtuale Windows supporta ora il tag FQDN. Per altre informazioni, vedere [Usare Firewall di Azure per proteggere le distribuzioni di Desktop virtuale Windows](../firewall/protect-windows-virtual-desktop.md).
>
>Per evitare problemi del servizio, è consigliabile usare tag FQDN o del servizio invece degli URL. Gli URL e i tag elencati corrispondono solo a risorse e siti di Desktop virtuale Windows. Gli URL di altri servizi, come Azure Active Directory, non sono inclusi.

La tabella seguente elenca gli URL facoltativi accessibili alle macchine virtuali di Azure:

|Indirizzo|Porta TCP in uscita|Scopo|Tag del servizio|
|---|---|---|---|
|*.microsoftonline.com|443|Autenticazione a Microsoft Online Services|nessuno|
|*.events.data.microsoft.com|443|Servizio di telemetria|nessuno|
|www.msftconnecttest.com|443|Rileva se il sistema operativo è connesso a Internet|nessuno|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|nessuno|
|login.windows.net|443|Accedere a Microsoft Online Services, Microsoft 365|nessuno|
|*.sfx.ms|443|Aggiornamenti per il software client di OneDrive|nessuno|
|*.digicert.com|443|Verifica della revoca del certificato|nessuno|

>[!NOTE]
>Desktop virtuale di Windows attualmente non dispone di un elenco di intervalli di indirizzi IP che è possibile sbloccare per consentire il traffico di rete. Al momento è supportato solo l'sblocco di URL specifici.
>
>Per un elenco di URL correlati a Office sicuri, inclusi gli URL richiesti per Azure Active Directory, vedere [URL e intervalli di indirizzi IP per office 365](/office365/enterprise/urls-and-ip-address-ranges).
>
>È necessario usare il carattere jolly (*) per gli URL che coinvolgono il traffico del servizio. Se si preferisce non usare * per il traffico correlato ad agenti, ecco come trovare gli URL senza caratteri jolly:
>
>1. Registrare le macchine virtuali nel pool di host di Desktop virtuale Windows.
>2. Aprire il **Visualizzatore eventi**, quindi passare a **Windows Logs**  >  **Application**  >  **Wvd-Agent** e cercare l'ID evento 3701.
>3. Sbloccare gli URL trovati nell'evento con ID 3701. Gli URL nell'evento con ID 3701 sono specifici dell'area. È necessario ripetere il processo di sblocco con gli URL pertinenti per ogni area in cui si vogliono distribuire le macchine virtuali.

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
>L'apertura di questi URL è essenziale per un'esperienza client affidabile. Il blocco dell'accesso a questi URL non è supportato e influirà sulle funzionalità del servizio.
>
>Questi URL corrispondono solo a siti e risorse client. Questo elenco non include gli URL per altri servizi come Azure Active Directory. Gli URL di Azure Active Directory sono disponibili in ID 56 negli [URL e negli intervalli di indirizzi IP di Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).
