---
title: Convalida funzionale di un'offerta AppSource Dynamics 365 Finance and Operations in Azure Marketplace.
description: Come convalidare il funzionamento di un'offerta di Dynamics 365 Finance e Operations in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: b9685081c0beacd745a83067b9d9876384933377
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131242"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>Convalida funzionale AppSource Dynamics 365 Finance and Operations

Per completare una prima pubblicazione nel [centro](https://partner.microsoft.com/dashboard/home)per i partner, le offerte per Dynamics 365 Finance e Operations richiedono due convalide funzionali:

- Caricare un video dimostrativo dell'ambiente Dynamics 365 che mostra le funzionalità di base.
- Sono presenti schermate che illustrano l'ambiente di [Servizi ciclo](https://lcs.dynamics.com/) di vita (LCS) della soluzione.

> [!NOTE]
> Le successive pubblicazioni di ricertificazione non richiedono la dimostrazione. Per altre informazioni, vedere il [documento dei criteri di AppSource](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops).

## <a name="how-to-validate"></a>Come convalidare

Per la convalida funzionale sono disponibili due opzioni:

- Per dimostrare e registrare l'ambiente e la soluzione [LCS](https://lcs.dynamics.com/) , è possibile usare una chiamata di conferenza di 30 minuti con l'ora solare Pacifico (pst).
- Nel centro per i partner passare a [Commercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)  >  **Overview** e caricare un URL video dimostrativo e le schermate LCS nella scheda contenuto supplementare dell'offerta.

Il team di certificazione Microsoft esamina il video e i file, quindi approva la soluzione o invia messaggi di posta elettronica sui passaggi successivi.

### <a name="option-1-30-minute-conference-call"></a>Opzione chiamata Conference di 1:30 minuti

Per pianificare una chiamata di revisione finale, contattare [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) il nome dell'offerta e alcuni possibili slot temporali tra le 8.00 e le 17.00 del Pacifico.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>Opzione 2: caricare un video dimostrativo e le schermate LCS

1. Registrare un video e caricare l'indirizzo nel sito di hosting scelto. Attenersi alle seguenti indicazioni:

    - Visualizzabile dal team di certificazione Microsoft.
    - Lunghezza inferiore a 20 minuti.
    - Include fino a tre funzionalità principali principali della soluzione nell'ambiente Dynamics 365.

    > [!NOTE]
    > È accettabile usare un video di marketing esistente se soddisfa le linee guida.

2. Eseguire le schermate seguenti dell'ambiente [LCS](https://lcs.dynamics.com/) che corrispondono all'offerta o alla soluzione che si vuole pubblicare. Devono essere sufficientemente chiare da consentire al team di certificazione di leggere il testo. Salvare le schermate come file JPG. È possibile fornire [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) l'autorizzazione per l'ambiente LCS, in modo che sia possibile verificare la configurazione invece di fornire schermate.

    1. Passare alla libreria del progetto **LCS**  >  **Business Process Modeler**  >  **Project library** . Eseguire screenshot di tutti i passaggi del processo. Includere i **diagrammi** e le colonne **rivedute** , come illustrato di seguito:

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="Mostra la finestra libreria del progetto.":::

      2. Passare a **LCS**  >  **soluzione Gestione**  >  **test pacchetto soluzione** . Eseguire screenshot che includono la panoramica e il contenuto dei pacchetti illustrati negli esempi seguenti:

    | Campo | Immagine <img src="" width="400px">|
    | --- | --- |
    | Panoramica del pacchetto | [![Screenshot che mostra la finestra "Panoramica dei pacchetti".](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>Responsabili approvazione soluzioni</li></ul> | [![Schermata di panoramica del pacchetto](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | Contenuto del pacchetto<ul><li>Modello</li><li>Pacchetto distribuibile software</li></ul> | [![Schermata contenuto pacchetto uno](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>Configurazione di GER</li><li>Backup di database</li></ul><br>Gli artefatti non sono necessari nella sezione di **configurazione GER** . | [![Schermata contenuto pacchetto due](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Modello di report Power BI</li><li>Elemento BPM</li></ul><br>Gli artefatti non sono necessari nella sezione **Power bi** . | [![Schermata contenuto pacchetto tre](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>Elabora pacchetto dati</li><li>Contratto di licenza della soluzione e informativa sulla privacy</li></ul><br>Le sezioni **configurazione GER** e **modello di report Power bi** sono facoltative da includere per le offerte per Finanza e operazioni. | [![Schermata del contenuto del pacchetto quattro](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    Per ulteriori informazioni su ogni sezione del portale di LCS, vedere il [manuale dell'utente di LCS](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide).

3. Caricare nel centro per i partner.

    1. Creare un documento di testo che includa le schermate e l'indirizzo video demo oppure salvare le schermate come file JPG distinti.
    2. Aggiungere il testo e gli eventuali file JPG in un file con estensione zip nel [Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) nel centro per i partner nella scheda **contenuto supplementare** dell'offerta Finance and Operations.

    [![Mostra la finestra libreria del progetto](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla creazione di un'offerta, vedere: [creare un'offerta di Dynamics 365 per le operazioni](./partner-center-portal/create-new-operations-offer.md).