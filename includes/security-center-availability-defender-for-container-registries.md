---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 30de9181fd23a29b28973d01899f0b23fca3ae89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014577"
---
## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|**Azure Defender per i registri contenitori** viene fatturato come illustrato nella [pagina dei prezzi](../articles/security-center/security-center-pricing.md)|
|Immagini e registri supportati:|Immagini Linux in registri di Registro Azure Container accessibili da Internet pubblico con accesso alla shell|
|Immagini e registri non supportati:|Immagini di Windows<br>Registri privati<br>Registri con accesso limitato con un firewall, endpoint di servizio o endpoint privati come Collegamento privato di Azure<br>Immagine super minimaliste, come immagini [Docker scratch](https://hub.docker.com/_/scratch/), o immagini "Distroless" che contengono solo un'applicazione e le relative dipendenze di runtime senza strumento di gestione pacchetti, shell o sistema operativo|
|Autorizzazioni e ruoli obbligatori:|**Ruolo con autorizzazioni di lettura per la sicurezza** e [ruoli e autorizzazioni di Registro Azure Container](../articles/container-registry/container-registry-roles.md)|
|Cloud:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Cloud commerciali<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov - Al momento è supportata solo la funzionalità analisi su push. Per altre informazioni, vedere [Quando vengono analizzate le immagini?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)<br>:::image type="icon" source="../articles/security-center/media/icons/no-icon.png" border="false"::: Governo cinese, altri governi|
|||