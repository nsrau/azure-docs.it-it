---
title: Versioni di Threat Modeling Tool - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Documentazione delle note sulla versione per Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: bdf8b701567aaa3a0d9006333557bcec4f312723
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586467"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA versione 7.1.50911.2 - 12/09/2018

Siamo lieti di annunciare che Microsoft Threat Modeling Tool è ora disponibile per il download come versione supportata disponibile a livello generale. Questa versione contiene aggiornamenti importanti per la privacy e la sicurezza, oltre a correzioni di bug, aggiornamenti delle funzionalità e miglioramenti per la stabilità. Agli utenti esistenti della versione di anteprima 2017 verrà richiesto di eseguire l'aggiornamento alla versione più recente tramite la tecnologia ClickOnce all'apertura del client. Per i nuovi utenti dello strumento [fare clic qui per scaricare il client](https://aka.ms/threatmodelingtool).

Con questa versione, termina il supporto della versione di anteprima 2017 e si consiglia di tutti gli utenti di questa versione di eseguire l'aggiornamento alla versione disponibile a livello generale. A partire dal 15 ottobre 2018 verrà definita la versione minima di ClickOnce richiesta per Threat Modeling Tool e verrà richiesto a tutti i clienti che usano l'anteprima di eseguire l'aggiornamento.

Microsoft Threat Modeling Tool 2016, disponibile nell'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=49168), rimane supportato fino al 1 ottobre 2019 solo per gli aggiornamenti critici della sicurezza.

## <a name="feature-changes"></a>Modifiche apportate alle funzionalità

### <a name="azure-stencil-updates"></a>Aggiornamenti degli stencil di Azure

Sono stati aggiunti ulteriori stencil di Azure con le minacce e le mitigazioni associate al set di stencil fornito con questa versione. Sono state apportate modifiche significative nelle aree di interesse "Servizi app di Azure", "Offerte di database di Azure" e "Archiviazione di Azure".

![Aggiornamenti degli stencil di Azure](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Rimozione della funzionalità di integrazione di OneDrive

Le funzionalità per salvare in OneDrive, aprire da OneDrive e condividere un collegamento dell'anteprima sono state rimosse. Gli utenti di OneDrive sono invitati a usare il client Microsoft [OneDrive per Windows](https://onedrive.live.com/about/en-us/download/) per accedere ai file archiviati in OneDrive tramite le finestre di dialogo standard per aprire e salvare i file.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Principali correzioni di bug segnalati dai clienti

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>Nell'anteprima di Threat Modeling Tool, lo strumento si blocca quando si usa il modello standard

- Può verificarsi un arresto anomalo dello strumento quando si aggiunge uno stencil generico, ad esempio "Generic Data Flow" (Flusso di dati generico), all'area di disegno e lo stencil genera minacce. Il problema è stato risolto.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>Nell'anteprima di Threat Modeling Tool, quando si salva un report o si copiano le minacce, i livelli di rischio non sono corretti

- Se un utente modifica il livello di rischio di minacce specifiche e quindi salva un report o copia i rischi, potrebbe essere ripristinato il livelli di rischio alto. Il problema è stato risolto.

## <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Gli utenti di schermi ad alta risoluzione potrebbero riscontrare testo di piccole dimensioni nelle proprietà della minaccia

#### <a name="issue"></a>Problema

Nella visualizzazione di analisi dello strumento, se l'utente usa uno schermo ad alta risoluzione configurato per impostazione predefinita per ingrandire per migliorare la leggibilità in Windows, la sezione "Possibili Mitigation(s)" (Mitigazioni possibili) di una minaccia potrebbe essere visualizzata con testo di piccole dimensioni.

![Problema noto con gli schermi ad alta risoluzione](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Soluzione alternativa

L'utente può fare clic sul testo della mitigazione e usare il controllo zoom di Windows standard (CTRL+rotellina del mouse verso l'alto) per aumentare l'ingrandimento della sezione.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Possibile errore di apertura per i file nella sezione "Recently Opened Models" (Modelli aperti di recente) della finestra principale

#### <a name="issue"></a>Problema

La funzionalità "Open From OneDrive" (Apri da OneDrive) della versione di anteprima è stata rimossa. Gli utenti con "Recently Opened Models" (Modelli aperti di recente) salvati in OneDrive riceveranno l'errore seguente.

![Funzionalità di OneDrive rimossa](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Soluzione alternativa

Gli utenti di OneDrive sono invitati a usare il client Microsoft [OneDrive per Windows](https://onedrive.live.com/about/en-us/download/) per accedere ai file archiviati in OneDrive tramite la finestra di dialogo standard "Open a model" (Apri modello).

![Funzionalità di OneDrive rimossa](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>L'organizzazione usa la versione 2016 dello strumento, è possibile usare il set di stencil di Azure?

Sì. Il [set di stencil di Azure è disponibile in GitHub](https://github.com/Microsoft/threat-modeling-templates/) e può essere caricato nella versione 2016 dello strumento. Per creare un nuovo modello con il set di stencil di Azure, usare la finestra di dialogo "Template For New Models" (Modello per nuovi modelli) nella schermata del menu principale. Threat Modeling Tool 2016 non esegue il rendering dei collegamenti disponibili nei campi "Possible Mitigations" (Mitigazioni possibili) del set di stencil di Azure, pertanto i collegamenti potrebbero essere visualizzati come tag HTML.

![Aggiornamenti degli stencil di Azure nella versione 2016 del client](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - Microsoft Windows 10
- Versione .NET richiesta
  - .NET 3.5.2
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e include le informazioni [sull'uso dello strumento](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
